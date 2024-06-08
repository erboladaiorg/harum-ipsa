# SUBSCRIBER
[![Subscriber](https://github.com/erboladaiorg/harum-ipsa/actions/workflows/github-workflows.yml/badge.svg)](https://github.com/erboladaiorg/harum-ipsa/actions/workflows/github-workflows.yml)

size: 1.3Kb (gzipped)

This tiny library let's you coordinate and dispatch events using channels, namespaces and events. 

You create the subscriber once. And add subscriptions to it. You dispatch from the subscriber passing first channel name, event name and an optional data:
```js
 const 
    subscriber = new Subscriber(),
    subscription = subscriber.subscribe("my-channel");
subscription.on("my-event@my-namespace", () => console.log("Hello World!"));
subscriber.dispatch("my-channel", "my-event"); // Hello World!
```

You can create multiple channels:

```js
const subscription$1 = subscriber.subscribe("my-channel");
const subscription$2 = subscriber.subscribe("my-channel");
const subscription$3 = subscriber.subscribe("my-other-channel");
```

Add events to subscriptions using event name, only namespace or both. There must be `@` between event and namespace. Event comes first:

```js
subscription$1.on("my-event", () => void(0))
subscription$1.on("@my-namespace", () => void(0))
subscription$1.on("my-event@my-namespace", () => void(0))
```

Pass optional data if you want:

```js
const 
    subscriber = new Subscriber(),
    subscription$1 = subscriber.subscribe("my-channel"),
    subscription$2 = subscriber.subscribe("my-channel");
subscription$1.on("my-event@my-namespace", (data) => data.firstname);
subscription$1.on("my-other-event@my-namespace", (data) => data.lastname);
subscriber.dispatchReturn("my-channel", "@my-namespace", {firstname: "john", lastname: "doe"})
/*
    [
        {channel: "my-channel", subscription: subscription$1, namespace: "my-namespace", event: "my-event", value: "john"},
        {channel: "my-channel", subscription: subscription$2, namespace: "my-namespace", event: "my-other-event", value: "doe"}
    ]
*/
```
When creating subscriptions, you can pass an optional garbage collection function argument (by default it is `() => false`, so never removed), which if returns truthy, than that subscriptions will be destroyed and will no longer respond to dispatches. 

```js
const subscription$1 = subscriber.subscribe("my-channel", () => !someElement.parentNode) //if someElement is detached from the documentElement, this subscriber wil be removed and won't respond to dispatch calls. Eventually it should be garbage collected.
```

## Methods

| Method                          | Example                    | Explanation                      |
|---------------------------------|----------------------------|----------------------------------|
| `subscriber.clear()`    |  | Clears all subscriptions in the channel's `Set` |
| `subscriber.off(channelstr)`    | `subscriber.off("my-channel")` | Removes the channel, recreated automatically if needed later |
| `subscriber.removeChannel(channelstr)`    | `subscriber.off("my-channel")` | Alias for `subscriber.off` |
| `subscriber.dispatch(channelstr, eventstr [,data])`    | `subscriber.dispatch("my-channel", "my-event", {hello: "world"})` | Dispatches events in the channel |
| `subscriber.dispatchReturn(channelstr, eventstr [,data])`    | `subscriber.dispatchReturn("my-channel", "my-event", {hello: "world"})` | Dispatches events but instead of returning the subscriber, it returns the callback results as an array with the signature `{channel, subscription, namespace, event, value}` |
| `subscriber.subscribe(channelstr[,gc])`    | `subscriber.subscribe("my-channel")` | Creates a subscription to a channel |
| `subscription.destroy()`    | `subscription.destroy()` | Removes the subscription from internal channel `Set`. Dispatches no longer trigger this subscription. |
| `subscription.on(eventstr, callback)`    | `subscription.on("my-event@my-namespace", () => "hi")` | Adds a listener to the subscription |
| `subscription.addEventListener(eventstr, callback)`    | `subscription.addEventListener("my-event@my-namespace", () => "hi")` | Alias for `subscription.on` |
| `subscription.off(eventstr[,callback])`    | `subscription.off("@my-namespace")` | Removes an eventlistener. The `eventstr` can be `my-event` or `@my-namespace` or `my-event@my-namespace`. If eventstr is a `function`, then it will be removed from all namespaces and events including that function for that subscription |
| `subscription.removeEventListener(eventstr[,callback])`    | `subscription.removeEventListener("@my-namespace")` | Alias for `subscription.off` |
| `subscription.ondispatch = callback`    | `subscription.ondispatch = () => console.log("dispatched is called from subscriber!")` | A callback that is triggered whenever the subscriper dispatches this subscription. Only called if `ondispatch` is set to a `function` |



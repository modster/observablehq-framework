# BTC

```js
const wssUrl = "wss://ws.eodhistoricaldata.com/ws/crypto?api_token=demo";
const socket = new WebSocket(wssUrl);
invalidation.then(() => socket.close());
socket.addEventListener("open", () => socket.send(JSON.stringify({action: "subscribe", symbols: "BTC-USD"})));

const messages = Generators.observe((change) => {

  const messages = [];
  const duration = messages.duration = 60_000;

  const messaged = (event) => {
    const m = JSON.parse(event.data);
    const t = m.t;
    if (t == null) return;
    while ((t - messages[0]?.t) > duration) messages.shift();
    messages.push(m);
    change(messages);
  };

  socket.addEventListener("message", messaged);
  return () => socket.removeEventListener("message", messaged);

});
```

## 60 seconds

```js
Plot.plot({
  marginLeft: 50,
  x: {type: "time", domain: [now - messages.duration, now]},
  y: {type: "linear", label: "price", inset: 10},
  marks: [Plot.lineY(messages, {x: "t", y: "p", curve: "step", clip: true})]
})
```
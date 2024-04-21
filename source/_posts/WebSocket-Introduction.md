---
title: WebSocket Introduction
date: 2023-10-17 14:03:49
excerpt: Introduction on concept of WebSocket
tags: [Learning Log, WebSocket]
categories:
  - Daily Logs
  - Learning Logs
---

# Introduction of WebSocket

WebSockets are a fascinating topic, especially when it comes to real-time applications like stock price updates.

### What is WebSocket?

WebSocket is a communication protocol that provides full-duplex (two-way) communication channels over a single, long-lived connection. It is designed to be implemented in web browsers and web servers, but it can be used by any client or server application. The WebSocket protocol makes it possible to establish a persistent, low-latency connection between the client (like a web browser) and the server.

### How is WebSocket different from HTTP?

1. **Persistent Connection**: Unlike HTTP where each request opens a new connection, a WebSocket connection is established once and remains open, allowing for real-time data transfer.
2. **Full Duplex**: WebSocket is bidirectional, meaning both the server and client can send messages to each other independently.
3. **Low Latency**: Since the connection is persistent, there's no need to reestablish it, which can reduce data transfer delays.

### Why is WebSocket suitable for real-time data like stock prices?

1. **Real-time updates**: With WebSocket, the server can push updates to the client as soon as new data is available, without the client having to request it.
2. **Efficiency**: Reduces the overhead and latency associated with opening and closing multiple HTTP connections.
3. **Reduced Network Traffic**: Instead of repeatedly polling an API over HTTP, which involves sending headers and establishing connections, you can receive data as it's updated, minimizing network traffic.

### How to use WebSocket for stock price updates?

1. **Establishing a Connection**: The client requests a WebSocket connection by using the "ws" (or "wss" for secure WebSocket) URI scheme. For example: `ws://example.com/socket`.
2. **Handling Messages**: Once connected, both the server and client can send and receive messages. In the context of stock prices, the server would push price updates to the client as they become available.
3. **Closing the Connection**: Either the client or server can initiate closing the WebSocket connection.

### Example in JavaScript:

Here's a simple example of how you might use WebSockets in JavaScript to get stock price updates:

```jsx
// Create a connection to the server
let socket = new WebSocket("ws://example.com/stock-price");

// Connection established
socket.onopen = function (event) {
  console.log("Connected to the server");
};

// Handle incoming messages (like stock price updates)
socket.onmessage = function (event) {
  let stockData = JSON.parse(event.data);
  console.log("Received stock price:", stockData.price);
};

// Connection closed
socket.onclose = function (event) {
  if (event.wasClean) {
    console.log(
      `Connection closed cleanly, code=${event.code}, reason=${event.reason}`
    );
  } else {
    console.log("Connection died");
  }
};

// Error handling
socket.onerror = function (error) {
  console.error("WebSocket Error:", error);
};
```

### Considerations:

1. **Server Support**: The server needs to support the WebSocket protocol. Not all APIs will offer WebSocket endpoints, so you'll need to choose or build a backend that does.
2. **Fallbacks**: Not all networks and proxies support WebSockets. You might need a fallback mechanism like long polling for these scenarios.
3. **Security**: Just like HTTP has HTTPS, WebSocket has WSS (WebSocket Secure) which encrypts the data.

In the context of stock prices, using WebSockets can provide a significant advantage in terms of real-time data delivery with reduced latency. If the stock trading platform or data provider offers a WebSocket API, it would be a great choice for your application.

# Postman on WebSocket

You can use Postman to connect to a WebSocket! Postman is widely known for its capabilities with HTTP requests, but as of version 8.0, Postman added support for WebSocket testing. This makes it a handy tool for testing and exploring WebSocket services as well.

Here's how you can use Postman to connect to a WebSocket:

1. **Open Postman**: Start the Postman application.
2. **Select WebSocket**: In the top-left corner where you would typically select an HTTP method like GET or POST, you'll find an option for `WS` (WebSocket). Select that.
3. **Enter WebSocket URL**: In the request URL field, enter your WebSocket URL. WebSocket URLs typically start with `ws://` (for unencrypted) or `wss://` (for encrypted, similar to HTTPS). For example: `ws://example.com/socket`.
4. **Connect**: Click on the "Connect" button. Postman will try to establish a connection to the WebSocket server.
5. **Send & Receive Messages**: Once connected, you can send messages to the server and view the messages received from the server in real-time.
6. **Close the Connection**: You can manually disconnect from the WebSocket server by clicking the "Disconnect" button.
7. **Additional Options**: Postman also provides options for setting custom headers, viewing connection details, and more.

Remember that while Postman provides a convenient interface for working with WebSockets, it's primarily for testing and exploration. When you're building an actual application, you'll be implementing WebSocket logic in your chosen programming language and framework.

Happy testing!

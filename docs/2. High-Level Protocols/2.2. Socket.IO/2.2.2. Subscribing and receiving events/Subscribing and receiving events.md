#Subscribing and receiving events
You can subscribe to predefined and custom events. Predefined events are "connect", "connecting", "event", "disconnect", "reconnect", "reconnecting", "reconnect_attempt", "reconnect_failed", "error". Custom events are programmer defined events that your server will send to your client. You can subscribe to an event by calling a socket’s On function:

```csharp
manager.Socket.On("login", OnLogin);
manager.Socket.On("new message", OnNewMessage);
```

An event handler will look like this:

```csharp
	void OnLogin(Socket socket, Packet packet, params object[] args)
	{
	}
```


- The *socket* parameter will be the namespace-socket object that the server sent this event.
- The *packet* parameter contains the internal packet data of the event. The packet can be used to access binary data sent by the server, or to use a custom Json parser lib to decode the payload data. More on these later.
- The *args* parameter is a variable length array that contains the decoded objects from the packet’s payload data. With the default Json encoder these parameters can be ‘primitive’ types(`int, double, string`) or list of objects(`List<object>`) or Dictionary`<string, object>` for objects.

A message emitted on the server(node.js):

```csharp
// send a message to the client
socket.emit('message', ‘MyNick’, ‘Msg to the client’);
```

can be caught by the client:

```csharp
// subscribe to the "message" event
manager.Socket.On("message", OnMessage);

// event handler
void OnMessage(Socket socket, Packet packet, params object[] args)
{
	// args[0] is the nick of the sender
// args[1] is the message
	Debug.Log(string.Format("Message from {0}: {1}", args[0], args[1]));
}
```

##Predefined events

- "connect": Sent when the namespace opens.
- "connecting": Sent when the SocketManager start to connect to the socket.io server.
- "event": Sent on custom (programmer defined) events.
- "disconnect": Sent when the transport disconnects, SocketManager is closed, Socket is closed or when no Pong message received from the server in the given time specified in the handshake data.
- "reconnect": Sent when the plugin successfully reconnected to the socket.io server.
- "reconnecting": Sent when the plugin will try to reconnect to the socket.io server.
- "reconnect_attempt": Sent when the plugin will try to reconnect to the socket.io server.
- "reconnect_failed": Sent when a reconnect attempt fails to connect to the server and the ReconnectAttempt reaches the options’ ReconnectionAttempts’ value.
- "error": Sent on server or internal plugin errors. The event’s only argument will be a BestHTTP.SocketIO.Error object.

##Other event-related functions:

- Once: you can subscribe to an event that will be called only once.

```csharp
// The event handler will be called only once
manager.Socket.Once("connect", OnConnected);
```

- Off: you can remove all event subscription, or just only one.

```csharp
// Removes all event-handlers
manager.Socket.Off();

// Removes event-handlers from the "connect" event
manager.Socket.Off("connect");


// Removes the OnConnected event-handler from the "connect" event
manager.Socket.Off("connect", OnConnected);
```
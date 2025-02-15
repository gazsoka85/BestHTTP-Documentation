#Sending events
You can send an event with the Emit function. You have to pass the event name as the first parameter and optionally other parameters. These will be encoded to json and will be sent to the server. Optionally you can set a callback function that will be called when the server processes the event(you have to set up the server code properly to be able to send back a callback function. See the Socket.IO server side documentation for more information).

```csharp
// Send a custom event to the server with two parameters
manager.Socket.Emit("message", "userName", "message");

// Send an event and define a callback function that will be called as an
// acknowledgement of this event
manager.Socket.Emit("custom event", OnAckCallback, "param 1", "param 2");

void OnAckCallback(Socket socket, Packet originalPacket, params object[] args)
{
Debug.Log("OnAckCallback!");
}
```

##Sending acknowledgement to the server
You can send back an acknowledgement to the server by calling the socket’s EmitAck function. You have to pass the original packet and any optional data:

```csharp
manager["/customNamespace"].On("customEvent", (socket, packet, args) =>
{
	socket.EmitAck(packet, "Event", "Received", "Successfully");
});
```

*You can keep a reference to the packet, and call the EmitAck from somewhere else.*

##Sending binary data
There are two ways of sending binary(byte[]) data.

- By passing to the Emit function the plugin will scan the parameters and if it founds one, it will convert it to a binary attachment(as introduced in Socket.IO 1.0). This is the most efficient way, because it will not convert the byte array to a Base64 encoded string on client side, and back to binary on server side.

```csharp
byte[] data = new byte[10];
//...
manager.Socket.Emit("eventWithBinary", "textual param", data);
```

- If the binary data is embedded in an object as a field or property the Json encoder must support the conversion. The default Json encoder can’t convert the embedded binary data to Json, you have to use a more advanced Json parser library (like "JSON .NET For Unity" - <http://u3d.as/5q2>)

##Receiving binary data
In the Socket.IO server when binary data sent to the client it will replace the data with a Json object({'_placeholder':true,'num':xyz}) and will send the binary data in another packet. On client side these packets will be collected and will be merged into one packet. The binary data will be in the packet’s Attachments property.
Here you will have some options too to use this packet:

- In your event-handler you can access all binary data through the packet’s Attachments property.

```csharp
Socket.On("frame", OnFrame);

void OnFrame(Socket socket, Packet packet, params object[] args)
{
	texture.LoadImage(packet.Attachments[0]);
}
```

- The second option is almost the same as the previous, with a little improvement: we will not decode the sent Json string to c# objects. We can do it because we know that the server sent only the binary data, no other information came with this event. So we will let the plugin know that do not decode the payload:

```csharp
// Subscribe to the "frame" event, and set the autoDecodePayload flag to false
Socket.On("frame", OnFrame, /*autoDecodePayload:*/ false);

void OnFrame(Socket socket, Packet packet, params object[] args)
{
// Use the Attachments property as before
texture.LoadImage(packet.Attachments[0]);
}
```

The `autoDecodePayload` parameter is `true` by default.

- We can replace back the "{'_placeholder':true,'num':xyz}" string to the index of the attachment in the Attachments list.

```csharp
Socket.On("frame", OnFrame, /*autoDecodePayload:*/ false);

void OnFrame(Socket socket, Packet packet, params object[] args)
{
	// Replace the Json object with the index
	packet.ReconstructAttachmentAsIndex();

	// now, decode the Payload to an object[]
	args = packet.Decode(socket.Manager.Encoder);

	// args now contains only an index number (probably 0)
	byte[] data = packet.Attachments[Convert.ToInt32(args[0])];

	texture.LoadImage(data);
}
```

- We can replace the "{'_placeholder':true,'num':xyz}" string with the binary data from the Attachments converted to a Base64 encoded string. Advanced Json parsers can convert it to byte arrays when they have to set it to an object’s field or property.

```csharp
Socket.On("frame", OnFrame, /*autoDecodePayload:*/ false);

void OnFrame(Socket socket, Packet packet, params object[] args)
{
	// Replace the Json object with the Base64 encoded string
	packet.ReconstructAttachmentAsBase64();

	// now, decode the Payload to an object[]
	args = packet.Decode(socket.Manager.Encoder);

	// args now contains a Base64 encoded string
	byte[] data = Convert.FromBase64String(args[0] as string);

	texture.LoadImage(data);
}
```
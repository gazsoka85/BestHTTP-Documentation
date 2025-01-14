#Advanced WebSocket

- **Ping** messages: It’s possible to start a new thread to send `Ping` messages to the server by setting the `StartPingThread` property to `true` before we receive an `OnOpen` event. This way Ping messages will be sent periodically to the server. The delay between two ping can be set in the `PingFrequency` property (it’s default is 1000ms).

- **Pong** messages: All ping messages received from the server the plugin will automatically generate a `Pong` answer.

- **Streaming**: Longer text or binary messages will get fragmented. These fragments are assembled by the plugin automatically by default. This mechanism can be overwritten if we register an event handler to the WebSocket’s `OnIncompleteFrame` event. This event called every time the client receives an incomplete fragment. These fragments will be ignored by the plugin, it doesn’t try to assemble these nor store them. This event can be used to achieve streaming experience. 

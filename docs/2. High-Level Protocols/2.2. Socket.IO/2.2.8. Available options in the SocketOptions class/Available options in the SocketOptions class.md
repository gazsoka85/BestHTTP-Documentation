#Available options in the SocketOptions class
You can pass a SocketOptions instance to the SocketManager’s constructor. You can change the following options:

- **Reconnection**: Whether to reconnect automatically after a disconnect. Its default value is true.
- **ReconnectionAttempts**: Number of attempts before giving up. Its default value is Int.MaxValue.
- **ReconnectionDelay**: How long to initially wait before attempting a new reconnection. Affected by +/- RandomizationFactor. For example the default initial delay will be between 500ms to 1500ms. Its default value is 1000ms.
- **ReconnectionDelayMax**: Maximum amount of time to wait between reconnections. Each attempt increases the reconnection delay along with a randomization as above. Its default value is 5000ms.
- **RandomizationFactor**: It can be used to control the ReconnectionDelay range. Its default value is 0.5 and can be set between the 0..1 values inclusive.
- **Timeout**: Connection timeout before a "connect_error" and "connect_timeout" events are emitted. It’s not the underlying tcp socket’s connection timeout, it’s for the socket.io protocol. Its default value is is 20000ms.
- **AutoConnect**: By setting this false, you have to call SocketManager's Open() whenever you decide it's appropriate.
- **ConnectWith**: The SocketManager will try to connect with the transport set to this property. It can be TransportTypes.Polling or TransportTypes.WebSocket.

When you create a new SocketOptions object its properties are set to theirs default values.

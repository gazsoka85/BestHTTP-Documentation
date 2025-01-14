#Writing a custom Json encoder
If you want to change the default Json encoder for various reasons, first you have to write a new one. To do so, you have to write a new class that implements the `IJsonEncoder` from the `BestHTTP.SocketIO.JsonEncoders` namespace.
The stripped IJsonEncoder is very tiny, you have to implement only two functions:

```csharp
public interface IJsonEncoder
{
List<object> Decode(string json);
string Encode(List<object> obj);
}
```

The Decode function must decode the given json string to a list of objects. Because of the nature of the Socket.IO protocol, the sent json is an array and the first element is the event’s name.

The Encode function is used to encode the data that the client wants to send to the server. The structure of this list is the same as with the Decode: the first element of the list is the event’s name, and any other elements are the user sent arguments.

And here comes a complete example using the LitJson library from the examples folder:

```csharp
using LitJson;
public sealed class LitJsonEncoder : IJsonEncoder
{
public List<object> Decode(string json)
{
JsonReader reader = new JsonReader(json);
return JsonMapper.ToObject<List<object>>(reader);
}

public string Encode(List<object> obj)
{
JsonWriter writer = new JsonWriter();
JsonMapper.ToJson(obj, writer);
return writer.ToString();
}
}
```

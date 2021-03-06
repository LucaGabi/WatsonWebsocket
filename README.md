![alt tag](https://github.com/jchristn/watsonwebsocket/blob/master/assets/watson.ico)

# Watson Websocket

[![][nuget-img]][nuget]

[nuget]:     https://www.nuget.org/packages/WatsonWebsocket/
[nuget-img]: https://badge.fury.io/nu/Object.svg

A simple C# async websocket server and client for reliable transmission and receipt of data, targeting both .NET Core and .NET Framework. 

## Test App

A test project for both client (```TestClient```) and server (```TestServer```) are included which will help you understand and exercise the class library.

## SSL

SSL is supported in WatsonWebsocket.  The constructors for ```WatsonWsServer``` and ```WatsonWsClient``` accept a ```bool``` indicating whether or not SSL should be enabled.  Since websockets, and as a byproduct WatsonWebsocket, use HTTPS, they rely on certificates within the certificate store of your operating system.  A test certificate is provided in both the ```TestClient``` and ```TestServer``` projects which can be used for testing purposes.  These should NOT be used in production.

For more information on using SSL certificates, please refer to the wiki.

## New in v2.0.2

- Fixed connection bug (thank you @wirmachenbunt)

## Server Example
```
using WatsonWebsocket;

WatsonWsServer server = new WatsonWsServer("[ip]", port, true|false);
server.ClientConnected = ClientConnected;
server.ClientDisconnected = ClientDisconnected;
server.MessageReceived = MessageReceived; 
server.Start();

static async Task<bool> ClientConnected(string ipPort, HttpListenerRequest request) 
{
    Console.WriteLine("Client connected: " + ipPort);
    return true;
}

static async Task ClientDisconnected(string ipPort) 
{
    Console.WriteLine("Client disconnected: " + ipPort);
}

static async Task MessageReceived(string ipPort, byte[] data) 
{ 
    Console.WriteLine("Message received from " + ipPort + ": " + Encoding.UTF8.GetString(data));
}
```

## Client Example
```
using WatsonWebsocket;

WatsonWsClient client = new WatsonWsClient("[server ip]", [server port], true|false);
client.ServerConnected = ServerConnected;
client.ServerDisconnected = ServerDisconnected;
client.MessageReceived = MessageReceived; 
client.Start(); 

static async Task MessageReceived(byte[] data) 
{
    Console.WriteLine("Message from server: " + Encoding.UTF8.GetString(data));
}

static async Task ServerConnected() 
{
    Console.WriteLine("Server connected");
}

static async Task ServerDisconnected() 
{
    Console.WriteLine("Server disconnected");
}
```

## Accessing from Outside Localhost

When you configure WatsonWebsocket to listen on ```127.0.0.1``` or ```localhost```, it will only respond to requests received from within the local machine.

To configure access from other nodes outside of ```localhost```, use the following:

- Specify the exact DNS hostname upon which WatsonWebsocket should listen in the Server constructor. The HOST header on incoming HTTP requests MUST match this value (this is an operating system limitation)
- If you want to listen on more than one hostname or IP address, use ```*``` or ```+```. You MUST run WatsonWebsocket as administrator for this to work (this is an operating system limitation)
- If you want to use a port number less than 1024, you MUST run WatsonWebsocket as administrator (this is an operating system limitation)
- Open a port on your firewall to permit traffic on the TCP port upon which WatsonWebsocket is listening
- You may have to add URL ACLs, i.e. URL bindings, within the operating system using the ```netsh``` command:
  - Check for existing bindings using ```netsh http show urlacl```
  - Add a binding using ```netsh http add urlacl url=http://[hostname]:[port]/ user=everyone listen=yes```
  - Where ```hostname``` and ```port``` are the values you are using in the constructor
  - If you are using SSL, you will need to install the certificate in the certificate store and retrieve the thumbprint
  - Refer to https://github.com/jchristn/WatsonWebserver/wiki/Using-SSL-on-Windows for more information, or if you are using SSL
- If you're still having problems, please do not hesitate to file an issue here, and I will do my best to help and update the documentation.

## Version History

Please refer to CHANGELOG.md for details.

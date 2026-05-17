# 14. Content Negotiation

Content Negotiation is a mechanism that allows the server and client to agree on the format of the data exchanged during an HTTP request and response.

## Key Concepts of Content Negotiation

1. Accept Header

The `Accept` header in the HTTP request is used by the client to inform the server about which media types (formats) it prefers to receive. The server uses this header to determine the most appropriate response format.

```http
GET /api/products HTTP/1.1
Accept: application/json
```

This means the client is requesting the response in JSON format.

> Example: A client may request a response in any supported format by using a wildcard Accept header such as `Accept: */*`.

2. Content-Type Header

The `Content-Type` header in the HTTP response indicates the format in which the server is sending the data.

3. Media Types (MIME Types)

These are identifiers that specify the format of the data. Common media types include:

- `application/json` for JSON (by default)
- `application/xml` for XML (by default)
- `text/plain` for plain text
- `application/octet-stream` for binary data

4. Configuring Formatters

This is typically done in the `Global.asax` file or the `Startup.cs` file (if using OWIN).

```csharp
public static void Register(HttpConfiguration config)
{
    // Enable pretty-printing for XML responses
    var xml = config.Formatters.XmlFormatter;
    xml.Indent = true;

    // Optionally configure the XML formatter to handle namespaces
    xml.UseXmlSerializer = true;

    // Other configurations like routing
    config.MapHttpAttributeRoutes();
}
```

Setting `xml.UseXmlSerializer = true` forces Web API to use `XmlSerializer` instead of `DataContractSerializer` for XML serialization, which provides different serialization options.

## Image

![Content Negotiation diagram](assets/14-content-negotiation/content-negotiation.png)

This image illustrates how the client `Accept` header and the server `Content-Type` response header work together during content negotiation.

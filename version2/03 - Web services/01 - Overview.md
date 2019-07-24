Overview
-------

### Supported protocols

The web services support the following protocols:

- HTTPS

### Authentication and authorization

The web services require an access token (Bearer) to be specified in the HTTP headers when sending requests. A token can be obtained from the OpenID Connect Identity Provider used by the Documaster instance.

### HTTP status codes

The following table lists the HTTP status codes used by the web services.

| Status code                | Definition                                                                                               | Example                                                         |
|:---------------------------|:---------------------------------------------------------------------------------------------------------|:----------------------------------------------------------------|
| 200 OK                     | The request was successful.                                                                              | HTTP/1.1 200 OK                                                 |
| 201 Created                | The request was fulfilled and resulted in a new resource being created.                                  | HTTP/1.1 201 Created                                            |
| 204 No Content             | The request was successful, but there is no content to send in the response payload body.                | HTTP/1.1 204 No Content                                         |
| 400 Bad Request            | The syntax of the request was invalid.                                                                   | HTTP/1.1 400 Bad Request                                        |
| 415 Unsupported Media Type | The payload format requested by the client is not supported by the server.                               | HTTP/1.1 415 Unsupported Media Type                             |
| 401 Unauthorized           | The request was denied due to an invalid or missing access token.                                        | <p>HTTP/1.1 401 Unauthorized</p><p>WWW-Authenticate: Bearer</p> |
| 403 Forbidden              | The request was denied due to the access token having insufficient privileges.                           | HTTP/1.1 403 Forbidden                                          |
| 404 Not Found              | The requested resource does not exist or is not accessible.                                              | HTTP/1.1 404 Not Found                                          |
| 409 Conflict               | Data was updated by another client after the last read by this client. The client may retry the request. | HTTP/1.1 409 Conflict                                           |
| 500 Internal Server Error  | An internal server error has occurred.                                                                   | HTTP/1.1 500 Internal Server Error                              |
| 503 Service Unavailable    | The service is temporarily unavailable.                                                                  | HTTP/1.1 503 Service Unavailable                                |

### Paths

The web services are available at the following path:

**/rms/api/public/noark5/v2/{service-name}**

### Dates encoding

- Dates must be encoded as **ISO 8601** strings prior to sending them to the web services.
- Dates are returned by the web services as **ISO 8601** formatted strings.

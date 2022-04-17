### HTTP Response Status Codes

HTTP response status codes indicate whether a specific HTTP request has been successfully completed. Responses are grouped in five classes:

> Only listed common codes below, full list please check [here](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)

#### 1xx (Informational): The request was received, continuing process

##### 102 Processing

This code indicates that the server has received and is processing the request, but no response is available yet.

#### 2xx (Successful): The request was successfully received, understood, and accepted

##### 200 OK

The request succeeded.

##### 202 Accepted

The request has been received but not yet acted upon. It is noncommittal, since there is no way in HTTP to later send an asynchronous response indicating the outcome of the request. It is intended for cases where another process or server handles the request, or for batch processing.

#### 3xx (Redirection): Further action needs to be taken in order to complete the request

##### 301 Moved Permanently

The URL of the requested resource has been changed permanently. The new URL is given in the response.

##### 304 Not Modified

This is used for caching purposes. It tells the client that the response has not been modified, so the client can continue to use the same cached version of the response.

#### 4xx (Client Error): The request contains bad syntax or cannot be fulfilled

##### 400 Bad Request

The server cannot or will not process the request due to something that is perceived to be a client error (e.g., malformed request syntax, invalid request message framing, or deceptive request routing).

##### 401 Unauthorized

Although the HTTP standard specifies "unauthorized", semantically this response means "unauthenticated". That is, the client must authenticate itself to get the requested response.

##### 403 Forbidden

The client does not have access rights to the content; that is, it is unauthorized, so the server is refusing to give the requested resource. Unlike 401 Unauthorized, the client's identity is known to the server.

##### 404 Not Found

The server can not find the requested resource. In the browser, this means the URL is not recognized. In an API, this can also mean that the endpoint is valid but the resource itself does not exist. Servers may also send this response instead of 403 Forbidden to hide the existence of a resource from an unauthorized client. This response code is probably the most well known due to its frequent occurrence on the web.

##### 405 Method Not Allowed

The request method is known by the server but is not supported by the target resource. For example, an API may not allow calling DELETE to remove a resource.

##### 413 Payload Too Large

Request entity is larger than limits defined by server. The server might close the connection or return an Retry-After header field.

##### 414 URI Too Long

The URI requested by the client is longer than the server is willing to interpret.

##### 429 Too Many Requests

The user has sent too many requests in a given amount of time ("rate limiting").

#### 5xx (Server Error): The server failed to fulfill an apparently valid request

##### 500 Internal Server Error

The server has encountered a situation it does not know how to handle.

##### 502 Bad Gateway

This error response means that the server, while working as a gateway to get a response needed to handle the request, got an invalid response.

##### 503 Service Unavailable

The server is not ready to handle the request. Common causes are a server that is down for maintenance or that is overloaded. Note that together with this response, a user-friendly page explaining the problem should be sent. This response should be used for temporary conditions and the Retry-After HTTP header should, if possible, contain the estimated time before the recovery of the service. The webmaster must also take care about the caching-related headers that are sent along with this response, as these temporary condition responses should usually not be cached.

##### 504 Gateway Timeout

This error response is given when the server is acting as a gateway and cannot get a response in time.

[看完了，回到目录](/README.md)
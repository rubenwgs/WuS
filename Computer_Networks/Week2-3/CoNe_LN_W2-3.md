# Computer Networks - Week 2 & 3 (Book)
- Author: Ruben Schenk
- Date: 16.03.2021
- Contact: ruben.schenk@inf.ethz.ch

# 2. Application Layer
## 2.2 The Web and HTTP
### 2.2.1 Overview of HTTP

The `HyperText Transfer Protocol (HTTP)`, the Web's application-layer protocol, is at the heart of the Web. The client program and server program, which both implement HTTP, talk to each other by exchanging HTTP messages. HTTP defines the structure of these messages and how the client and server exchange the messages.

A `Web page` consists of objects. An `object` is simply a file - such as an HTML file, a JPEG image, or a video clip - that is addressable by a single URL.

Because `Web browsers` implement the client side of HTTP, we will use the words *browser* and *client* interchangeably. `Web servers`, which implement the server side of HTTP, house Web objects, each addressable by a URL.

When a user requests a Web page, the browser sends HTTP request messages for the objects in the page to the server. The server receives the requests and responds with HTTP response messages that contain the objects. HTTP uses TCP as its underlying transport protocol.

It is important to note that the server sends requested files to clients without storing any state information about the client. Because an HTTP server maintains no information about the clients, HTTP is said to be a `stateless protocol`.

### 2.2.2 Non-Persistent and Persistent Connections
When the client-server interaction is taking place over TCP, the application developer needs to make a decision - should each request/response pair be sent over a *separate* TCP connection, or should all of the requests and their corresponding responses be sent over the *same* TCP connection? In the former approach, the application is said to use `non-persistent connections`; and in the latter approach, `persistent connections`.

#### HTTP with Non-Persistent Connections
Let's suppose a Web page consists of a base HTML file and 10 JPEG images, and that all 11 of these objects reside on the same server. Assume non-persistent connections, where each TCP connection is closed after the server sends the object - the connection does not persist for other objects. Thus, in the assumed example, when a user requests the Web page, 11 TCP connections are generated.

To this end, we define the `round-trip time (RTT)`, which is the time it takes for a small packet to travel from client to server and then back to the client. The RTT includes packet-propagation delays, packet-queuing delays in intermediate routers and switches, and packet-processing delays.

<img src="./Figures/CoNe_Fig2-7.png" alt="Round-Trip Time"
	title="Figure 2.7: Back-of-the-envelope calculation for the time needed to request and receive an HTML file." width="750px"/><br>

Thus, roughly, the `total response time` is two RTTs plus the transmission time at the server of the HTML file.

#### HTTP with Persistent Connections

Non-persistent connections have some shortcomings:

1. For each connection (aka for each object), TCP buffers must be allocated and TCP variables must be kept in both the client and server. This can place a significant burden on the Web server.
2. Each object suffers a delivery delay of two RTTs - one RTT to establish the TCP connection and one RTT to request and receive an object.

With HTTP 1.1 persistent connections, the server leaves the TCP connection open after sending a response. Subsequent requests and responses between the same client and server can be sent over the same connection. Typically, the HTTP server closes a connection when it isn't used for a certain time.

The default mode of HTTP uses persistent connections with pipelining.

### 2.2.3 HTTP Message Format

The HTTP specifications include the definitions of the HTTP message formats. There are two types of HTTP messages, request messages and response messages.

#### HTTP Request Message

Below we provide a typical HTTP request message:

```http

GET /somedir/page.html HTTP/1.1
Host: www.someschool.edu
Connection: close
User-agent: Mozilla/5.0
Accept-language: fr

```

Although this particular request message has five lines, a request message can have many more lines or as few as one line. The first line of an HTTP request message is called the `request line`; the subsequent lines are called the `header lines`. The request line has three fields: the method field, the URL field, and the HTTP version field. The method field can take on  several different values, including `GET`, `POST`, `HEAD`, `PUT`, and `DELETE`.

Let's look closer at the header lines in the example:
- `Host`: specifies the host on which the object resides
- `Connection: close`: tells the server that it doesn't bother with persistent connections
- `User-agent`: specifies the user agent, that is, the browser type making the HTTP request
- `Accept-language`: indicates that the user prefers to receive a French version of the object, if such an object exists on the server

The general form of an HTTP request message looks as follows:

<img src="./Figures/CoNe_Fig2-8.png" alt="HTTP Request Message"
	title="Figure 2.8: General format of an HTTP request message." width="750px"/><br>

The `HEAD` method is similar to the `GET` method with the difference being, that the sever responds only with an HTTP message and leaves out the requested object (often used for debugging).<br>
The `PUT` method is often used in conjunction with Web publishing tools and forms. It allows a user to upload an object to a specific path on a specific Web server.<br>
The `DELETE` method allows a user, or an application, to delete an object on a Web server.

#### HTTP Response Message
The HTTP response message below could be the response to the example request message from above:

```http

	HTTP/1.1 200 OK
	Connection: close
	Date: Tue, 18 Aug 2015 15:44:04 GMT
	Server: Apache/2.2.3 (CentOS)
	Last-Modified: Tue, 18 Aug 2015 15:11:03 GMT
	Content-Length: 6821
	Content-Type: text/html

	(data data data data data ...)

```

The message has three sections: an initial `status line`, six `header lines`, and then the `entitiy body`. The entitiy body contains the requested object itself. The status line has three fields: the protocol version field, a status code, and a corresponding status message.

Let's look closer at the header lines of our example:
- `Connection: close`: indicates that the server is going to close the TCP connection after sending the message
- `Date`: indicates the time and date when the HTTP response was created and sent by the server
- `Server`: indicates that the message was generated by an Apache Web server
- `Last-Modified`: indicates the date and time when the object was created or last modified
- `Content-Length`: indicates the number og bytes in the object being sent
- `Content-Type`: indicates that the object in the entity body is HTML text

<img src="./Figures/CoNe_Fig2-9.png" alt="HTTP Response Message"
	title="Figure 2.9: General format of an HTTP response message." width="750px"/><br>

Some common status codes and associated phrases include:
- `200 OK`: Request succeeded and the information is returned in the response.
- `301 Moved Permanently`: Requested object has been permanently moved, the new URL is specified in `Location` header of the response message.
- `400 Bad Request`: Generic error code indicating that the request could not be understood by the server.
- `404 Not Found`: The requested document does not exists on this server.
- `505 HTTP Version Not Supported`: The requested HTTP protocol version is not supported by the server.

### 2.2.4 User-Server Interaction: Cookies

We mentioned above that an HTTP server is stateless. However, it is often desireable for a Web site to identify users, either because the server wishes to restrict user access or beause it wants to serve content as a function of the user identity. For these purposes, HTTP uses `cookies`.

Cookie technology has four components: (1) a cookie header line in the HTTP response message, (2) a cookie header line in the HTTP request message, (3) a cookie file kept on the user's end system and managed by the user's browser, and (4) a back-end database at the Web site.

<img src="./Figures/CoNe_Fig2-10.png" alt="Cookies"
	title="Figure 2.10: Keeping user state with cookies." width="750px"/><br>

Suppose Susan contacts `Amazon.com` for the first time. When the request comes in, the Amazon Web server creates a unique identification number for Susan and stores it in its back-end database. The Amazon Web server then responds to Susan's browser, including in the HTTP response a `Set-cookie` header, which contains the id number. The header might be:

```http

	Set-cookie: 1678

```

As Susan continues to browse the Amazon site, each time she requests a Web page, her browser consults her cookie file, extracts her id number for this site, and puts a cookie header linea in the HTTP request as follows:

```http

	Cookie: 1678

```

Although the Amazon Web site does not encessarily know Susan's name, it knows exactly which pages user 1678 visited, in which order, and at what times!

### 2.2.5 Web Caching
A `Web cache` - also called a `proxy server` - is a network that satisfies HTTP requests on the behalf of an origin Web server. The Web cache has its own disk storage and keeps copies of recently requested objects in this storage.

Typically a Web cache is purchased and installed by an ISP. For example, a university might install a cache on its campus network and configure all of the campus browsers to point to the cache.

There are two main reasons to deploy Web caching in the Internet:
1. A Web cache can substantially reduce the response time for a client request.
2. Web caches can substantially reduce traffic on an institution's access link to the Internet.

#### The Conditional GET
HTTP has a mechanism that allows a cache to verify that its objects are up to date. This mechanism is called the `conditional GET`. An HTTP request message is a so-called conditional GET message if (1) the request message uses the `GET` method and (2) the request message includes an `If-Modified-Since` header line.

Let's walk through an example: First, on behalf of a requesting browser, a proxy cache sends a request message to a web server:

```http

	GET /fruit/kiwi.gif HTTP/1.1
	Host: www.exotiquecuisine.com

```

Second, the Web server sends a response message with the requested object to the cache:

```http

	HTTP/1.1 200 OK
	Date: Sat, 3 Oct 2015 15:39:29
	Server: Apache/1.3.0 (Unix)
	Last-Modified: Wed, 9 Sep 2015 09:23:24
	Content-Type: image/gif

	(data data data data data ...)

```

Third, one week later, another browser requests the same object via the cache. The cache performs an up-to-date check by issuing a conditional GET. Specifically, the cache sends:

```http

	GET /fruit/kiwi.gif HTTP/1.1
	Host: www.exotiquecuisine.com
	If-modified-since: Wed, 9 Sep 2015 09:23:24

```

Suppose the object has not been modified since 9 Sep 2015 09:23:24. Then, fourth, the Web server sends a response message to the cache:

```http
	
	HTTP/1.1 304 Not Modified
	Date: Sat, 10 Oct 2015 15:39:29
	Server: Apache/1.3.0 (Unix)

	(empty entity body)
	
```

## 2.4 DNS - The Internet's Directory Service

## 2.6 Video Streaming and Content Distribution Networks
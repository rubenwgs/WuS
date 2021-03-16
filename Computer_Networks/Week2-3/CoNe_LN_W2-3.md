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

## 2.4 DNS - The Internet's Directory Service

## 2.6 Video Streaming and Content Distribution Networks
**Computer Networks - Lecture 13 & 14**

- Author: Ruben Schenk
- Date: 07.06.2021
- Contact: ruben.schenk@inf.ethz.ch

# 5. Network Layer

The `network-layer protocols` are among the most challenging in the protocol stack. The network layer can be decomposed into two interacting parts, the `data plane` and the `control plane`.

The primary role of the network layer is deceptively simple - to move packets to appropriate output links from a sending to a receiving host. For that there are two functions:

- `Forwarding`: A packet arrives at some input link of a router and needs to be moved to an appropriate link. This one of the functions of the *data plane* and happens router-locally.
- `Routing`: Network layer must determine a route a packet takes as it flow from sender to receiver. This is a network-wide operation that is very complex and expensive and is part of the *control plane*.

## 5.1 Network Service Models

The `network service model` specifies what service the network layer provides to the transport layer and how it is implemented. We distinguish a `connectionless datagram service` like IP and a `connection-oriented virtual circuits service`.

Both models are implemented with `store-and-forward packet switching`:

- Routers receive complete packets, store them temporarily (if necessary) before forwarding it. Most of the time *statistical multiplexing* is used to share the link bandwidth over time.

The switching element provides an internal buffer for each output port to handle contention.

### 5.1.1 Datagram Model

Each (IP) packet contains a destination address. This is used by the router to forward each packet individually on different paths. For this, we consult the `forwarding table` which is keyed by the address and stores the next hop for each destination address.

The datagram model uses the `internet protocol (IP)` which is based on datagrams and carries source and destination address in each packet.

### 5.1.2 Virtual Circuit Model

The virtual circuit model uses circuit switching, but in a virtual sense: there is no bandwidth reservation but rather statistical sharing of links. There are three phases in a virtual circuit:

1. *Connection establishment*: Circuit is set up, i.e., path is chosen and the circuit information is stored in the routers.
2. *Data transfer*: Circuit is used, i.e., packets are forwarded along the path.
3. *Connection teardown*: Circuit is deleted, i.e., circuit information is removed from routers.

Each packet thus only needs to carry a short label that identifies the circuit. This label, however, has no global meaning and is link specific, such that at every router it needs to be translated. The forwarding tables are keyed by the circuits identifier and returns the output port the circuit uses.

### 5.1.3 Multi-Protocol Label Switching

ISPs use the virtual circuit switching technology by setting up circuits in their backbone ahead of time. When a packet enters the network, it adds a 4 byte MPLS label to the IP packet and removes it upon leaving the network.

The advantage MPLS offers are:

- Potential increase of switching speed since the IP header is not needed.
- MPLS provides the ability to forward packets along routes that would not be possible using the standard IP routing protocols.

The following table provides a short summary over the advantages and disadvantages of both network service models:

| **Issue**          | **Datagram**                | **Virtual Circuit**        |
| :----------------- | :-------------------------- | :------------------------- |
| Setup Phase        | Not needed                  | Required                   |
| Router state       | Per destination             | Per connection             |
| Addresses          | Packet carries full address | Packet carries short label |
| Routing            | Per packet                  | Per circuit                |
| Failures           | Easier to mask              | Difficult to mask          |
| Quality of service | Difficult to add            | Easier to add              |

## 5.2 Internet Protocol - Version 4 (IPv4)

### 5.2.1 IP Datagram

Most of the fields of a IPv4 datagram are self-explanatory, the rest is explained in short here:

- *Type of Service*: Allows to distinguish between different types of datagrams, e.g., real-time (VoIP) vs non-real time (FTP)
- *Datagram length*: Total length of datagram in bytes, at max $2^{16}$ bytes, but normally not larger than 1500 to fit into an Ethernet frame.
- *Fragmentation offset and flags*: Used for IP fragmentation.
- *Protocol*: Specifies the transport layer protocol (TCP or UDP).
- *Options* Allows the IP header to be extended but leads to processing overhead (not included in IPv6 anymore).

<img src="./Figures/CoNe_LN_Fig13-1.JPG" width="650px"/><br>

### 5.2.2 IP Addresses and Prefixes

> An `IPv4 address` is a 32-bit number written in *dotted-quad* notation `a.b.c.d` where $a, \, b, \, c,$ and $d$ are 8-bit numbers.

IP addresses are allocated in `prefixes`. Addresses in an $L$-bit prefix have the same top $L$ bits. Thus, there are $2^{32-L}$ addresses in an $L$-bit. IP prefixes are written in IP address notation, e.g., `128.13.0.0/16` denotes the first address in the prefix ($L = 16$ in this case).

We say a prefix is *more specific* if it is longer, and hence has a smaller number of IP addresses. A *less specific* prefix has a shorter prefix and hence a larger number of IP addresses.

The first and last address of a prefix are typically not used since they have a special function:

- *Network Identifier*: First address in a prefix, e.g., `128.13.0.0` for `128.13.0.0/16`.
- *Broadcast Address*: Last address in a prefix, e.g., `128.13.255.255` for `128.13.0.0/16`.

Prefixes are also specified by using a `network mask`. Performing a logical AND on the two produces the prefix (i.e. the network identifier). Example: `255.255.255.0` is the network mask for a 24-bit prefix.

### 5.2.3 IP Forwarding

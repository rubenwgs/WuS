# Computer Networks - Week 4 & 5 (Book)
- Author: Ruben Schenk
- Date: 18.03.2021
- Contact: ruben.schenk@inf.ethz.ch

# 3. Transport Layer
Residing between the application and network layer, the `transport layer` is a central piece of the layered network architecture. It has the critical role of providing communication services directly to the application processes running on different hosts.

## 3.1 Introduction and Transport-Layer Services
A transport-layer protocol provides for `logical communication` between application processes running on different hosts. By logical communication, we mean that from an application's perspective, it is as if the hosts running the processes were directly connected.

Transport-layer protocols are implemented in the end systems but not in network routers.<br>
On the sending side, the transport layer converts the application-layer messages it receives from a sending application process into transport-layer packets, known as transport-layer `segments`. This is done by breaking the application messages into smaller chunks and adding a `transport-layer header` to each chunk. The transport layer then passes the segment to the network layer at the sending end system, where the segment is encapuslated within a network-layer packet (called a `datagram`) and sent to the destination.<br>
On the receiving side, the network layer extracts the transport-layer segment from the datagram and passes the segment up to the transport layer.

### 3.1.1 Relationship Between Transport and Network Layers
Recall that the transport layer lies just above the network layer in the protocol stack. Whereas a transport-layer protocol provdes a logical communication between *processes* running on different hosts, a network-layer protocol provides logical communication betwenn *hosts*.

Within and end system, a transport protocol moves messages from application processes to the network edge (that is, the network layer) and vice versa, but it doesn't have any say about how the messages are moved within the network core.

The services that a transport protocol can provide are often constrained by the service model of the underlying network-layer protocol. If the network-layer protocol cannot provide delay or bandwidth guarantess for transport-layer segments sent between hosts, then the transport-layer protocol cannot provide delay or bandwidth guarantees for application messages sent between processes.

### 3.1.2 Overview of the Transport Layer in the Internet
Recall that the Internet makes two distinct transport-layer protocols available to the application layer. One of these protocols is `UPD (User Datagram Protocol)`, which provides an unreliable, connectionless service to the invoking application. The second of these protocols is `TCP (Transmission Control Protocol)`, which provides a reliable, connection-oriented service to the invoking application.

Before we proceed with our brief introduction of UDP and TCP, it will be useful to say a few words about Internet's network layer. The Internet's `network-layer protocol` has a name - `IP`, for `Internet Protocol`. IP provides logical communication between hosts. The IP service model is a `best-effort delivery service`. This means that IP makes its "bestt effort" to deliver segments between communicating hosts, *but it makes no guarantees*. In particular, it does not guarantee segment delivery, it does not guarantee oderly delivery of segments, and it does not guarantee the integrity of the data. For these reasons, IP is said to be an `unreliable service`. We also mention here that every host has at least one `network-layer address`, a so called `IP address`.

The most fundamental responsibility of UDP and TCP is to extend IP's delivery service between two end systems to a delivery service between two processes running on the end sysstems. Extending host-to-host delivery to process-to-process delivery is called `transport-layer multiplexing` and `demultiplexing`. UDP and TCP also provide integrity checking by including error-detection fields in their segment's headers.

These are the only two services UDP provides. In particular, like IP, UDP is an unreliable service.

TCP, on the other hand, offers several additional services to applications:
- It provides `reliable data transfer`. TCP ensures that data is delivered from sending processes to receiving processes, correctly and in order.
- It provides `congestion control`. Loosely speaking, TCP congestion control prevents any one TCP connection from swamping the links and routers between communicating hosts with an excessive amount of traffic.

## 3.2 Multiplexing and Demultiplexing
At the destination host, the transport layer receives segments from the network layer just below. The transport layer has the responsibility of delivering the data in these segments to the appropriate application process running in the host. Let's examine how this is done:

First recall that a process can have one or more `sockets`, doors through which data passes from the network to the process and vice versa. The transport layer in the receiving host does not actually deliver data directly to a process, but instead to an intermediary socket. To identify a socket, each socket has a unique identifier.

Each transport-layer segment has a set of fields in the segment for the purpose of socket identification. At the receiving end, the transport layer examines these fields to identify the receiving socket and then directs the segment to that socket. This job of delivering the data to the correct socket is called `demultiplexing`. The job of gathering data chunks from different sockets, encapsulating them with header information and passing the resulting segments to the network layer is called `multiplexing`.

Let us examine how multiplexing and demultiplexing is actually done:<br>
We know that transport-layer multiplexing requires (1) that sockets have unique identifiers, and (2) that each segment has special fields that indicate the socket to which the segment is to be delivered. These special fields are the `source port number field` and the `destination port number field`.

<img src="./Figures/CoNe_Fig3-3.png" alt="Port-Number Fields"
	title="Figure 3.3: Source and destination port-number fields in a transport-layer segment." width="350px"/><br>

Each port number is a 16-bit number, ranging from 0 to 65'535. The port numbers ranging from 0 to 1023 are called `well-known port numbers` and are restricted, which means that they are reserved for use by well-known application protocols such as HTTP (port number 80) and FTP (port number 21).

#### Connectionless Multiplexing and Demultiplexing
We can describe UDP multiplexing/demultiplexing as follows. Suppose a process in Host A, with UDP port 19157, wants to send a chunk of application data to process with UDP port 42069 in Host B:
1. The transport layer in Host A creates a transport-layer segment that includes the applciation data, the source port number (19157), the destination port number (42069).
2. The transport layer passes the resulting segment to the network layer.
3. The network layer encapsulates the segment in an IP datagram and tries to deliver the segment to the receiving host.
4. If the segment arrives at the receiving Host B, the transport layer examines the destination port number (42069).
5. Finally, the transport layer at the receiving Host B delivers the segment to its socket identified by port 42069.

It is important to note that a UPD socket is fully identified by a two-tuple consisting of a destination IP address and a destiantion port number.

#### Connection-Oriented Multiplexing and Demultiplexing


## 3.3 Connectionless Transport: UDP

## 3.4 Principles of Reliable Data Transfer

## 3.5 Connection-Oriented Transport: TCP

## 3.6 Principles of Congestion Control

## 3.7 TCP Congestion Control
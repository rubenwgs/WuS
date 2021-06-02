**Computer Networks - Lecture 5 & 6**

- Author: Ruben Schenk
- Date: 01.06.2021
- Contact: ruben.schenk@inf.ethz.ch

# 3. Transport Layer

## 3.1 What do we need in the transport layer? (Principles)

What do we actually need in the transport layer?

- Network should be kept minimal, i.e., easy to build, broadly applicable
- We need a global `best-effort` packet delivery service
- Applications should be restricted to app-specific functionality
- We need data delivery to the correct application, which means that the transport needs to demultiplex incoming data
- We need files or byte-streams abstractions for applications
- Reliable transfer if needed
- No overloading at the receiver and at the network

## 3.2 How do we build reliable transport? (Reliable Transport)

Since the Internet is an unreliable environment, packets may get *lost*, *corrupted*, *reordered*, or even *duplicated*.

> A `reliable transport protocol` should enable communication with the following properties:
>
>- *Correctness*: Packets should be received in the same order and without any gap
>- *Timeliness*: It should minimize the time until data is transferred.
>- *Efficiency*: It should minimize the use of bandwidth by not sending too many packets.
>



## 3.3 How does the Internet's transport work?

## 3.4 Sockets: the application and the transport interface
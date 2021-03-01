# Computer Networks - Week 1 (Book)
# 1. Computer Networks and the Internet
## 1.1 What Is the Internet?
### 1.1.1 A Nuts-and-Bolts Description

The Internet is a computer network that interconnects billions of computing devices through the world. In Internet jargon, all of these devices are called `hosts` or `end systems`. <br>
End systems are connected by a network of `communication links` and `packet switches`. Different links can transmit data at different rates, with the `transmission rate` of a link measured in bits/second. 

`Packages` are packages of information, put together by a sending end system. The sequence of communication links and packet switches traversed by a packet from the sending end system to the receiving end system is known as a `route` or `path` through the network.

End systems access the Internet through `Internet Service Providers (ISPs)`, including residential ISPs such as local cable or telephone companies, corporate ISPs, university ISPs etc. <br>
End systems, packet switches, and other pieces of the Internet run `protocols` that control the sending and receiving of information within the internet. The `Transmission Control Protocol (TCP)` and the `Internet Protocol (IP)` are two of the most important protocols in the Internet.

### 1.1.2 A Services Description
We can also describe the Internet from an entirely different angle - namely, as *an infrastructure that provides services to applications*. The applications are said to be `distributed applications`, since they involve multiple end systems that exchange data with each other.

End systems attached to the Internet provide a `socket interface` that specifies how a program running on one end system asks the Internet infrastructure to deliver data to a specific destination program running on another system.

### 1.1.3 What Is a Protocol?
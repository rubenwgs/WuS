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
#### Network Protocols
All activity in the Internet that involves two or more communicating remote entities is governed by a protocol. Protocols are running everywhere in the Internet.
> A **protocol** defines the format and the order of messages exchanged between two or more communicating entities, as well as the actions taken on the transmission and/or receipt of a message or other event.

## 1.2 The Network Edge
<img src="./Figures/CoNe_Fig1-1.png" alt="Network"
	title="Figure 1.1: Some pieces of the Internet" height="750px"/><br>

In this book, end systems are also refered to as `hosts` because they host (that is, *run*) application programs such as a Web browser program, a Web server program etc. <br> Hosts are sometimes further divided into two categories: `clients` and `servers`. Informally, clients tend to be desktop and mobile PCs, smartphones and so one, whereas servers tend to be more powerful machines that store and distribute Web pages, stream video, relay e-mail, and so on.

### 1.2.1 Acess Networks
#### Home Access: DSL, Cable, FFTH, Dial-Up, and Satellite

Today, the most prevalent types of broadband residential acess are `digital subscriber line (DSL)` and cable. A residence typically obtains DSL Internet access from the same local telephone company (*telco*) that provides its wired local phone access. <br>
Each customer's DSL modem uses the existing telephone line (twisted-pair copper wire) to exchange data with a digital subscriber line access multiplexer (*DSLAM*) located in the telco's local central office (*CO*).

The residential telephone line carries both data and traditional telephone signals simultaneously, which are encoded in different frequencies:
* A high-speed downstream channel, in the 50 kHz to 1 MHz band
* A medium-speed upstream channel, in the 4 kHz to 50kHz band
* An ordinary two-way telephone channel, in the 0 to 4 kHz band

<img src="./Figures/CoNe_Fig1-5.png" alt="DSL"
	title="Figure 1.5: DSL Internet access" width="750px"/><br>
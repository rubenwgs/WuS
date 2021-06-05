**Computer Networks - Lecture 9 & 10**

- Author: Ruben Schenk
- Date: 01.06.2021
- Contact: ruben.schenk@inf.ethz.ch

### 3.3.6 Timeouts & Retransmissions

TCP resets a timer whenever new data is ACKed, not on a duplicate ACK. Upon a timeout, the packet containing the next byte, i.e., the seqNo of the ACK packet.

The problem here is that setting the timeout value is hard. Choosing it too short will result in duplicate packets, choosing it too long will result in inefficiencies. In order to set the timeout value, we need some estimate of the connections RTT.

> The `Kern-Partridge Algorithm` gives us an idea on how to estimate the RTT. We start with measuring the $\text{SampleRTT}$ for original transmissions, that is, not for retransmissions. We are then able to estimate the RTT the following way:
>
> $$ \text{SampleRTT} = \text{AckRcvdTime} - \text{SendPacketTime} \\ \text{EstimatedRTT} = \alpha \times \text{EstimatedRTT} + (1 - \alpha) \times \text{SampleRTT}$$
>
> for $\alpha = 0.875$. From this, we determine the `retransmission timeout (RTO)` by
> 
> $$ \text{RTO} = 2 \times \text{EstimatedRTT} $$
>
> We might use an `exponential backoff`, that is, upon a timeout we double the $\text{RTO}$. Every time a new measurement comes if, we collapse the $\text{RTO}$ back to $2 \times \text{EstimatedRTT}$.

### 3.3.7 Congestion Control

`Congestion control` aims at solving three problems:

- *Bandwidth estimation*: How to adjust the bandwidth of a single flow to the bottleneck bandwidth?
- *Bandwidth adaptation*: How to  adjust the bandwidth of a single flow to variation of the bottleneck bandwidth?
- *Fairness*: How to share bandwidth fairly among flows, without overloading the network?

It is important to note that congestion control differs from flow control, but botha re provided by TCP though:

- `Flow control`: prevents one fast sender from overloading a slow receiver.
- `Congestion control`: Prevents a set of senders from overloading the network.

The sender adapts its sending rate based on these two windows:

- `Receiving Window (RWND)`: How many bytes can be sent without overflowing the receiver buffer?
- `Congestion Window (CWND)`: How many bytes can be sent without overflowing the routers?

From those two windows follows the `Sender Window`. determined by $\min{(\text{CWND}, \, \text{RWND})}$.

#### Congestion Detection

There are essentially three ways to detect congestion:

- The network could tell the source but the signal itself could be lost on the way.
- We could measure the packet delay but the signal probably will be noisy.
- We could measure the packet loss. This is a fail-safe signal that TCP already has to detect.

If we detect duplicated ACKs, we have a `mild congestion signal` and if we have a timeout, we have a `severe congestion signal`.

#### Reacting to Congestion

TCP's approach is to gently increase when not congested and to rapidly decrease when congested.

We start with the `Slow Start Phase`, that is, we set the $\text{CWND}$ to $1$ and increase it by one for each ACK we receive. However, once we have a rough estimate of the bandwidth, we need a more gentle adjustment algorithm.

The two possible options are:

- `Multiplicative Increase or Decrease (MID)`, i.e., $\text{CWND} = a \cdot \text{CWND}$.
- `Additive Increase or Decrease`, i.e., $\text{CWND} = b + \text{CWND}$.

TCP implements additive increase multiplicative decrease (AIMD): After each ACK, we increment the $\text{CWND}$ by $1 / \text{CWND}$. Per RTT, the increase is therefore at most $1$. One might as the question on when the sender leaves the slow-start phase and starts AIMD. We introduce a `slows start threshold`, and adapt it as a function of congestion. On a timeout, we set $\text{SSTHRESH} = CWND/2$.

### 3.3.8 QUIC - Quick UDP Internet Connections

## 3.4 Sockets: the application and the transport interface

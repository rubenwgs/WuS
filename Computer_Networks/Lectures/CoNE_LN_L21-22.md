**Computer Networks - Lecture 21 & 22**

- Author: Ruben Schenk
- Date: 15.06.2021
- Contact: ruben.schenk@inf.ethz.ch

## 6.5 LAN Switching

Modern Ethernet is based on the usage of switches rather than multiple access, i.e., hosts are connected with to switch ports. We distinguish three types of devices:

- `Hub/Repeater`: Only at the physical layer, strengthens the signal.
- `Switch`: At the link layer, looks where to forward the packet.
- `Router`: At the network layer.

### 6.5.1 Inside a Switch

A `switch`:

- uses frame addresses to connect input port to the right output port. It allows fro multiple frames to be switched in parallel.
- ports are full-duplex, allowing for both input and output. There is no multiple access control.
- in case there is contention, there are both input and output buffers. On overload, frames are lost.

Switches and hubs have replaced shared cable of classic Ethernet, since they provide a better reliability and scalable performance.

### 6.5.2 Switch Forwarding

A switch needs to find the right output port for the destination address in the Ethernet frame.

#### Backward Learning

Switches use a port/table to forward frames and they generate the table as follows:

1. To fill the table, it looks at the source address of the input frames.
2. To forward, it sends it to the port, or else broadcasts it to all ports.

This method works as long as there are no loops in the system.

#### Switch Spanning Tree

One solution to loops in the network are spanning trees.  
Switches collectively find a `spanning tree` for the topology and then only forward to along the tree. When a frame is *broadcasted*, it goes all the way up to the root and then all the way down to all the branches.

**Spanning Tree Algorithm**

1. Elect a root node of the tree (the switch with the lowest address)
2. Grow tree as shortest distances from the root (using the lowest address to break distance ties)
3. Turn off ports for forwarding if they are not on the spanning tree

# 7. Physical Layer

The `physical layer` concerns how signals are used to transfer message bits over a link. We want to send digital signals but the medium we use uses analog signals.

## 7.1 Properties of Media

### 7.1.1 Link Model

WE abstract the physical model as follows: We consider the `rate` (or bandwidth) in bits/second and the `delay` or `latency` in seconds as the key properties of such a physical channel.

#### Message Latency

The `message latency` $L$ consists of two parts:

- `Transmission delay` $T$: The time used to put a $M$ bit message on the wire:
  
  $$
    T = \frac{M \, [\text{bits}]}{Rate \, [\text{bits/sec}]} = \frac{M}{R} \, [\text{sec}]
  $$
- `Propagation delay` $P$: The time used for the bits to propagate across the wire:
  
  $$
    P = \frac{length}{speed \, of \, signals} = \frac{length}{\frac{2}{3}c} = D \, [\text{sec}]
  $$

This yields a total latency or delay of $L = \frac{M}{R} + D$. For rates we use powers of $10$, for storage/data sizes we use powers of $2$.

#### Bandwidth-Delay Product

The amount of data in flight is the `bandwidth.delay product`, given by $BD = R \cdot D$, and is measured in either bits or messages.

### 7.1.2 Types of Media

We introduce the following different types of media:

- *Wires - Twisted Pair*: Used in LANs and telephone lines. Twists reduce radiated signals and effects of external interference.
- *Wires - Coaxial Cable*: A copper core shielded by insulating material, braided outer conductor and protective plastic covering. Provides a better performance due to better shielding.
- *Fiber*: Long, thin, pure strands of glass allow for enormous bandwidths over long distances. Works by having a light source at one end and a photo-detector on the other end. Transmits at around $\frac{2}{3}c$.
- *Wireless*: Sender radiates signal over and entire region into all directions. This potentially leads to interference.

## 7.2 Simple Signal Propagation

Analog signals are used to encode digital bits. A signal over time can be represented by its frequency components (called *Fourier analysis*). As signals `propagate over a wire`, the following happens:

1. The signal is delayed (since it propagates at $\frac{2c}{3}$)
2. The signal is attenuated
3. Frequencies above a cutoff are highly attenuated
4. Noise is added to the signal

When a signal, however, is `propagated over fiber`, the light propagates with very low loss in three very wide frequency bands (furthermore, at these frequencies the attenuation is very low).

Signals that are sent over `wireless` travel at the speed of light, spread out and attenuate at a fast rate. Multiple signals on the same frequency

## 7.3 Modulation Schemes

We need signals to represent bits. One way to do this is the `Non-Return to Zero (NRZ)` scheme. In this scheme, high voltage ($+V$) represents a $1$, low voltage ($-V$) represents a $0$.

This simple scheme uses only 2 levels, if we were to increase it to 4 levels, it could support 2 bits per symbol

## 7.4 Fundamental Limits
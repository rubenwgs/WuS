**Computer Networks - Lecture 17 & 18**

- Author: Ruben Schenk
- Date: 10.06.2021
- Contact: ruben.schenk@inf.ethz.ch

## 5.6 Border Gateway Protocol (BGP)

So far, we have looked at `intra-domain routing protocols` like distance vector and link state algorithms. These work fine within, e.g., an autonomous system, but as soon as a network gets too big, they quickly become infeasible:

- Distance vector protocols converge slowly, so for a network as big as the internet, convergence would never happen.
- Link state algorithms need the whole network topology, which is impossible for the internet.

Since the internet is a network of networks, referred to as an `autonomous system (AS)`, we use special `inter-domain routing protocols` like `BGP` to connect the autonomous systems. By using BGP, autonomous systems exchange information about IP prefixes that they can reach. The protocol needs to solve three key challenges:

- *Scalability*: The number of networks and prefixes is huge.
- *Privacy*: Networks don't want to expose internal topologies.
- *Policy Enforcement*: The network needs to control where to send and receive traffic in the absence of an internet-wide link-cost metric.

BGP relies on `path-vector routing`, similar to distance-vector routing, but with the key idea, that we advertise an *entire AS-level path* instead of distances.

### 5.6.1 BGP Policies

Two ASes connect only if they have a business relationship. We distinguish between two types of relationships:

- Customer-Provider Relationship:
  - In a customer-provider relationship, a customer pays a provider to get internet connectivity globally, e.g. Swisscom is a customer of Deutsche Telekom. The amount the customer pays is based on the peak usage.
- Peer-Peer Relationship
  - In a peer-peer relationship, peers don't pay each other for connectivity bur rather connect out of common interests, mainly since they exchange a large amount if traffic, e.g. Deutsche Telekom and AT&T.

#### Policy Rules

BGP obeys the following policy rules:

- Providers transit traffic for their customers.
- Peers to not transit traffic between each other.
- Customers do not transit traffic between their providers.

#### Selection and Export

In `selection`, we must decide which path to use for *outbound* traffic. The general rule is to prefer routes coming from customers over peers over providers.

In `export` we must decide which path to use for *inbound* traffic. Routes coming from customers are propagated to everyone else, that is, to peers and providers, where as routes coming from peers and providers are only propagated to customers.  
Note that this requires Tier-1's to be connected through a *full-mesh of peer links*, otherwise the Internet would be partitioned.

### 5.6.2 BGP Protocol

### 5.6.2 Problems with BGP
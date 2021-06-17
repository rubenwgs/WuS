**Computer Networks - Lecture 23 & 24**

- Author: Ruben Schenk
- Date: 17.06.2021
- Contact: ruben.schenk@inf.ethz.ch

#### BGP Does not validate the content of advertisements

We might get `bogus AS paths` by removing a part of an AS path, for example turning '701 3715 88' into '701 88'. This way we can for example avoid AS 3715 or help AS 88 look like it is closer to the Internet's core.

We might also add ASes to the path, e.g. turning '701 88' into '701 3715 88'. This way we can trigger loop detection in AS 3715 or making our AS look like it has richer connectivity.

#### Proposed enhancements

We could develop a `secure BGP`, with *origin authentication* and *cryptographic signatures*. `BGPsec` adds the following:

- *Address attestations*: Claims the right to originate a prefix, is signed and distributed out-of-band, and is checked through delegation chain from ICANN.
- *Route attestations*: Distributed as an attribute in BGP update messages, and signed by each AS as route traverses the network.
- *Resource Public-Key Infrastructure (RPKI)*: Per-prefix certificate issued by Regional Internet Registries (RIR), and used to authenticate the first AS hop through Route Origin Authorization (ROA).

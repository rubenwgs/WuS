**Computer Networks - Lecture 19 & 20**

- Author: Ruben Schenk
- Date: 14.06.2021
- Contact: ruben.schenk@inf.ethz.ch

## 6.2 Error Detection and Correction

Bit-errors are introduced by signal attenuation and electromagnetic noise and are, in general, not avoidable when using certain types of media for transport. Our aim is to detect and possibly even correct these errors at a very low level, namely at the link layer. A possibility is to add `redundancy`, i.e., check bits that allow some errors to be detected. Adding even more check bits even makes it possible to directly correct some errors.

*Goal*: Structure code to detect many errors with few check bits and modest computational effort.

### 6.2.1 Intuition & Usage

A `codeword` consists of $D$ data bits and $R$ check bits. The sender computes $R$ based on the data $D$ and sends the codeword of $D + R$ bits (concatenated). A receiver then, upon receiving $D + R$ bits, recomputes $R'$ based on $D$. If $R'$ doesn't match $R$, then there is an error somewhere.

*Note*: For data bits $D$, the set of correct codewords should only be a tiny fraction of the set of all codewords, such that the probability of a randomly chosen codeword being correct is very small.

#### Hamming Distance

Let the `distance` of two codewords $D + R_1$ and $D + R_2$ be the number of bits that need to be flipped to turn $D + R_1$ into $D + R_2$ (or vice versa).

> The `hamming distance` of a code is the minimum distance between a pair of codewords.

> A code of hamming distance $d + 1$ can `detect` up to $d$ errors.

> A code of hamming distance $2d + 1$ can `correct` up to $d$ errors by mapping to the closest codeword.

### 6.2.2 Error Detection

#### Parity Bit

Take $D$ data bits and add only a single `check bit` $c$ such that $c$ is the sum of all $D$ bits modulo $2$, i.e.

$$ c \equiv_2 \sum_{i = 1}^D x_i$$

where $x_i$ denotes the $i$-th data bit.

The distance of the code is $2$, it can thus detect exactly one error (but not locate it) and correct none.

#### Checksums

*Idea*: Sum up data in $N$-bit words, that gives a stronger protection than parity and is widely used in TCP/IP/UDP etc. `Internet checksum` works as follows:

**Sending side:**

1. Arrange data in $16$-bit words.
2. Put $0$ in the checksum position and add the words.
3. Add any carryover back to get $16$ bits.
4. The checksum is now given by the complement (negation).

**Receiving side:**

1. Arrange the data in $16$-bit words.
2. Add the words and the checksum.
3. Add any carryover back to get $16$ bits.
4. Negate the result and check if it is $0$, if not, then an error occurred.

#### Cyclic Redundancy Check (CRC)

Let the data $D$ consist of $d$ data bits. Sender and receiver then agree on a $k + 1$ bit pattern, which is called the generator $G$ (and can be expressed as a polynomial over the finite field $GF(2)$). The sender will then choose $r$ bits $R$ to append to $D$ such that the resulting $d + r$ bit pattern is congruent modulo $2$ to $G$, i.e., $D + R \equiv_2 G$.  
The receiver then checks whether the received $d + r$ bits are divisible by $G$ with a $0$ remainder. If not, an error occurred.

### 6.2.3 Error Correction

The general problem that makes error correction so hard is that check bits aren't reliable either. If we can construct a code of Hamming Distance $\geq 2d + 1$, then codewords containing at most $d$ bit errors are uniquely mapped to the closest valid codeword.

#### Hamming Code

A `hamming code` is a code with hamming distance $3$. It can hence correct $1$ bit error. For a message of $n$ bits, choose $k$ such that $n = 2^k - k - 1$ holds. We insert check bits at positions of powers of $2$, starting with position $1$.  
When written in binary, the positions of the check bits have exactly one bit set to $1$. The check bit $p8$ for example records the parity of all positions that have the $4$-th bit set to $1$. Check bit $p4$ records parity of all positions that have the $3$rd bit set to $1$.

Now to decode, we recompute the check bits, arrange them as a binary number, called the `syndrome`. It tells us the error positions where the bit needs to be flipped. If the syndrome is $0$, no error occurred.

## 6.3 Retransmissions

## 6.4 Multiple Access

## 6.5 Switching
# Lesson 1: DDoS Attacks

The slides for this lesson can be found [here](./pdfs/DDoS%20Attacks.pdf).

## DoS taxonomy

The DoS terms defined in this lesson are as follows:

* **Random scanning** - Each compromised computer probes random addresses.
* **Permutation scanning** - All compromised computers share a common
pseudo-random permutation of the IP address space.
* **Signpost scanning** - Uses the communication patterns of the compromised
computer to find new targets.
* **Hitlist scanning** - A portion of a list of targets is supplied to a
compromised computer.
* **Subnet spoofing** - Generate random addresses within a given address space.
* **Random spoofing** - Generate 32-bit numbers and stamp packets with them.
* **Fixed spoofing** - The spoofed address is the address of the target.
* **Server application** - The attack is targeted to a specific application on a
server.
* **Network access** - The attack is used to overload or crash the
communication mechanism of a network.
* **Infrastructure** - The motivation of this attack is a crucial service of a
global internet operation, for example a core router.

## Network DoS

**Goal**: take out a large site with little computing power required, mainly
achieved through **amplification** - using a small number of packets to achieve
a big effect.

### Types of amplification attacks

1. **DoS bugs** - exploiting a design flaw in a service, disrupting the machine
and crashing the server application.
2. **DoS flood** - commanding a botnet to generate a flood of requests against
a server application.

## Network layers affected by DoS

DoS attacks can happen at any layer of the network stack, here are some
examples:

* **Link** - saturating the link until no communication can occur.
* **TCP/UDP** - causing the server to maintain too many sessions.
* **Application** - requesting a large number of large file downloads, taxing
the server.

## SYN floods

This attack takes advantage of the TCP handshake, forcing a server to maintain
the state of a large number of incomplete handshakes and dead sessions. The
attacker floods the server with SYN packets with random source IP addresses -
the server will attempt to send back a SYN/ACK but, because the IP address is
fake, these handshakes will never be completed.

This effectively fills up the backlog queue of the server listening on its
socket - also wastes server memory maintaining state for incomplete connections.
No further connections are possible while the server is waiting on a response
from the spoofed IP addresses.

### Defending against SYN floods

Use **syncookies** - removing connection state from the server, small
performance overhead for this implementation, as well.

> SYN cookie is a technique used to resist IP Spoofing attacks. The technique's
> primary inventor Daniel J. Bernstein defines SYN cookies as "particular
> choices of initial TCP sequence numbers by TCP servers." In particular, the
> use of SYN cookies allows a server to avoid dropping connections when the SYN
> queue fills up. Instead of storing additional connections, the SYN queue entry
> is encoded into the sequence number sent in the SYN+ACK response. If the
> server then receives a subsequent ACK response from the client with the
> incremented sequence number, the server is able to reconstruct the SYN queue
> entry using information encoded in the TCP sequence number and proceed as
> usual with the connection.

Using **proxies** - powerful servers protect webservers from SYN floods, only
forwarding traffic to the webserver if the TCP session is legitimate. The
powerful servers receive the SYN flood and drop connections if the TCP session
never completes. This was first implemented by organizations like CloudFlare and
Prolexic.

### Sophisticated SYN flood

In order to bypass proxies, some botnets establish the entire TCP session,
but instead of sending legitimate HTTP headers, they send short HTTP headers and
continue to flood the server at the application level.

While this bypasses the proxy, the bots can no longer spoof their IP address and
must maintain legitimate TCP connections. In addition, because the bot's IP
address is exposed, the proxy can rate-limit the bot's HTTP requests. Finally,
the location / IP addresses of the botnet / zombies are exposed to the
defenders.

## DoS mitigation through client puzzles

The idea with **client puzzles** is that, in order to continue with the session,
the server sends the client a mathematical puzzle to solve, causing the client
to have to do some computation and provide an answer before the server will
handle the client's request.

During a DoS attack, the server will generate client puzzles that will continue
to get harder as the DoS attack continues. If no attack is occurring, the server
will cease sending client puzzles.

The primary limitation discussed in this lecture is that it's hard to scale
hard puzzles based on CPU computation because not all devices are created equal.
Cellphones will have a harder time accessing sites if required to solve client
puzzles.

## DoS mitigation through IP traceback

The idea here is to modify core internet routers to record information,
specifically related to the path of the packet, so that the victim can determine
the source of a DoS attack.

Some assumptions have to be made my the implementers of this defense:

* Most core networks routers remain uncompromised
* Attackers send many packets
* Route from attacker to victim remains relatively stable

Some issues exist with this mitigation:

* Requires more space in the IP packet in order to store pathing information
* Paths can be long between attacker and victim
* No extra fields currently exist in the IP header

In order to implement this without too many changes to the IP specification, we
can:

* Store one link in each packet, as the router probabilistically stores its own
address.
* Regardless of the path length, this requires a fixed amount of space in the IP
header as we are only overwriting the source address.

### Edge sampling

Using the assumptions defined above and the probabilistic storage of router
addresses in the destination field of the IP header, victims are able to conduct
**edge sampling** in order to determine the path and source of an attack.
Because routers probabilistically store their information in the IP header, with
enough edge samples, the victim is able to reconstruct a graph using the edges
to determine the source of an attack.

## DoS reflection attacks

**Reflection attacks** are a DoS technique in which the attacker uses a botnet
to spoof the IP address of the victim for requests that will get serviced to the
victim. For instance, the botnets all use the IP address of the victim to send
DNS or HTTP requests to a server. The servers are unwittingly responding to
these requests and sending the responses to the victim, causing the victim to
be overwhelmed with unsolicited responses.

IP traceback isn't helpful here because any tracing would lead back to the
**reflectors** - legitimate servers answering a request.

## Capability based defenses

Similar to how capabilities are provided to services in some operating system
implementations, in order to communicate with a server the client sends a SYN
request to the server but also includes in the request a request for a
**capability** - a unique, cryptographically secure secret maintained by the
server that the client must offer in order to communicate.

The client can continue to communicate once it receives its capability, offering
the capability each time it sends a packet. Once the client becomes hostile,
however, due to detection via rate-limiting and its number of requests, the
server can revoke its capability, causing its traffic to be dropped. This
mitigation can take place as close to the source as possible, and works best
if done so.

## Quizzes

**Which of these are reasons why the UDP-based NTP protocols is particularly
vulnerable to amplification attacks**?

- [X] A small command can generate a large response
- [X] Vulnerable to IP spoofing

**SYN cookies quiz**

- [X] The server must reject all TCP options because the server discards the
SYN queue entry.

**With regards to a UDP flood attack, which of the following statements are
true:**

- [X] Attackers can spoof the IP address of their UDP packets
- [X] Firewalls cannot stop a flood because the firewall is susceptible to
flooding.

**Client puzzles quiz**

Which of the following statements are true?

- [X] Client puzzles should be stateless.
- [X] Client puzzle complexity should increase as the strength of the attack
increases.

**Which of the following are assumptions that can be made about IP Traceback?**

- [X] Attackers may work alone or in groups.

**Select all the statements that are true for edge sampling:**

- [X] Multiple attackers can be identified since edges identify splits in
reverse path.
- [X] Requires space in the IP packet header.

**Self defense against reflector attacks should incorporate:**

- [X] Server redundancy - servers should be located in multiple networks and
locations.
- [X] Traffic limiting - traffic from a name server should be limited to
reasonable thresholds.

## References

1. [https://blog.cloudflare.com/the-ddos-that-almost-broke-the-internet/](https://blog.cloudflare.com/the-ddos-that-almost-broke-the-internet/)
2. [A DoS-limiting Network Architecture](./pdfs/tva-sigcomm2005.pdf)
3. [Practical Network Support for IP Traceback](./pdfs/Sigcomm00.pdf)
4. [On Memory-Bound Functions for Fighting Spam](./pdfs/crypto03.pdf)
5. [Moderately Hard, Memory-bound Functions](./pdfs/memory-longer-acm.pdf)
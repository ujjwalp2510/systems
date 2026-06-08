# Networking

## Concept

Networking is the set of rules, protocols, and infrastructure that allow computers to communicate with each other.

Examples:

- Laptop ↔ Server
- Mobile App ↔ Backend
- Backend ↔ Database
- Microservice ↔ Microservice

Without networking, computers would be isolated and unable to exchange data.

---

## Mental Model

Think of networking as a global postal system.

```text
Computer A
    ↓
Network
    ↓
Computer B
```

Just as letters need addresses and delivery rules, computers need IP addresses and protocols.

---

## Practical Example

When you open:

```text
amazon.com
```

your computer communicates with Amazon's servers through multiple networking layers.

---

## System Design Relevance

Every system design topic builds on networking:

- APIs
- Load Balancers
- Databases
- CDNs
- Microservices
- Reverse Proxies
- Service Discovery

Without networking fundamentals, advanced system design becomes memorization.

---

## Interview Notes

Networking is fundamentally about communication between machines.

Most large-scale systems are simply many computers communicating efficiently.

---

# Server

## Concept

A server is a computer (or a set of computers) that runs one or more services and responds to requests from clients over a network.

A server is not special hardware.

Any machine becomes a server when it runs software that listens for incoming requests.

Example:

```js
app.listen(3000);
```

---

## Mental Model

```text
Client
   ↓ Request
Server
   ↓ Response
Client
```

A server waits for requests and responds when requests arrive.

---

## Practical Example

```text
Chrome
   ↓
Node.js Backend
   ↓
Response
```

The Node.js application acts as a server.

---

## System Design Relevance

Every scalable application consists of servers.

Examples:

- Web Servers
- Application Servers
- Database Servers
- Cache Servers

---

## Interview Notes

A useful definition:

> A server is just a computer running a program that listens on a port.

---

# Internet

## Concept

The Internet is a massive network of interconnected computers.

It is not the cloud.

It is a collection of routers, switches, cables, servers, and devices connected together.

---

## Mental Model

```text
Laptop
   ↓
Router
   ↓
ISP
   ↓
Internet
   ↓
Amazon Server
```

---

## Practical Example

Opening YouTube means communicating with computers that may be thousands of kilometers away.

---

## System Design Relevance

Every distributed system ultimately relies on the Internet to connect machines.

---

## Interview Notes

The Internet itself is just a network of networks.

---

# IP Address

## Concept

An IP Address uniquely identifies a machine on a network.

Examples:

```text
192.168.1.5
142.250.183.46
```

---

## Mental Model

```text
House Address
      ↓
IP Address
```

Without an IP address, data would not know where to go.

---

## Practical Example

Amazon servers have IP addresses.

DNS eventually converts:

```text
amazon.com
```

into an IP address.

---

## System Design Relevance

Every request ultimately travels to an IP address.

Load balancers, databases, servers, and services all communicate using IP addresses.

---

## Interview Notes

A domain name is for humans.

An IP address is for machines.

---

# Private IP vs Public IP

## Concept

### Private IP

Used inside local networks.

Examples:

```text
192.168.x.x
10.x.x.x
172.16.x.x
```

### Public IP

Assigned by an ISP.

Visible to the Internet.

---

## Mental Model

```text
Private IP = Apartment Number

Public IP = Building Address
```

---

## Practical Example

Your laptop:

```text
192.168.1.5
```

Amazon sees:

```text
49.x.x.x
```

(the router's public IP)

---

## System Design Relevance

NAT and network architecture depend on understanding public and private IPs.

---

## Interview Notes

Servers generally cannot see your private IP.

They usually see your public IP.

---

# ISP (Internet Service Provider)

## Concept

An ISP connects your network to the Internet.

Examples:

- Jio
- Airtel
- ACT
- BSNL

---

## Mental Model

The ISP is the bridge between your local network and the rest of the Internet.

---

## Practical Example

Without an ISP:

```text
Laptop
 ↓
Router
```

You have a local network but no Internet access.

---

## System Design Relevance

Every Internet-facing application depends on ISP infrastructure.

---

## Interview Notes

The ISP is not the Internet.

It provides access to the Internet.

---

# Router

## Concept

A router forwards traffic between networks.

Responsibilities:

1. Connect local devices.
2. Forward traffic to the Internet.
3. Forward responses back to the correct device.
4. Perform NAT.

---

## Mental Model

```text
Laptop
Phone
TV
 ↓
Router
 ↓
Internet
```

The router acts like a receptionist directing traffic.

---

## Practical Example

When Amazon responds, the router determines which device should receive the response.

---

## System Design Relevance

Routing is one of the fundamental concepts behind distributed systems.

---

## Interview Notes

Routers typically forward packets.

They do not need to understand application-level data.

---

# DNS (Domain Name System)

## Concept

DNS converts domain names into IP addresses.

Example:

```text
amazon.com
      ↓
54.x.x.x
```

DNS does not verify that a website is alive.

It only maps names to IP addresses.

---

## Mental Model

DNS is the Internet's contact book.

```text
Mom
 ↓
Phone Number
```

Similarly:

```text
amazon.com
 ↓
IP Address
```

---

## Practical Example

DNS lookup flow:

```text
Browser Cache
      ↓
OS Cache
      ↓
DNS Resolver
      ↓
DNS Hierarchy
```

---

## System Design Relevance

DNS caching significantly reduces latency and infrastructure load.

---

## Interview Notes

DNS resolution happens before any TCP connection can be established.

---

# Packets

## Concept

Data is broken into smaller units called packets before being transmitted across a network.

---

## Mental Model

```text
Large File
     ↓
Many Small Boxes
```

Instead of sending one giant object, send many smaller pieces.

---

## Practical Example

A video is transmitted as thousands of packets.

---

## System Design Relevance

Packet-based communication enables scalable and fault-tolerant networking.

---

## Interview Notes

Packets can:

- Be lost
- Arrive out of order
- Be duplicated

---

# TCP (Transmission Control Protocol)

## Concept

TCP provides reliable communication between computers.

Responsibilities:

- Reliable Delivery
- Ordering
- Duplicate Detection
- Retransmission

---

## Mental Model

TCP is a tracked courier service.

Every packet is monitored until delivery is confirmed.

---

## Practical Example

Without TCP:

```text
1 2 3 4
```

might arrive as:

```text
1 4 2 3
```

or:

```text
1 2 4
```

TCP fixes these issues.

---

## System Design Relevance

Critical systems such as:

- Banking
- Payments
- APIs
- File Uploads

rely on TCP.

---

## Interview Notes

TCP prioritizes reliability over speed.

---

# TCP 3-Way Handshake

## Concept

Before communication starts:

```text
Client ------ SYN ------> Server

Client <--- SYN-ACK ----- Server

Client ------ ACK ------> Server
```

After this:

```text
TCP Connection Established
```

---

## Mental Model

```text
Client: Can you hear me?

Server: Yes.
Can you hear me?

Client: Yes.
```

---

## Practical Example

A browser cannot send an HTTP request until the TCP handshake succeeds.

---

## System Design Relevance

Every HTTPS request depends on a TCP connection first.

---

## Interview Notes

If TCP handshake fails:

- TLS cannot start.
- HTTP requests cannot be sent.

---

# Ports

## Concept

A port is a logical communication endpoint managed by the operating system.

The IP identifies the machine.

The port identifies the application.

---

## Mental Model

```text
IP Address = Apartment Building

Port = Apartment Number
```

---

## Practical Example

```js
app.listen(3000);
```

Meaning:

> Deliver traffic arriving on port 3000 to this process.

---

## System Design Relevance

Ports allow multiple services to run on the same machine.

Examples:

- HTTPS → 443
- MySQL → 3306
- PostgreSQL → 5432
- Node.js → 3000

---

## Interview Notes

Two processes cannot listen on the same port simultaneously.

Otherwise the OS would not know which process should receive incoming traffic.

---

# NAT (Network Address Translation)

## Concept

NAT allows multiple devices to share a single public IP address.

---

## Mental Model

```text
Multiple Apartments
        ↓
One Building Address
```

---

## Practical Example

```text
192.168.1.5:54321
      ↓
49.x.x.x:62000

192.168.1.6:54321
      ↓
49.x.x.x:62001
```

The router maintains a mapping table.

---

## System Design Relevance

NAT enables billions of devices to share limited IPv4 addresses.

---

## Interview Notes

NAT often rewrites source ports to maintain uniqueness.

---

# TCP Connections

## Concept

A TCP connection is uniquely identified by:

```text
Source IP
Source Port
Destination IP
Destination Port
```

---

## Mental Model

Each connection has a unique identity card.

---

## Practical Example

```text
49.x.x.x:62000
      ↓
54.x.x.x:443
```

---

## System Design Relevance

This allows a server to handle millions of users on the same port.

---

## Interview Notes

A server handling one million users is usually handling one million TCP connection entries.

Not one million ports.

---

# HTTPS & TLS

## Concept

HTTPS = HTTP + TLS Encryption

TLS provides:

- Encryption
- Authentication
- Integrity

---

## Mental Model

Instead of sending messages in plain text, both parties communicate using a shared secret.

---

## Practical Example

Flow:

```text
DNS
 ↓
TCP Handshake
 ↓
TLS Handshake
 ↓
HTTP Request
 ↓
HTTP Response
```

---

## System Design Relevance

Every modern web application depends on TLS.

---

## Interview Notes

Remember:

```text
TCP
 ↓
TLS
 ↓
HTTP
```

TLS cannot start without TCP.

HTTP cannot start without TLS (for HTTPS).
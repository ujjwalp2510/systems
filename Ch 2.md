# Chapter 1.5 - System Thinking Foundations

These are not purely networking concepts.

These are the engineering insights that emerge from networking and form the foundation of System Design thinking.

---

# DNS Caching

## Concept

DNS lookups are relatively expensive compared to reading data from memory.

To avoid repeatedly querying DNS servers, DNS results are cached at multiple layers.

Common cache layers:

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

## Mental Model

Before asking the Internet for information:

```text
Check Local Memory First
```

Always avoid expensive work if the answer is already available.

---

## Practical Example

When you visit:

```text
google.com
```

multiple times, your browser often already knows the IP address.

Instead of contacting DNS servers again, it uses the cached result.

This makes the website load faster.

---

## System Design Relevance

This is usually the first real caching example engineers encounter.

The same principle later appears in:

```text
Browser Cache
↓
DNS Cache
↓
CDN Cache
↓
Redis Cache
↓
Application Cache
```

A large part of System Design is:

> Avoid repeating expensive work.

---

## Interview Notes

Caching improves:

- Latency
- Scalability
- Infrastructure Cost

DNS caching is one of the earliest examples of distributed caching.

---

# Why TCP Exists

## Concept

The Internet itself does not guarantee:

- Delivery
- Ordering
- Duplicate Prevention

Packets may:

- Be lost
- Arrive out of order
- Be duplicated

TCP exists to provide reliability on top of an unreliable network.

---

## Mental Model

TCP assumes:

```text
The Network Is Unreliable
```

and builds mechanisms to make communication reliable.

---

## Practical Example

Suppose:

```text
Packet1
Packet2
Packet3
Packet4
```

Without TCP:

```text
Packet1
Packet4
Packet2
Packet3
```

might arrive.

Or:

```text
Packet3
```

might never arrive.

TCP detects and fixes such issues.

---

## System Design Relevance

This idea appears everywhere in distributed systems.

If the network can fail:

- Requests can fail
- Messages can fail
- Services can fail

Which leads to concepts like:

- Retries
- Idempotency
- Fault Tolerance
- Message Queues
- Eventual Consistency

---

## Interview Notes

One of the most important engineering assumptions:

```text
Networks Are Unreliable
```

Design systems assuming failures will happen.

---

# Default Ports

## Concept

Certain protocols have standard ports that clients automatically assume.

Examples:

| Protocol | Default Port |
|-----------|-------------|
| HTTP | 80 |
| HTTPS | 443 |

---

## Mental Model

The browser already knows where standard services are expected to be available.

---

## Practical Example

These two URLs are effectively identical:

```text
https://amazon.com
```

```text
https://amazon.com:443
```

The browser automatically assumes:

```text
443
```

for HTTPS.

However:

```text
http://localhost:3000
```

requires specifying:

```text
3000
```

because 3000 is not a default HTTP port.

---

## System Design Relevance

Production systems often look like:

```text
Internet
    ↓
Port 443
(Nginx / Load Balancer)
    ↓
Port 3000
(Node.js App)
```

Understanding default ports helps explain reverse proxies and load balancers.

---

## Interview Notes

Users rarely type:

```text
:443
```

because browsers automatically use the default HTTPS port.

---

# Connection vs Listening Port

## Concept

A server listens on a port.

That does not mean only one user can connect.

A single listening port can handle millions of active TCP connections.

---

## Mental Model

```text
One Door
     ↓
Millions Of Visitors
```

Port 443 is the door.

TCP connections are the visitors.

---

## Practical Example

Amazon may listen on:

```text
54.x.x.x:443
```

Millions of users connect to the same port.

The operating system differentiates connections using:

```text
Source IP
Source Port
Destination IP
Destination Port
```

Each connection has a unique combination.

---

## System Design Relevance

This concept is fundamental to understanding:

- Web Servers
- Reverse Proxies
- Load Balancers
- API Gateways
- Nginx

---

## Interview Notes

A common misconception:

```text
1 Port = 1 User
```

Incorrect.

The correct understanding:

```text
1 Listening Port
+
Millions Of TCP Connections
```

---

# Resource Bottlenecks

## Concept

Every system consumes resources.

When traffic grows, one or more resources eventually become bottlenecks.

Common resources:

- CPU
- RAM
- Network Bandwidth
- File Descriptors
- Database Connections

---

## Mental Model

Always ask:

```text
What resource grows
per request?

What resource grows
per connection?
```

---

## Practical Example

### Chat Server

Millions of users are connected.

Most users are idle.

Likely bottlenecks:

```text
RAM
↓
File Descriptors
↓
Network
↓
CPU
```

---

### Video Streaming Platform

Millions of users are downloading video.

Likely bottleneck:

```text
Network Bandwidth
```

---

### Compute-Heavy API

Each request performs expensive calculations.

Likely bottleneck:

```text
CPU
```

---

## System Design Relevance

This is one of the most important System Design habits.

Do not immediately ask:

```text
Which technology should I use?
```

First ask:

```text
What resource is being consumed?
```

---

## Interview Notes

Junior engineers often ask:

```text
What is the bottleneck?
```

Senior engineers ask:

```text
What resource grows as traffic grows?
```

The answer reveals the bottleneck.

---

# Key Takeaways From Chapter 1.5

1. Cache whenever expensive work is repeated.
2. Networks are unreliable by default.
3. TCP exists because the Internet cannot guarantee reliability.
4. Standard protocols use default ports.
5. One listening port can serve millions of users.
6. Connections are identified using a TCP 4-tuple.
7. Every scaling problem is ultimately a resource problem.
8. Always identify what resource grows with traffic.
9. System Design is largely the art of managing resource consumption.
10. Think in terms of constraints, not technologies.
# Chapter 3: HTTP, Reverse Proxy, Authentication & Reliability

## HTTP

After the following layers are established:

```text
DNS
↓
TCP
↓
TLS (HTTPS)
```

the browser can finally communicate with the server using HTTP.

HTTP is an application-layer protocol that defines how clients and servers exchange requests and responses.

---

## Why HTTP Exists

A TCP connection only provides a reliable communication channel.

It does not answer:

```text
Which page do you want?
What action do you want to perform?
Who is making the request?
What data is being sent?
```

HTTP provides a standard structure for this communication.

---

# HTTP Request Structure

An HTTP request consists of:

```text
Request Line
Headers
Body (Optional)
```

Example:

```http
GET /product/iphone HTTP/1.1

Host: amazon.com
Authorization: Bearer <JWT>
Accept-Language: en
```

---

## Request Line

Contains:

```text
HTTP Method
Route / Resource
HTTP Version
```

Example:

```http
GET /product/iphone HTTP/1.1
```

Meaning:

```text
Method = GET
Resource = /product/iphone
Version = HTTP/1.1
```

---

## Headers

Headers contain metadata about the request.

Examples:

```http
Authorization: Bearer <JWT>
Accept-Language: en
User-Agent: Chrome
Host: amazon.com
```

Used for:

```text
Authentication
Language Preferences
Browser Information
Caching
Content Negotiation
```

---

## Body

Contains actual data sent to the server.

Example:

```http
POST /login

{
  "email": "abc@gmail.com",
  "password": "secret"
}
```

Typically used with:

```text
POST
PUT
PATCH
```

---

# HTTP Methods

HTTP methods communicate intent.

## GET

Used to retrieve data.

```http
GET /product/iphone
```

Expected behavior:

```text
Read data
Do not modify state
```

---

## POST

Used to create resources.

```http
POST /orders
```

Expected behavior:

```text
Create something new
```

---

## PUT

Used to replace an existing resource.

```http
PUT /users/123
```

Expected behavior:

```text
Replace entire resource
```

---

## PATCH

Used to partially update a resource.

```http
PATCH /users/123
```

Expected behavior:

```text
Update specific fields
```

---

## DELETE

Used to remove a resource.

```http
DELETE /wishlist/10
```

Expected behavior:

```text
Delete resource
```

---

# HTTP Response Structure

An HTTP response consists of:

```text
Status
Headers
Body
```

Example:

```http
HTTP/1.1 200 OK

Content-Type: text/html

<html>
 ...
</html>
```

---

## Status

Indicates success or failure.

Examples:

```text
200 OK
404 Not Found
500 Internal Server Error
```

---

## Headers

Metadata about the response.

Examples:

```text
Content-Type
Cache-Control
Set-Cookie
```

---

## Body

Actual response data.

Examples:

```text
HTML
JSON
Images
Videos
PDFs
```

---

# Browser Rendering

The server does NOT render webpages.

The server sends:

```text
HTML
CSS
JavaScript
JSON
```

The browser performs:

```text
Parse HTML
↓
Build DOM
↓
Build CSSOM
↓
Create Render Tree
↓
Layout
↓
Paint
↓
Display
```

---

## Backend Responsibilities

```text
Business Logic
Authentication
Authorization
Database Access
Generating Responses
```

---

## Frontend Responsibilities

```text
Rendering UI
Handling User Interaction
DOM Updates
Animations
```

---

# Reverse Proxy

A reverse proxy sits between clients and application servers.

Example:

```text
Client
↓
Nginx :443
↓
Node.js :3000
```

---

## Why Use A Reverse Proxy

### TLS Termination

Nginx can handle:

```text
HTTPS
Certificates
TLS Handshakes
```

and forward traffic internally.

---

### Routing

```text
/api/users
↓
Node Service

/payments
↓
Go Service

/recommendations
↓
Python Service
```

---

### Load Balancing

```text
Nginx
↓
Node1
Node2
Node3
```

Requests can be distributed among multiple servers.

---

### Hide Internal Architecture

Clients only see:

```text
amazon.com
```

not:

```text
:3000
:3001
:3002
```

---

# Health Checks

Load balancers need to know whether a server is healthy.

Example:

```text
Nginx
↓
GET /health
↓
Node Server
```

Responses:

```text
200 OK → Healthy
```

or

```text
No Response / 500 → Unhealthy
```

If unhealthy, traffic is not routed there.

---

# Authentication In Distributed Systems

Problem:

```text
Nginx
↓
Node1
Node2
Node3
```

User logs in through Node1.

Next request may hit Node3.

How does Node3 know who the user is?

---

# JWT (JSON Web Token)

Flow:

```text
Credentials
↓
Server Verification
↓
JWT Created
↓
Browser Stores JWT
```

Future requests:

```http
Authorization: Bearer <JWT>
```

---

## Stateless Authentication

Every server can verify the JWT independently.

```text
Browser
↓
Node1

Browser
↓
Node3
```

No session sharing required.

---

# JWT Misconception

JWT is usually NOT encrypted.

JWT is typically:

```text
Header
Payload
Signature
```

Base64 encoded.

Meaning:

```text
Readable
But Not Safely Modifiable
```

---

# JWT Security

Security comes from the signature.

Server creates:

```text
Payload
+
Secret Key
↓
Signature
```

If payload changes:

```text
Payload ≠ Signature
```

Verification fails.

---

## Secret Key

Known only to the server.

```text
Server → Knows Secret Key
Client → Does NOT Know Secret Key
```

---

# Session-Based Authentication

Alternative approach:

```text
Browser
↓
Session ID
↓
Redis / Database
↓
User Data
```

Server performs a lookup on every request.

---

# Distributed Systems Reliability

A successful TCP delivery does NOT guarantee business success.

Example:

```text
Request Reaches Server
↓
Server Crashes Before Saving
```

TCP succeeded.

Business operation failed.

---

# Timeouts

Important rule:

```text
Timeout ≠ Failure
```

A timeout may mean:

```text
Request Never Reached Server

OR

Request Failed

OR

Request Succeeded But Response Was Lost
```

Client cannot distinguish these cases.

---

# Duplicate Request Problem

Example:

```http
POST /orders
```

Server creates:

```text
Order #500
```

Response is lost.

Client retries.

Without protection:

```text
Order #501
Order #502
```

may also be created.

---

# Idempotency Key

A unique identifier generated by the client.

Example:

```http
POST /orders

Idempotency-Key: abc123
```

---

## First Request

```text
abc123
↓
Order #500 Created
```

Server stores:

```text
abc123 → Order #500
```

---

## Retry

Client sends:

```http
POST /orders

Idempotency-Key: abc123
```

again.

Server detects:

```text
Already Processed
```

and returns:

```text
Order #500
```

instead of creating a new order.

---

# Purpose Of Idempotency

To answer:

```text
Are these two requests actually the same business action?
```

---

# State Reconciliation

Suppose:

```text
Order Created Successfully
```

but the response never reaches the client.

Client is uncertain.

Solution:

```text
Query Current State
```

Examples:

```http
GET /orders
```

```http
GET /payments/{id}
```

The database becomes the source of truth.

---

# Key Principle

```text
The source of truth is the database,
not the response received by the client.
```

---

# Defense In Depth

Never rely on a single layer of protection.

## Frontend

```text
Disable Buttons
Show Loading States
Prevent Double Clicks
```

---

## Backend

```text
Idempotency Keys
Validation
Rate Limiting
```

---

## Database

```text
Transactions
Unique Constraints
Locks
```

All layers work together to maintain correctness.
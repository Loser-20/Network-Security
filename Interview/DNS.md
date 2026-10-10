---

# How DNS Finds a Website That Does Not Exist on My Private DNS Servers

Many administrators assume that if a DNS record does not exist on their private DNS server, the lookup will fail immediately. In reality, this is usually not the case.

Suppose your clients are configured to use the following DNS servers:

```text
10.10.10.10
10.10.10.20
```

A user attempts to access:

```text
www.google.com
```

Neither DNS server contains a local record for Google.

## Step 1: Client Queries Preferred DNS

The client first sends the request to:

```text
10.10.10.10
```

DNS Query:

```text
What is the IP address of www.google.com?
```

---

## Step 2: DNS Checks Local Database

The DNS server checks:

- Host Records (A Records)
- CNAME Records
- Conditional Forwarders
- Cached Lookups

If no matching record exists, DNS proceeds to the next step.

---

## Step 3: DNS Uses Forwarders

If Forwarders are configured, the DNS server forwards the request to another DNS server.

Example:

```text
Forwarder 1 : 8.8.8.8
Forwarder 2 : 1.1.1.1
```

Flow:

```text
Client
   |
   v
10.10.10.10
   |
   v
8.8.8.8
   |
   v
Google DNS Records
   |
   v
Returns IP Address
```

The answer is then returned to the client and stored in cache.

---

## Step 4: DNS Uses Root Hints (When No Forwarder Exists)

If no Forwarders are configured, Windows DNS uses Root Hints.

Root Hint servers know where to find Top-Level Domain (TLD) servers such as:

```text
.com
.org
.net
.gov
```

Flow:

```text
Client
   |
   v
10.10.10.10
   |
   v
Root DNS Server
   |
   v
.com DNS Server
   |
   v
google.com Authoritative DNS
   |
   v
Returns Google's IP
```

---

## Step 5: Response Is Cached

After DNS receives the answer, it stores the result in its cache.

Example:

```text
www.google.com
 =
142.250.x.x
```

Future requests may be answered immediately from cache without performing another Internet lookup.

Benefits:

- Faster response time
- Reduced Internet queries
- Lower DNS server workload

---

## What Happens If 10.10.10.10 Fails?

Suppose the primary DNS server is unavailable.

The client attempts:

```text
10.10.10.10
```

No response.

The client then tries:

```text
10.10.10.20
```

If the secondary server can resolve the request using its own forwarders or root hints, the lookup succeeds.

Flow:

```text
Client
   |
   X
10.10.10.10 (Unavailable)

Client
   |
   v
10.10.10.20
   |
   v
Forwarder / Root Hints
   |
   v
Google DNS Records
   |
   v
Response Returned
```

---

## Important DNS Concept

A secondary DNS server is **not normally used simply because the primary DNS server does not contain a record**.

The primary DNS server first attempts:

1. Local DNS Zones
2. DNS Cache
3. Conditional Forwarders
4. Forwarders
5. Root Hints

Only when the DNS server is unavailable, unreachable, or unable to respond will the client typically try the secondary DNS server.

---

## Real-World Example

Internal Record:

```text
printer01.company.local
```

Stored on:

```text
10.10.10.10
```

Public Website:

```text
www.microsoft.com
```

Not stored locally.

Resolution Process:

```text
printer01.company.local
    |
    v
Resolved Locally

www.microsoft.com
    |
    v
Forwarder (8.8.8.8)
    |
    v
Internet DNS
    |
    v
Microsoft IP Returned
```

This allows a single DNS server to provide both:

- Internal name resolution
- Public Internet name resolution

---

# DNS Query Resolution Order

Windows DNS generally follows the order below:

```text
1. Hosts File
2. DNS Cache
3. Local DNS Zones
4. Conditional Forwarders
5. Forwarders
6. Root Hints
7. DNS Resolution Failure
```

This sequence helps administrators understand where a DNS query is being resolved and which component should be checked during troubleshooting.

---
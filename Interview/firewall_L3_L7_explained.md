
# Why Firewall Rules Are Applied at Layer 3 (L3) and Layer 7 (L7)

Firewalls enforce security based on the information available in network packets. Layers 3 and 7 provide the most actionable data for security decisions. Below is a detailed technical explanation.

## 🔹 Layer 3 (Network Layer) — IP-Based Firewalling
Layer 3 contains IP addressing information, which allows firewalls to manage traffic based on *where* communication is occurring. At this layer, the firewall can inspect:
- Source & destination IP addresses
- Subnets and routing paths
- Protocol types (TCP, UDP, ICMP)

This makes L3 the earliest point at which meaningful filtering decisions can be made. It helps answer questions like:
- **Who is trying to communicate?** (IP identity)
- **From which network or segment?**
- **To what destination?**

L3 rules are crucial for:
- Network segmentation between VLANs
- Blocking or allowing traffic between subnets
- Basic access control (e.g., allowing only TCP/443)
- Geo‑IP blocking and routing enforcement

Since L3 provides routing-level insight, it forms the foundation for all upstream firewall decisions and reduces traffic load before deeper inspection occurs.

---

## 🔹 Layer 7 (Application Layer) — Application & Content Filtering
Layer 7 provides visibility into the *application* and *content* inside the traffic. Modern firewalls (NGFWs) perform Deep Packet Inspection (DPI) to identify applications even if they’re running on non-standard ports.

Firewalls inspect:
- HTTP/HTTPS
- DNS, SSH, FTP, SMTP
- Application signatures (YouTube, Teams, Zoom, Dropbox)
- Malware payloads
- TLS-inspected content (if enabled)

L7 rules answer:
- **What application is in use?**
- **Is the traffic safe or malicious?**
- **Does the content violate policy?**

L7 allows enforcement of:
- URL/content filtering
- Malware detection & IPS
- SaaS access control
- User-identity-based rules

Because applications today often tunnel over HTTPS, Layer 7 is the only reliable way to distinguish legitimate traffic from malicious or shadow IT traffic.

---

## 🔹 Why Not Other Layers?
### Layer 1 & 2
Physical and data link layers deal with electrical signaling and MAC addresses — too low-level for security logic.

### Layer 4
Although port numbers (TCP/UDP) live at L4, they are enforced as part of L3 rule sets (IP + port). Hence, L4 is *included* but not treated as its own firewall policy tier.

### Layers 5 & 6
Session and presentation layers do not provide meaningful data for firewall enforcement. They are typically internal to applications.

---

## 🔹 Final Summary
Firewalls apply rules at **L3 and L7** because:
- **L3 identifies where the traffic is coming from and going to (IP identity).**
- **L7 identifies what the traffic actually contains (application/content awareness).**
- These layers contain the only actionable information relevant for enforcing security.

Other OSI layers either provide too little context or lack relevance for access policy decisions.

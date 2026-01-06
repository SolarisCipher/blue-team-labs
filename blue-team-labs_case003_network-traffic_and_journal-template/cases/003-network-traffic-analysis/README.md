# Case #003 — Network traffic analysis: DNS failure signaled by ICMP “Port Unreachable”

**Type:** Network troubleshooting / incident triage  
**Focus:** DNS over UDP (port 53) + ICMP Destination Unreachable  
**Environment:** Training/lab scenario (sanitized)

## Executive summary
Customers reported that a website would not load and they received a **“destination port unreachable”** error.  
Traffic analysis shows **UDP packets sent to a DNS server**, followed by **ICMP Destination Unreachable** messages indicating **“udp port 53 unreachable.”** This strongly suggests that DNS resolution was failing because the destination DNS service was unavailable, blocked, or overwhelmed.

## Incident details
- **Time observed:** 13:24:36.098564  
- **How it was detected:** customer complaints and internal reproduction (website failed to load)
- **Tool used:** `tcpdump` (packet capture) on the affected network segment/host

## What we expected to see (normal flow)
1. Client sends a **DNS query** to a DNS server using **UDP/53** (sometimes TCP/53 for larger responses).
2. DNS server replies with the **IP address** for the requested domain.
3. Client connects to the web server (typically **TCP/443** for HTTPS) and loads the page.

## What we actually saw (from the capture)
- Client sent **UDP packets** to the DNS server (DNS query attempt).
- The network returned **ICMP Destination Unreachable** with the message **“udp port 53 unreachable.”**
- Result: DNS lookup failed, so the browser could not obtain the destination IP for the website, and the page could not load.

### Why ICMP matters here
“udp port 53 unreachable” typically means the destination host is reachable, but **nothing is listening on UDP port 53**, or a device in the path is actively rejecting that traffic.

## Likely causes (ranked)
1. **DNS service down** on the DNS server (crash, misconfiguration, stopped service).
2. **Firewall/ACL blocking or rejecting UDP/53** (policy change, misrule).
3. **DoS/DDoS against the DNS service** causing unavailability (overload), consistent with the incident hypothesis.
4. **Routing/NAT issues** affecting reachability to the DNS resolver.

> Important: with only this evidence, we can confidently say “DNS resolution failed due to UDP/53 being unreachable,” while “DoS/DDoS” is a strong hypothesis that should be validated with additional telemetry.

## Immediate containment and recovery actions
- Switch clients to a **secondary DNS resolver** (if available) or a known-good resolver (policy-dependent).
- Verify the DNS service health:
  - service status, CPU/memory, query rate, logs
- Check perimeter controls:
  - firewall/ACL rules for UDP/53, recent changes, deny logs
- If attack suspected:
  - enable rate limiting / filtering upstream where possible
  - engage ISP/provider or DDoS protection if available

## Detection and prevention ideas
- Alert on spikes in **DNS query failures** (SERVFAIL/timeouts) and **ICMP Destination Unreachable** related to DNS.
- Baseline normal DNS traffic and set thresholds for anomaly detection.
- Implement resilient DNS design (redundant resolvers, anycast, upstream protection, proper monitoring).

## Lessons learned
Packet-level triage can quickly pinpoint **where** a failure occurs (DNS stage vs web stage).  
Mapping symptoms (“site not loading”) to protocol steps (DNS → HTTPS) reduces guesswork and speeds incident response.

---
**Disclaimer:** Educational content for legal lab environments. No credentials, personal data, or flags included.

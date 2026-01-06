# Case #001 — Web Redirect via DNS/HTTP (“yummyrecipesforme”)

**Type:** Web security / network traffic triage  
**Focus:** DNS resolution + HTTP request sequence + suspicious redirect  
**Environment:** Training/lab scenario (sanitized)

## Executive summary
Users reported that a recipe website prompted them to download a file in order to access “free recipes.”  
Network traffic shows initial DNS resolution and HTTP activity for `yummyrecipesforme.com`, followed shortly by a new DNS resolution for a similar-looking domain (`greatrecipesforme.com`) and a new HTTP connection. This pattern is consistent with a malicious redirect / lure to a look‑alike domain and potential malware download attempt.

## Observations (from traffic)
- **DNS** resolution for `yummyrecipesforme.com` returned **203.0.113.22** (destination IP).
- Shortly after, the client initiated an **HTTP** connection and sent a **GET** request.
- A later DNS query resolved `greatrecipesforme.com` to **192.0.2.172**, followed by a new connection from a different ephemeral client port.

> Notes: DNS uses **port 53** (UDP/TCP). Client **source ports** (e.g., 52444/56378) are ephemeral and can change per connection.

## Indicators of compromise (IOCs)
**Domains**
- `yummyrecipesforme.com`
- `greatrecipesforme.com` (look‑alike domain)

**IPs**
- `203.0.113.22`
- `192.0.2.172`

## Why this looks suspicious
- **User prompt to download a file** is a classic malware delivery tactic.
- **Look‑alike domain** (great vs yummy) suggests typosquatting or an intentional redirect.
- **Short time gap** between the first site visit and the second DNS+HTTP sequence is consistent with an automated redirect chain or a scripted download lure.

## Recommended containment (quick wins)
1. **Block domains/IPs** at DNS filtering / secure web gateway (and add to an allow/deny policy as appropriate).
2. **Disable downloads** from the affected domains via browser policy where possible.
3. **Isolate affected endpoints** that attempted the download, and run an EDR/AV scan.

## Recommended remediation (root cause)
- If this is your site: investigate for **web compromise** (CMS/plugin updates, suspicious admin accounts, injected scripts, malicious ads).
- If this is user browsing: enforce **DNS filtering**, **web content filtering**, and **browser download controls**.
- Run user awareness refresh: “no downloads required for website access”.

## Detection ideas
- Alert on **DNS queries** to newly seen / look‑alike domains.
- Alert on **HTTP downloads** (or unusual `Content-Disposition: attachment`) from domains not previously associated with downloads.
- Correlate: `DNS query → HTTP GET → download attempt` within a short time window.

## Lessons learned
Clear incident notes + a small set of IOCs (domains/IPs) makes triage faster.  
DNS + HTTP sequence analysis is a practical way to confirm suspicious redirects without relying only on user reports.

---
**Disclaimer:** Educational content for legal lab environments. No credentials, personal data, or flags included.

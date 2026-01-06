# Case #002 — DDoS/ICMP traffic analysis mapped to NIST CSF

**Type:** Network security / incident analysis  
**Focus:** ICMP-based disruption + response planning using NIST CSF  
**Environment:** Training/lab scenario (sanitized)

## Executive summary
This case documents analysis of a service disruption consistent with a **denial-of-service** event. The available evidence points to **abnormal ICMP activity** contributing to availability impact. The response is mapped to the **NIST Cybersecurity Framework (CSF)** to demonstrate structured thinking: Identify, Protect, Detect, Respond, Recover.

## What happened (high level)
- Users experienced service disruption / connectivity issues.
- Network observations indicated unusual ICMP traffic patterns around the time of impact.
- The incident was triaged and documented with containment and recovery recommendations.

## Evidence noted
- ICMP activity was observed during the disruption window (availability impact).
- DNS/service reachability was affected (symptom of availability disruption).
- As you continue through this course, you may use this template to record your findings after completing an activity or to take notes on what you've learned about a specific tool or concept. You can also use this chart as a way to practice applying the NIST framework to different situations you encounter.

## Timeline (example structure)
- **T0**: Disruption reported (user/system reports)
- **T0 + minutes**: Initial triage (scope, affected assets, basic checks)
- **T0 + 30–60 min**: Network review (ICMP anomalies, service reachability)
- **T0 + hours**: Containment (rate limiting / filtering) + monitoring
- **T0 + day**: Post-incident review and hardening actions

## NIST CSF mapping

### Identify
- Determine affected services, assets, and business impact (availability).
- Confirm scope: single host vs multiple services/network segment.

### Protect
- Ensure baseline protections are in place:
  - network ACLs / firewall policies
  - DDoS protection where available (ISP/CDN/WAF depending on service)
  - secure configuration management

### Detect
- Enable and review:
  - network monitoring (ICMP rate, interface saturation)
  - alerts for traffic anomalies
  - logs from firewall/router/IDS if available

### Respond
- Containment:
  - rate limit ICMP where appropriate
  - block abusive sources (careful with spoofing risk)
  - engage ISP/provider if upstream mitigation is needed
- Communication:
  - notify stakeholders with clear status + ETA updates
  - document all actions and timestamps

### Recover
- Validate service restoration and monitor for recurrence.
- Capture lessons learned:
  - what signals were missing?
  - what automation/alerts would reduce time-to-detect?
  - update playbooks and runbooks

## Recommended actions (practical)
1. Implement **traffic baselining** and alerts (anomaly detection at the network edge).
2. Establish **DDoS escalation path** (who to call, provider contacts, pre-approved actions).
3. Add **rate limiting** / filtering policies carefully tested in a lab/staging environment.
4. Improve **logging and retention** for network devices to support faster triage.

---
**Disclaimer:** Educational content for legal lab environments. No credentials, personal data, or flags included.

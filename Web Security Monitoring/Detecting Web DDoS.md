# Room: Detecting Web DDoS

**Platform:** TryHackMe

**Path:** SOC Level 1

**Status:** Completed

---

# Objective

The objective of this room was to understand how Denial-of-Service (DoS) and Distributed Denial-of-Service (DDoS) attacks work, identify their indicators using log analysis and Splunk, and learn common defensive techniques used to mitigate these attacks.

---

# Topics Covered

* DoS vs DDoS
* Motivations Behind DDoS Attacks
* Splunk Log Analysis
* Detection Indicators
* Application-Level Defenses
* Network & Infrastructure Defenses
* Web Application Firewall (WAF)
* Content Delivery Network (CDN)

---

# DoS vs DDoS

## DoS (Denial of Service)

* Launched from a single source.
* Attempts to exhaust the target's resources.
* Makes services unavailable to legitimate users.

## DDoS (Distributed Denial of Service)

* Launched from multiple compromised systems (Botnet).
* Generates a much larger volume of traffic.
* More difficult to detect and mitigate than a traditional DoS attack.

---

# Detection Using Splunk

Indicators that may suggest a DoS or DDoS attack include:

* Sudden spikes in HTTP requests.
* Large numbers of requests within a few seconds.
* Multiple requests targeting the same endpoint.
* Traffic originating from numerous IP addresses (DDoS).
* Abnormal request rates compared to normal traffic.

Splunk can be used to identify these patterns and investigate abnormal web activity.

---

# Application-Level Defenses

## Secure Development Practices

Applications should validate user input to prevent abuse and resource exhaustion.

Proper input validation helps reduce the risk of attackers sending oversized or malicious requests designed to overwhelm the application.

---

## Challenges

Applications can verify legitimate users before processing requests by using:

* CAPTCHA
* JavaScript Challenges

These techniques help distinguish between real users and automated bots.

---

# Network & Infrastructure Defenses

## Content Delivery Network (CDN)

A CDN improves both performance and security by:

* Caching content
* Serving users from edge servers
* Reducing load on the origin server
* Load balancing
* Absorbing large traffic volumes
* Providing visibility into traffic patterns and geographic distribution

---

## Web Application Firewall (WAF)

A WAF inspects incoming HTTP requests and decides whether to:

* Allow
* Challenge
* Block

Modern WAFs rely on security rules and threat intelligence to detect malicious traffic.

Example:

Rate limiting can restrict requests to sensitive endpoints such as:

* /login
* /register

to reduce brute force and DoS attempts.

---

## Large-Scale Mitigation

Cloud-based protection services can:

* Absorb massive traffic volumes.
* Distribute requests across multiple servers.
* Maintain service availability during large attacks.

---

# Bypassing Security Measures

Attackers may attempt to evade defenses by:

* Appending random query parameters to bypass CDN caching.
* Changing User-Agent strings.
* Spoofing Referrer headers.
* Using geographically distributed sources.

Understanding these techniques helps analysts recognize sophisticated attacks.

---

# Commonly Targeted Resources

Frequently targeted web resources include:

* /login
* /search
* /api
* /register
* /signup
* /contact
* /feedback
* /cart
* /checkout

These endpoints often require authentication, database queries, session management, or payment processing, making them attractive targets.

---

# Key Takeaways

* DoS attacks originate from a single source, while DDoS attacks involve multiple systems.
* Splunk is valuable for detecting abnormal request patterns.
* Traffic spikes and repeated requests are strong indicators of potential attacks.
* Secure coding practices help reduce application-layer risks.
* CDNs and WAFs provide essential protection against modern DDoS attacks.
* Attackers may attempt to bypass security controls using various evasion techniques.

---

# SOC Perspective

SOC Analysts monitor web traffic for sudden spikes, repeated requests, unusual request rates, and attacks targeting critical endpoints. By combining SIEM analysis, web server logs, and network telemetry, analysts can quickly identify denial-of-service attacks, determine their impact, and coordinate mitigation efforts with security and infrastructure teams.

---

# Personal Notes

Topics to review later:

* Splunk searches for DDoS detection
* Rate Limiting Strategies
* CDN Architecture
* WAF Rule Tuning
* Botnet Detection
* Layer 7 DDoS Attacks

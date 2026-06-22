# Detecting Web Attacks

## Objective

The objective of this room was to understand common web attacks, learn how to detect them using web server logs and network traffic analysis, and understand the role of Web Application Firewalls (WAFs) in defending web applications.

---

## Topics Covered

* Client-Side Attacks
* Server-Side Attacks
* Web Server Log Analysis
* Network Traffic Analysis
* Web Application Firewall (WAF)

---

## Client-Side Attacks

### Cross-Site Scripting (XSS)

* Injects malicious JavaScript into web pages.
* Executes in the victim's browser.
* Can steal cookies, session tokens, or manipulate web pages.

### Cross-Site Request Forgery (CSRF)

* Tricks an authenticated user into performing unwanted actions.
* Exploits the user's existing authenticated session.

---

## Server-Side Attacks

### Brute Force

* Attempts many username and password combinations.
* Often identified by repeated failed login attempts.

### SQL Injection

* Injects malicious SQL queries into application input.
* May allow attackers to read or modify database contents.

### Command Injection

* Executes operating system commands through vulnerable applications.
* Can lead to full system compromise.

---

## Detecting Attacks

### Web Server Logs

Reviewed `access.log` files to identify:

* Suspicious requests
* Unusual URLs
* Repeated login attempts
* Requests containing payloads
* Abnormal HTTP status codes

---

### Network Traffic Analysis

Used packet analysis techniques to identify:

* Suspicious HTTP requests
* Unusual client behavior
* Indicators of web attacks
* Malicious communication patterns

---

## Web Application Firewall (WAF)

A WAF acts as the first line of defense for web applications.

Its primary functions include:

* Filtering HTTP/HTTPS traffic
* Blocking malicious requests
* Applying security rules
* Detecting common web attacks

---

## Key Takeaways

* Web attacks can target both clients and servers.
* Web server logs provide valuable evidence during investigations.
* Network traffic analysis complements log analysis.
* WAFs help prevent many common web attacks before they reach the application.
* Detection rules are essential for identifying malicious behavior.

---

## SOC Perspective

As a SOC Analyst, understanding web attacks is important for investigating alerts, analyzing web server logs, identifying Indicators of Compromise (IOCs), and determining whether suspicious activity represents a real attack or normal user behavior.

---

## Personal Notes

Topics to review later:

* Advanced SQL Injection Detection
* XSS Payload Analysis
* WAF Rule Tuning
* HTTP Log Analysis
* Detection Engineering for Web Applications

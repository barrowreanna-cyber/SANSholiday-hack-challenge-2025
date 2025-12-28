# Objective 2 – It's All About Defang

## Challenge Overview

The purpose of this challenge was to evaluate and utilize a newly developed SOC phishing triage tool to ensure that all Indicators of Compromise (IOCs) were properly identified, handled, and blocked.

Multiple suspicious emails were reported and the goal was to:

- identify malicious indicators
- extract domains, URLs, IPs, and email addresses
- verify regex detection capability
- ensure everything was safely defanged

---

## Environment & Tools

- SOC phishing triage tool
- email body and email headers
- custom regex patterns

---

## Key Concepts

- phishing analysis
- indicator extraction
- regex pattern creation
- defanging malicious artifacts

---

## Regex Patterns Used

### Domain extraction
To extract all domains, including root domains and subdomains appearing in both headers and the email body, the following regex pattern was used:
\b(?:[a-zA-Z0-9-]+\.)+[a-zA-Z]{2,}\b


This pattern captures:
- Primary domains (e.g., icicleinnovations.mail)
- Subdomains (e.g., mail.icicleinnovations.mail)
- Internal domains (e.g., dosisneighborhood.corp)
- During IOC extraction, the internal domain dosisneighborhood.corp was initially identified through regex-based parsing of email headers. As this domain belongs to the organization and does not represent attacker-controlled infrastructure, it was manually removed during analyst validation.

### URL Extraction
To extract full URLs present in the phishing email, including executable download links, the following regex pattern was applied:
https?://[^\s]+

### Email Address Extraction

[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}
This pattern captured all valid email address formats present in the email.
### IP Address Extraction
IP addresses found within the email headers were extracted using this regex pattern:
\b(?:\d{1,3}\.){3}\d{1,3}\b

Additional tweaks were required to remove internal, non-malicious indicators such as IP addresses in the private range ,as these belonged to the organization’s own infrastructure and did not represent attacker activity.

##  Defanging process

To prevent accidental execution:

| Original | Defanged |
|--------|---------|
| `https` | `hxxps` |
| `.` | `[.]` |
| `@` | `[@]` |
| `://` | `[://]` |
 # Chained SED command to successfully defang all identified IOCs:
s/\./[.]/g; s/@/[@]/g; s/http/hxxp/g; s/:\//[://]/g

## Result 
Successfully:

- extracted every IOC
- verified tool still detected each pattern
- removed false positives (internal domains)
- safely defanged malicious indicators




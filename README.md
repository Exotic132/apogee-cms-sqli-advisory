# Apogee-cms-sqli-advisory
Security advisory and technical documentation for SQL injection in Apogee CMS `module.php` (CAN-2026-2033679)

# Security Advisory: Unauthenticated SQL Injection in Apogee CMS

## Overview
* **Software:** Apogee CMS
* **Vendor:** Apogee Information Systems
* **Component:** `module.php`
* **Vulnerability:** SQL Injection ([CWE-89](https://cwe.mitre.org/data/definitions/89.html))
* **Tracking ID:** CAN-2026-2033679
* **Status:** Disclosed (90-day vendor grace period elapsed)

## Summary
An unauthenticated SQL injection vulnerability exists in Apogee CMS within `module.php`. The application fails to sanitize or parameterize input derived from URL path segments before running database queries.

Because `module.php` processes dynamic routing across the entire application, the vulnerability is not restricted to specific GET/POST parameters or fixed endpoints. Instead, it can be triggered through any directory path or sub-folder handled by the core router. 

Examples of reachable path structures include:
* `/gr/[SQLi]`
* `/en/[SQLi]`
* `/[any-directory]/[SQLi]`

## Impact
An unauthenticated remote attacker can inject arbitrary SQL queries via simple HTTP GET requests. Successful exploitation allows attackers to extract database content, enumerate tables, or potentially bypass authentication depending on the database user privileges.

## Remediation
The database logic in `module.php` should be updated to use prepared statements with parameterized queries (such as PHP PDO or MySQLi) rather than concatenating raw path inputs directly into SQL queries.

## Disclosure Timeline
* **Reported:** Initial vulnerability details sent to Apogee Information Systems.
* **CVE Reserved:** Candidate ID CAN-2026-2033679 reserved via the MITRE CNA-LR portal.
* **Grace Period Ended:** 90 days passed without vendor response or patch confirmation.
* **Public Disclosure:** Public advisory published for community tracking and defensive awareness.

## Proof of Concept (PoC)

The vulnerability can be verified by supplying standard SQL syntax characters or boolean logic within any directory path handled by `module.php`.

### Example Syntax:
- `https://example.com/gr/'/`
- `https://example.com/gr/' OR 1=1--/`
- `https://example.com/en/' AND '1'='1/`

### Observed Behavior:
An unescaped quote triggers a database query syntax error (or alters page response logic), indicating that path inputs are concatenated directly into the backend SQL statement.

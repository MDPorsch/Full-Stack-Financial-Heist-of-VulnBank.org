# Full-Stack Financial Heist of VulnBank.org

Date: February 27, 2026

Attacker Machine: Kali Linux

Tools: Burp Suite Professional, Nmap, Dirsearch, Dig, FoxyProxy

Target: https://vulnbank.org


Executive Summary
This lab documents a multi-stage application compromise. By bypassing Cloudflare WAF protections and exploiting critical flaws in Authentication, Input Validation, and Business Logic, I successfully moved from unauthenticated reconnaissance to System Administrator status


 # Phase 1: Infrastructure & Reconnaissance
Step 1: Proxy Configuration

    Action: Configured FoxyProxy to route Firefox traffic through Burp Suite (127.0.0.1:8080).
    Technical Impact: Established a "Man-in-the-Middle" (MITM) position to intercept and manipulate raw HTTP requests.
    Documentation Update: Interception active; environment ready for traffic analysis.

Step 2: Automated Directory Discovery

    Action: Executed dirsearch -u https://vulnbank.org.
    Technical Impact: Identified hidden endpoints: /api/docs/ (Info Leak), /console (Admin Access), and /.wp-config.php.swp (Source Disclosure).
    Documentation Update: Attack surface mapped; identified multiple entry points beyond the UI.

Step 3: DNS & WAF Fingerprinting

    Action: Executed dig vulnbank.org and nmap -p- -sS -T4 172.67.134.11 -Pn.
    Technical Impact: Confirmed the target is behind Cloudflare CDN/WAF. Identified edge proxy ports (2052-2096).
    Documentation Update: Infrastructure identified as Cloudflare-protected; exploitation requires WAF-evasive techniques.

# Phase 2: Vulnerability Discovery (Unauthenticated)
Step 4: Weak Password Policy

    Action: Registered a test account with low-entropy credentials.
    Technical Impact: Confirmed [CWE-521]. The application lacks complexity requirements, enabling Brute-Force and Credential Stuffing.
    Documentation Update: Finding #1: Insecure Authentication Policy.

Step 5: Input Sanitization Failure

    Action: Injected special characters (< > ' ") into the /register endpoint.
    Technical Impact: [CWE-20] Confirmed the backend processes raw metacharacters.
    Documentation Update: Finding #2: Improper Input Validation (Gateway to XSS/SQLi).

# Phase 3: Active Exploitation (SQL Injection)
Step 6: SQLi Probing (Error-Based)

    Action: Inputted h@h.com' into the email field.
    Technical Impact: Triggered an HTTP 500 Internal Server Error.
    Documentation Update: Finding #3: SQL Injection confirmed on Email parameter.

Step 7: Authentication Bypass

    Action: Injected the tautology payload: a' or 1=1 --.
    Technical Impact: Bypassed the login logic by forcing the SQL query to evaluate as TRUE.
    Documentation Update: Finding #4: Successful Authentication Bypass via SQLi.

# Phase 4: Business Logic & Financial Exploitation
Step 8: Password Brute-Force (Sniper Attack)

    Action: Used Burp Intruder to brute-force a validated username.
    Technical Impact: Identified valid credentials via HTTP Status/Length anomalies.
    Documentation Update: Finding #5: Lack of Rate Limiting/Account Lockout.

Step 9: Mass Assignment (Balance Manipulation)

    Action: Used Burp Repeater to inject "balance": 99000000 into the JSON registration body.
    Technical Impact: [CWE-915] The API trusted client-side input for sensitive financial attributes.
    Documentation Update: Finding #6: Digital Currency Minting via Mass Assignment.

Step 10: In-Flight Proxy Tampering

    Action: Intercepted a live registration in Burp Proxy and edited the balance field before it hit the server.
    Technical Impact: Proved total lack of Server-Side Integrity Checks.
    Documentation Update: Finding #7: Insecure Client-Side Trust.

# Phase 5: Vertical Privilege Escalation
Step 11: System Administrator Takeover

    Action: Injected "role": "admin" and "isAdmin": true into the registration metadata.
    Technical Impact: [CWE-269] Successfully escalated privileges from a standard user to a System Administrator.
    Documentation Update: Finding #8: Vertical Privilege Escalation.

Step 12: Final Access Validation

    Action: Logged in and accessed the /console and Admin Dashboard.
    Technical Impact: Confirmed "God Mode" access, allowing for total user and transaction management.
    Documentation Update: TOTAL SYSTEM COMPROMISE ACHIEVED.

# Conclusion
The VulnBank.org breach was made possible by a catastrophic failure in Server-Side Validation. By trusting the data sent via the proxy, the application allowed for the total corruption of the financial ledger and the unauthorized creation of administrative accounts.

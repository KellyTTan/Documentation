# Median Alerts Found on Invoice Pre-Approval [OWASP Zap]
The following are median alerts that are found after conducting scans on invoice pre-approval pages. The two main alerts found are CSRF and Cross Domain Misconfiguration. The preventions for these alerts are listed for reference.


## Cross-Site Request Forgery (CSRF)
- Aka one-click attack or session riding
- Tricks already authenticated users to execute unwanted actions ​
- This may lead to:
    - Changed passwords
    - Data theft
    - Damage client relationships 

#### Preventions: 
  - If framework has built-in CSRF protection, make sure it’s being used
      - Add CSRF tokens to all state changing requests (if CSRF protection is not build-in) 
      - Validate these requests on the backend 
  - GET requests for state changing operations should not be used 
  - Consider implementing different methods of mitigations
      - User interaction-based protection 
      - SameSite Cookie Attribute 
      
***
## Cross Domain Misconfiguration 
- Occurs when CORS is incorrectly configured on the server 
- This leads to:
    - Malicious domains being able to send requests 
    - Allows credientials to be sent to untrusted soures 
        - like cookies for examples 
- May increase vulnerability to CSRF attacks 
#### Preventions:
  - Make sure sensitive data is not available in an unauthenticated manner 
  - ensure Access-Control-Origin is not excessively permissive 
      - This should never be set to "*" if the resource contains sensitive information 
      - **OR** remove all CORS headers entirely 

***
## Other Vulnerability Alerts 
- **Vulnerable JS library**: AngularJS version 1.4.3 is vulnerable
    - Upgrade to the latest version 
- **X-Frame-Options Header Not Set**: Occurs when server doesn't return X-Frame-Options header
    - This can leave websites prone to clickjacking attacks 
    - Ensure it is set on all web apges returned by the site
- **Content Security Policy(CSP) Header Not Set**: CSP is an added layer of security 
    - It is used to detect and mitigate certain attacks 
    - Content-Security-Policy header must be properly configured

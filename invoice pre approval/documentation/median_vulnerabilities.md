# Median Alerts Found on Invoice Pre-Approval [OWASP Zap]
The following are median alerts that are found after conducting scans on invoice pre-approval pages. The two main alerts found are Cross-Site Request Forgery and Cross Domain Misconfiguration. Other medium alerts are listed below along with their preventions. 

## [M] Cross-Site Request Forgery (CSRF)
- Aka one-click attack or session riding
- Tricks already authenticated users to execute unwanted actions with the help of social engineering 
- This may lead to:
    - Changed passwords
    - Data theft
    - Damage client relationships 
#### How it works:
1. The attacker sends an http request from the authenticated user's browser to the application 
2. The user will be tricked into accepting the request
3. The request will be sent from the user's browser to the target application 
4. The application will accept the request since it came from an authenticated user
#### Preventions: 
  - If framework has built-in CSRF protection, make sure it’s being used
      - Add CSRF tokens to all state changing requests (if CSRF protection is not build-in) 
      - Validate these requests on the backend 
  - GET requests for state changing operations **should not be used** 
  - Consider implementing different methods of mitigations
      - User interaction-based protection 
      - SameSite Cookie Attribute 
  - Upon commiting to a huge transaction, display an additional decision to the user such as entering a password. 
  - The CSRF attacker wouldn't know the password therefore the transaction would not take place.
#### Detection:
  - **Check if a unique identifier is sent with every HTTP request sent to the application** [[ref]](https://owasp.org/www-project-code-review-guide/reviewing-code-for-csrf-issues)
    - Checking if the request has a valid session cookie is not enough
    - CSRF request **won't** have this valid unique identifier
    - "By checking the page rendering we need to see if any unique identifiers are appended to the links rendered by the application in the user’s browser. If there is no unique identifier relating to each HTTP request to tie a HTTP request to the user, we are **vulnerable**."  
***

## [M] Cross Domain Misconfiguration 
- Occurs when CORS (**Cross Origin Resource Sharing**) is incorrectly configured on the server 
- This leads to:
    - Malicious domains being able to send requests 
    - Allows credientials to be sent to untrusted soures 
        - like cookies for examples 
- May increase vulnerability to CSRF attacks
> CORS: An HTTP-header based mechanism that allows a server to indicate any origins (other than its own) from which a browser should permit loading resources.
> Examples of CORS headers that are the most important for security:
> - `Access-Control-Allow-Origin`
> - `Acess-Control-Allow-Credentials`
> - `Access-Control-Allow-Methods`
#### Preventions:
  - Make sure sensitive data is not available in an unauthenticated manner 
  - Ensure `Access-Control-Origin` is not excessively permissive 
      - This should **never** be set to "*" if the resource contains sensitive information 
      - **OR** remove all CORS headers entirely 
***

## [M] Vulnerable JS library
- AngularJS version 1.4.3 is vulnerable
- Some of the vulnerabilities from using this version include [[ref]](https://security.snyk.io/package/npm/angular/1.4.3):
    - XXS
    - Prototype pollution
    - DoS
#### Preventions:
- It is recommended to upgrade from AngularJS to Angular as all the new updates from Google will only go to Angluar
    - Latest version of Angular: **v15**
    - "One of the keys to a successful upgrade is to do it incrementally, by running the two frameworks side by side in the same application, and porting AngularJS components to Angular one by one." (Upgrading from AngularJS to Angular tuturoial can be found [here](https://angular.io/guide/upgrade))
- The latest version or AngularJS is **1.8.3**
***

## [M] X-Frame-Options Header Not Set
- Occurs when server doesn't return `X-Frame-Options` header
- This can leave websites prone to clickjacking attacks 
#### Preventions:
- Ensure it is set on all web pages returned by the site
***

## [M] [Content Security Policy(CSP) Header Not Set](https://github.com/KellyTTan/Documentation/blob/main/ctagold/documentation/ctagold_vulnerabilities.md#m-content-security-policy-csp-header-not-set)

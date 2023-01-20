# Alerts Genereated from CTA Gold Scans 
The following are new alerts that have come up on the CTA Gold Scans for Prospect, Secondary Pre Approval, and Distribution pages.
This report features our first high alert (SQL Injection) and a few other median vulnerabilities. 

## [H] SQL Injection
- Occurs when a SQL query is injected as input data by an unauthorized user
    - The injected query affects the execution of predefined SQL commands
- Successful SQL injection may lead to:
    - Reading sensitive data from databases
    - Modifying database data (insert/update/delete)
    - Execute admin operations 
#### Preventions: 
- Never concatenate data received from users
- ALways use the most specific and restrictive data types 
    - For instance, if your web app has a method for retrieving an instance based on its integer ID, don't have the method accept String as a parameter
- Create **prepared statements** (parameterized queries)
    - Allows database to distinguish between code and data
    - One of the best and easiest forms of prevention
    - Ensures the attacker is not able to change the intent of a query
    - Placeholders are used instead of the inputed values, ensuring the values themselves are never part of the text of the query
    - Crating a prepared statement example:
        - `String user = "user"; // comes from user`
        - `String password = "password"; // comes from user, gets hashed, etc`
        - `String query = SELECT * FROM users WHERE user = ? AND password = ?";`
        - `PreparedStatement statement = con.prepareStatement(query);`
        - `myStmt.setString(1, user);`
        - `myStmt.setString(2, password);`
- Use properly constructed stored procedures
- Use allow list (whitelist) input validation
    - Defines what IS authorized and everything else that is not defined is **not** authorized 
- Ensure applications do not return detailed error messages [[ref]](https://web.archive.org/web/20151005235207/http://www.net-security.org/dl/articles/IntegrigyIntrotoSQLInjectionAttacks.pdf)
    - The more information found in error messages, the more useful it is to the attacker. 
***

## [M] Content Security Policy (CSP) Header Not Set
- `Content-Security-Policy` header restricts how resources (JavaScript, CSS< etc.) are being loaded
- CSP adds a layer of security to detect and mitigate certain types of attacks including:
    - XXS
    - Data Injection 
- Can be used to specify which sources are allowed.
- Can restrict content by specifying server origins and script endpoints
#### Preventions:
- Set `Content-Securirty-Policy` to the correct values to control what resources the user is allowed to load for the page 
- Examples of common use cases:
    - restricts to content that comes from only the site's own origin
`Content-Security-Policy: default-src 'self'`
    - allow content from a trusted domain and all its subdomains 
`Content-Security-Policy: default-src 'self' example.com *.example.com`
***

## [M] Missing Anti-Clickjacking Header
- Alert appears when `X-Frame-Options` or`Content-Securty-Policy` header is not being used 
- `X-Frame-Options` header is used to indicate whether or not if a browser is allowed to render a page in a `<frame>`, `<iframe>`, `<embed>` or `<object>`
    - This is used to avoid click-jacking attacks by ensuring the content is no embedded into other sites
#### Preventions:
- Ensure all sites uses one of the headers
***

## [M] Vulnerable JS Library
- jquery version 1.11.3 is vulnerable 
#### Preventions: 
- Upgrade to the latest version of jquery 
- Latest version: 3.6.2

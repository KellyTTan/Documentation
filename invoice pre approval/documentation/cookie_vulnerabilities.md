# Alerts: Web Cookies [OWASP Zap]
The following are common cookie alerts that have been detected across multiple scans on OWASP Zap. These are considered low level alerts, but should still be something to be aware of as you can never be too safe. 
## Cookie No HttpOnly Flag 
- Occurs when a cookie has been set **without** HttpOnly flag
    - Means cookies can be accessed by Javascript 
    - If a session cookie is access, session hijacking may be possible 
 - HttpOnly flag helps mitigate the risk of client-side scripts from accessing cookies.
#### Preventions:
 - Ensure HttpOnly flag is set for all cookies.
 
## Cookie Without Secure Flag
- Occurs when a cookie is set without the secure flag 
    - Therefore, the cookie can be accessed via encrypted connections 
- Secure attribute tells the browser to only send the cookie if the request is being sent over a secure channel
#### Preventions:
- Ensure secure flag is set for cookies containing sensitive information 
    - Including session tokens 
    - Should always be passed using an encrypted channel

## Cookie without SameSite Attribute or Set as None
- Occurs when a cookie doesn’t have the SameSite attribute.
- Occurs when SameSite attribute is set to the value ‘none’.
#### Preventions:
- Ensure all cookies have SameSite attribute and are either set to **‘lax’** or **‘strict’**

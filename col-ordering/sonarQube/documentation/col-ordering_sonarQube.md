# Col-Ordering SonarQube Documentation 
This document lists the Security Hostspots for Col-Ordering. SonarQube did not list any vulnerabilities on this page.
- [H] [CSRF](https://github.com/KellyTTan/Documentation/edit/main/col-ordering/sonarQube/documentation/test.md#h-cross-sit-request-forgery-csrf)
- [L] [Insecure Configuration](https://github.com/KellyTTan/Documentation/edit/main/col-ordering/sonarQube/documentation/test.md#l-insecure-configuration) 
- [L] [Others](https://github.com/KellyTTan/Documentation/edit/main/col-ordering/sonarQube/documentation/test.md#l-others) 

## Security Hotspots 
### [H] Cross-Sit Request Forgery (CSRF)
- An HTTP method is safe when used to perform a read-only operation, such as retrieving information. 
  - In contrast, an **unsafe HTTP method is used to change the state of an application**, for instance to update a user's profile on a web application.
- Common **safe** HTTP methods are `GET`, `HEAD`, or `OPTIONS`.
- Common **unsafe** HTTP methods are `POST`, `PUT` and `DELETE`.
- Allowing both safe and unsafe HTTP methods to perform a specific operation on a web application could impact its security
  - For example CSRF protections are most of the time only protecting operations performed by unsafe HTTP methods.
- **There is a risk if:**
  - HTTP methods are not defined at all for a route/controller of the application.
  - Safe HTTP methods are defined and used for a route/controller that can change the state of an application.
#### Sensitive Code Example
```
@RequestMapping("/delete_user")  // Sensitive: by default all HTTP methods are allowed
public String delete1(String username) {
// state of the application will be changed here
}
@RequestMapping(path = "/delete_user", method = {RequestMethod.GET, RequestMethod.POST}) // Sensitive: both safe and unsafe methods are allowed
String delete2(@RequestParam("id") String id) {
// state of the application will be changed here
}
```
#### Solution 
- For all the routes/controllers of an application, the authorized HTTP methods should be explicitly defined
- Safe HTTP methods should only be used to perform read-only operations.
- Compliant solution:
```
@RequestMapping("/delete_user", method = RequestMethod.POST)  // Compliant
public String delete1(String username) {
// state of the application will be changed here
}
@RequestMapping(path = "/delete_user", method = RequestMethod.POST) // Compliant
String delete2(@RequestParam("id") String id) {
// state of the application will be changed here
}
```
***
### [L] Insecure Configuration 
- Delivering code in production with **debug features activated is security-sensitive**. It has led in the past to the following vulnerabilities:
  - [CVE-2013-2006](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2013-2006): OpenStack Identity (Keystone) Grizzly 2013.1.1 logs admin tokens and LDAP passwords in plaintext when debug mode is enabled.
  - [CVE-2015-5306](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2015-5306): OpenStack Ironic Inspector allowed remote attackers to access the Flask console and execute arbitrary Python code by triggering an error when debug mode was enabled.
  - [CVE-2018-1999007](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-1999007): A cross-site scripting vulnerability exists in Jenkins 2.132 and 2.121.1 that allows attackers with the ability to control the existence of some URLs in Jenkins to define JavaScript that would be executed in another user's browser when that other user views HTTP 404 error pages while Stapler debug mode is enabled.
- An application's debug features enable developers to find bugs more easily and thus facilitate also the work of attackers. 
  - It often gives access to detailed information on both the system running the application and users.
- **There is a risk if:** 
  - The code or configuration enabling the application debug features is deployed on production servers.
  - The application runs by default with debug features activated. 
#### Sensitive Code Example
`Throwable.printStackTrace(...)` prints a Throwable and its stack trace to `System.Err` (by default) which is not easily parseable and can expose sensitive information:
```
try {
  /* ... */
} catch(Exception e) {
  e.printStackTrace();        // Sensitive
}
```
#### Solution 
- Do not enable debug features on production servers.

***
### [L] Others 


## Vulnerabilities 
SonarQube listed no vulnerabilities for col-ordering.

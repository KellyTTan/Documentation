# Common Security Hotspots from SonarQube
- [[H] Cross-Site Request Forgery (CSRF)]()
- [[L] Insecure Configuration]()
## [H] Cross-Site Request Forgery (CSRF)
### Make sure allowing safe and unsafe HTTP methods is safe.
- An HTTP method is safe when used to perform a read-only operation, such as retrieving information. 
  - In contrast, an **unsafe HTTP method is used to change the state of an application**, for instance to update a user's profile on a web application.
- Common **safe** HTTP methods are `GET`, `HEAD`, or `OPTIONS`.
- Common **unsafe** HTTP methods are `POST`, `PUT` and `DELETE`.
- Allowing both safe and unsafe HTTP methods to perform a specific operation on a web application could impact its security
  - For example CSRF protections are most of the time only protecting operations performed by unsafe HTTP methods.
- **There is a risk if:**
  - HTTP methods are not defined at all for a route/controller of the application.
  - Safe HTTP methods are defined and used for a route/controller that can change the state of an application.
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
### Make sure disabling Spring Security's CSRF protection is safe.
- A cross-site request forgery (CSRF) attack occurs when a trusted user of a web application can be forced, by an attacker, to perform sensitive actions that he didn't intend
  - This includes updating his profile or sending a message, more generally anything that can change the state of the application.
- The attacker can trick the user/victim to click on a link, corresponding to the privileged action, or to visit a malicious web site that embeds a hidden web request and as web browsers automatically include cookies
  - The actions can be authenticated and sensitive.
- There is a risk if:
  - The web application uses cookies to authenticate users.
  - There exist sensitive operations in the web application that can be performed when the user is authenticated.
  - The state / resources of the web application can be modified by doing HTTP POST or HTTP DELETE requests for example.
#### Solution
- Protection against CSRF attacks is strongly recommended to be activated by default for all unsafe HTTP methods with an unguessable CSRF token.
- All sensitive operations should not be performed with safe HTTP methods like GET which are designed to be used only for information retrieval.
- Compliant Solution:
  - Spring Security CSRF protection is enabled by default, do not disable it:
```
@EnableWebSecurity
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {

  @Override
  protected void configure(HttpSecurity http) throws Exception {
    // http.csrf().disable(); // Compliant
  }
}
```
***

## [L] Insecure Configuration
### To review: Make sure this debug feature is deactivated before delivering the code in production.
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
- Compliant solution:
  - Loggers should be used (instead of `printStackTrace`) to print throwables:
```
try {
  /* ... */
} catch(Exception e) {
  LOGGER.log("context", e); // Compliant
}
```
***

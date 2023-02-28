# Common Security Hotspots from SonarQube
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

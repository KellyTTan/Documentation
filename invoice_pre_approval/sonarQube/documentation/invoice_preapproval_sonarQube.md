# Invoice Pre-Approval SonarQube Documentation 
This document lists the Security Hostspots for Invoice Pre-Approval. SonarQube did not list any vulnerabilities on this page.
- [H] [CSRF](https://github.com/KellyTTan/Documentation/blob/main/invoice_pre_approval/sonarQube/documentation/invoice_preapproval_sonarQube.md#h-cross-sit-request-forgery-csrf)

## Security Hotspots 
### [H] Cross-Sit Request Forgery (CSRF)
#### (1) To review: Make sure allowing safe and unsafe HTTP methods is safe here.
```
 *
     * @param request
     * @param response
     * @return
     */
    @RequestMapping(value = "/error")
    public ModelAndView error(HttpServletRequest request, HttpServletResponse response) {
        // Appropriate HTTP response code (e.g. 404 or 500) is automatically set by Spring.
        // Here we just define response body.
        logger.error("/error was reached");
```
#### (2) To review: Make sure allowing safe and unsafe HTTP methods is safe here.
```
        mav.addObject("errorAttributes", errorAttributes);
        return mav;
    }
    @RequestMapping(value = "/error/denied")
    public ModelAndView errorDenied(HttpServletRequest request, HttpServletResponse response) {
        logger.error("reached /error/denied");
        // Appropriate HTTP response code (e.g. 404 or 500) is automatically set by Spring.
        // Here we just define response body.
```
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

## Vulnerabilities 
SonarQube listed no vulnerabilities for invoice pre-approval.

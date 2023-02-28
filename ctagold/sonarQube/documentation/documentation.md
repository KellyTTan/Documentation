# CTAGold SonarQube Documentation 
This document lists the Security Hostspots and vulnerabilities for ctagold. 2 vulnerabilities and 5 instances of CSRF were found.
- Security Hotspots: [H] [CSRF](https://github.com/KellyTTan/Documentation/blob/main/gco/sonarQube/documentation/gco_sonarqube.md#h-cross-site-request-forgery-csrf)
    - [Safe and unsafe HTTP methods](https://github.com/KellyTTan/Documentation/blob/main/gco/sonarQube/documentation/gco_sonarqube.md#to-review-make-sure-allowing-safe-and-unsafe-http-methods-is-safe-here)
    - [Spring Security's CSRF protection](https://github.com/KellyTTan/Documentation/blob/main/gco/sonarQube/documentation/gco_sonarqube.md#to-review-make-sure-disabling-spring-securitys-csrf-protection-is-safe-here)
- Vulnerabilities: 
    - [XML parsers should not be vulnerable to XXE attacks](https://github.com/KellyTTan/Documentation/blob/main/gco/sonarQube/documentation/gco_sonarqube.md#1-basic-authentication-should-not-be-used)
    - [Basic authentication should not be used](https://github.com/KellyTTan/Documentation/blob/main/gco/sonarQube/documentation/gco_sonarqube.md#2-xml-parsers-should-not-be-vulnerable-to-xxe-attacks)

## Security Hotspots 
### [H] Cross-Site Request Forgery (CSRF)
#### To review: Make sure allowing safe and unsafe HTTP methods is safe here.
(1) src/main/java/com/conti/cite/gold/controller/canada/salesreport/SalesReportController.java
```
    private static final Logger logger = LoggerFactory.getLogger(SalesReportController.class);
    @Autowired
    ReportService reportService;
    @RequestMapping(value = "/golddealerreport")
    public ModelAndView goldDealerReport(@AuthenticationPrincipal CGUserDetails loggedInUser) {
        ModelAndView mav = new ModelAndView();
        if (loggedInUser.getAuthorities().contains(new SimpleGrantedAuthority("CITE_CMS_ADOBE"))) {
            mav.setViewName("layout/salesreport_aem");
```
(2) src/main/java/com/conti/cite/gold/controller/canada/salesreport/SalesReportController.java
```
        mav.addObject("citePage", "../canada/reports/golddealerreport.jsp");
        return mav;
    }
    @RequestMapping(value = "/golddistributorreport")
    public ModelAndView goldDistributorReport(@AuthenticationPrincipal CGUserDetails loggedInUser) {
        ModelAndView mav = new ModelAndView();
        if (loggedInUser.getAuthorities().contains(new SimpleGrantedAuthority("CITE_CMS_ADOBE"))) {
            mav.setViewName("layout/salesreport_aem");
```
(3) src/main/java/com/conti/cite/gold/controller/canada/salesreport/SalesReportController.java
```
        mav.addObject("citePage", "../canada/reports/golddistributorreport.jsp");
        return mav;
    }
    @RequestMapping(value = "/goldadminreport")
    public ModelAndView goldAdminReport(@AuthenticationPrincipal CGUserDetails loggedInUser) {
        ModelAndView mav = new ModelAndView();
        if (loggedInUser.getAuthorities().contains(new SimpleGrantedAuthority("CITE_CMS_ADOBE"))) {
            mav.setViewName("layout/salesreport_aem");


```
(4) src/main/java/com/conti/cite/gold/controller/paymentletter/PaymentLetterController.java
```
        }
        mav.addObject("customerAccountNumber", loggedInUser.getCustomerDetails().getNumber());
        return mav;
    }
    @RequestMapping("/paymentletter/view/pdf/{fileName}")
    public void downloadPDFResource( HttpServletRequest request,
                                     HttpServletResponse response,
                                     @PathVariable("fileName") String fileName) {
        if (!fileName.endsWith(".pdf")) {
            fileName = fileName + ".pdf";
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
#### To review: Make sure disabling Spring Security's CSRF protection is safe here.
(5) src/main/java/com/conti/cite/gold/config/SecurityConfig.java
```
//        http.authorizeExchange()
//                .pathMatchers("/pub/**")
//                .permitAll()
//                .anyExchange().authenticated();
        http.csrf().disable();
        http.sessionManagement().sessionCreationPolicy(SessionCreationPolicy.ALWAYS);
        http.addFilterBefore(corsFilter, CsrfFilter.class);
    }
```
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

## Vulnerabilities 
### (1) Basic authentication should not be used
src/main/java/com/conti/cite/gold/config/WebServiceMessageSenderWithAuth.java
```
        Base64.Encoder enc = Base64.getEncoder();
        String userpassword = username + ":" + password; // change to a real user and password
        String encodedAuthorization = enc.encodeToString(userpassword.getBytes());
        connection.setRequestProperty("Authorization", "Basic " + encodedAuthorization);
```
- Use a more secure method than basic authentication.
- Basic authentication's only means of obfuscation is Base64 encoding. 
- Since Base64 encoding is easily recognized and reversed, it offers only the thinnest veil of protection to your users, and should not be used.

### (2) XML parsers should not be vulnerable to XXE attacks
src/main/java/com/conti/cite/gold/config/HadoopHiveConfig.java
```
 String configPath = getPath(configXMLPath);
        File configFile = new File(configPath);
        if (configFile.exists()) {
            DocumentBuilderFactory factory = DocumentBuilderFactory.newInstance();
            DocumentBuilder builder = factory.newDocumentBuilder();
            org.apache.hadoop.conf.Configuration config = HBaseConfiguration.create();
```
- Disable access to external entities in XML parsing.
- XML specification allows the use of entities that can be internal or external (file system / network access ...)  
    - This could lead to vulnerabilities such as confidential file disclosures or SSRFs.
- It is recommended to disable access to external entities and network access in general.

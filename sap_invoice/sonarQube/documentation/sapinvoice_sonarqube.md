
# SAP Invoice SonarQube Documentation 
This document lists the Security Hostspots and vulnerabilities for sap invoice. 4 vulnerabilities and 11 instances of CSRF were found.
- Security Hotspots: [H] [CSRF](https://github.com/KellyTTan/Documentation/blob/main/ctagold/sonarQube/documentation/documentation.md#h-cross-site-request-forgery-csrf)
    - [Safe and unsafe HTTP methods]()
    - [Spring Security's CSRF protection]()
- Vulnerabilities: 
    - [

## Security Hotspots 
### [H] Cross-Site Request Forgery (CSRF)
#### To review: [Make sure allowing safe and unsafe HTTP methods is safe here.](https://github.com/KellyTTan/Documentation/blob/main/references/sonarQube/security_hotspots.md#make-sure-allowing-safe-and-unsafe-http-methods-is-safe)
(1) src/main/java/com/conti/cite/sapinvoice/controller/fpx/FPXConfirmationController.java
```
        this.i18NService = i18NService;
    }
    //This rest api call need to configure at FPX to get the indirect AC response
    @RequestMapping(value = "/indirectAC", method = {RequestMethod.GET, RequestMethod.POST})
    public String indirectAC( HttpServletRequest httpRequest, RedirectAttributes redirectAttributes ) {
        logger.debug("indirect A0>>");
        FPXAuthorizationResponseDTO response = prepareFPXResponse(httpRequest);
```
(2) src/main/java/com/conti/cite/sapinvoice/controller/fpx/FPXConfirmationController.java
```
        //return "forward:combineAction?navigationPath=cite-plt-en-my%2Finvoice";
        return "redirect:" + cocUrl + "/coc/sapinvoice/pub/fpx/combineAction?navigationPath=cite-plt-en-my%2Finvoice";
    }
    @RequestMapping(value = "/combineAction", method = {RequestMethod.GET, RequestMethod.POST})
    public ModelAndView combineAC(@AuthenticationPrincipal CGUserDetails loggedInUser, HttpServletRequest httpRequest) {
        ModelAndView mav = new ModelAndView();
        if (loggedInUser.getAuthorities().contains(new SimpleGrantedAuthority("CITE_CMS_ADOBE"))) {
            mav.setViewName("wrapper_aem");
```
(3) src/main/java/com/conti/cite/sapinvoice/controller/fpx/FPXDirectController.java
```
        this.invoiceClearanceWSHandler = invoiceClearanceWSHandler;
    }
    //This rest api call need to configure at FPX to get the direct AC response
    @RequestMapping(value = "/directAC", method = {RequestMethod.GET, RequestMethod.POST})
    @ResponseBody
    public String directAC( HttpServletRequest httpRequest ) {
        logger.debug("direct A0>>");
        //TODO - need to remove
```
(4) src/main/java/com/conti/hellowww/controller/CiteErrorController.java
```
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
(5) src/main/java/com/conti/hellowww/controller/CiteErrorController.java
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

#### To review: [Make sure disabling Spring Security's CSRF protection is safe here.](https://github.com/KellyTTan/Documentation/blob/main/references/sonarQube/security_hotspots.md#make-sure-disabling-spring-securitys-csrf-protection-is-safe)
(6) src/main/java/com/conti/cite/sapinvoice/SecurityConfig.java
```
        // Require authentication for all requests
        http.authorizeRequests()
                .antMatchers("/pub/fpx/indirectAC", "/pub/fpx/directAC").permitAll()
                .anyRequest().authenticated();
        http.csrf().disable();
        http.sessionManagement().sessionCreationPolicy(SessionCreationPolicy.ALWAYS);
        http.addFilterBefore(corsFilter, CsrfFilter.class);
    }
```

### [M] Weak Cryptography
#### To review: Make sure this weak hash algorithm is not used in a sensitive context here.
(1) src/main/java/com/conti/cite/sapinvoice/service/ItellaInvoiceService.java
```
        return itellaURL.toString();
    }
    private String generateMD5Hash(String part1, String part2, String part3) {
        String temp = part1 + part2 + part3;
        return DigestUtils.md5Hex(temp);
    }
    private String buildSuffix(String part1, Map<String, String> urlParams) {
        String part2 = buildPart2(buildParamList(urlParams));
        String md5Hash = generateMD5Hash(part1, part2, KEYWORD);
```

### [L] Insecure Configuration 
#### To review: [Make sure this debug feature is deactivated before delivering the code in production.](https://github.com/KellyTTan/Documentation/blob/main/references/sonarQube/security_hotspots.md#to-review-make-sure-this-debug-feature-is-deactivated-before-delivering-the-code-in-production)
(1) src/main/java/com/conti/cite/sapinvoice/controller/BaseExcelReportController.java
```
			doc.write(response.getOutputStream());
			
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
	/*protected void generateExcel( List<StatementsItem> paymentList,
```
(2) src/main/java/com/conti/cite/sapinvoice/controller/fpx/FPXPkiImplementation.java
```
		FileReader pvtFileReader = null;
		try {
			pvtFileReader = new FileReader(pvtFile);
		} catch (FileNotFoundException e) {
			e.printStackTrace();
			pvtFileReader = null;
		}
		return pvtFileReader;
```
(3) src/main/java/com/conti/cite/sapinvoice/ws/handler/XmlInvoiceWSHandler.java
```
            xmlInvoiceResponse.setzFileData(response.getZFILEDATA());
            xmlInvoiceResponse.setzFileName(response.getZFILENAME());
            xmlInvoiceResponse.setzCreatedDate(response.getZCREATEDATE());
        }
        catch (Exception e) {
            e.printStackTrace();
            logger.error(e.getMessage(), e);
        }
        logger.info("Exit: retrieveXmlInvoices()");
        return xmlInvoiceResponse;
```
### [L] Others 
#### To review: Make sure not using rel="noopener" is safe here.
(1) src/main/webapp/citeNgApp/src/app/paymentlist/summary/paymentsummary/paymentsummary.component.html
```
  <div class="row">
    <div class="col-sm-4 col-md-6">
      By continue with FPX payment, mean you agreed with <a href="https://www.mepsfpx.com.my/FPXMain/termsAndConditions.jsp" target="_blank">FPX's Terms &amp; Conditions</a>
    </div>
    <div class="col-sm-4 col-md-4" style="text-align: right">
      <a class="ci-button-size-40" *ngIf="!submitSpinner" (click)="makePayment()">{{ labels["paymentBtn"] }}</a>
      <div *ngIf="submitSpinner">
        <mat-spinner [diameter]="30"></mat-spinner>
```

***
## Vulnerabilities 
### (1) Server certificates should be verified during SSL/TLS connections 
```
new X509TrustManager() {
    public java.security.cert.X509Certificate[] getAcceptedIssuers() {
        return null;
    }
    public void checkClientTrusted(
```
### (2) Server certificates should be verified during SSL/TLS connections 
```
        java.security.cert.X509Certificate[] certs, String authType) {
}
public void checkServerTrusted(
```

### (3) Weak SSL/TLS protocols should not be used
```
                    public boolean isClientTrusted(java.security.cert.X509Certificate[] chain) {
                        return true;
                    }
                }
        };
SSLContext sc = SSLContext.getInstance("SSL");
```
### (4) Server hostnames should be verified during SSL/TLS connections
```
sc.init(null, trustAllCerts, new java.security.SecureRandom());
HttpsURLConnection.setDefaultSSLSocketFactory(sc.getSocketFactory());
HttpsURLConnection.setDefaultHostnameVerifier(new HostnameVerifier() {
    public boolean verify(String hostname, SSLSession session) {
        return true;
```


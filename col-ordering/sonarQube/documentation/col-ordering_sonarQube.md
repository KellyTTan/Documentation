# Col-Ordering SonarQube Documentation 
This document lists the Security Hostspots for Col-Ordering. SonarQube did not list any vulnerabilities on this page.
- [H] [CSRF](https://github.com/KellyTTan/Documentation/blob/main/col-ordering/sonarQube/documentation/col-ordering_sonarQube.md#security-hotspots)
- [L] [Insecure Configuration](https://github.com/KellyTTan/Documentation/blob/main/col-ordering/sonarQube/documentation/col-ordering_sonarQube.md#l-insecure-configuration) 
- [L] [Others](https://github.com/KellyTTan/Documentation/blob/main/col-ordering/sonarQube/documentation/col-ordering_sonarQube.md#l-others) 

## Security Hotspots 
### [H] Cross-Sit Request Forgery (CSRF)
#### (1) To review: Make sure allowing safe and unsafe HTTP methods is safe here.
```
            }
        }
    }
    @Trace
    @RequestMapping(value="/customers/shipto", produces= MediaType.APPLICATION_JSON_VALUE)
    @ResponseBody
    public SelectableCiteCustomers getShipToCustomerList(@AuthenticationPrincipal CGUserDetails loggedInUser) {
        String customerNumber = loggedInUser.getCustomerDetails().getNumber();
        String salesOrg = loggedInUser.getCustomerDetails().getMarketDetails().getSalesOrganization();
```
#### (2) To review: Make sure allowing safe and unsafe HTTP methods is safe here.
```
     * 
     * @param request
     * @param response
     * @return
     */
    @RequestMapping(value = "/error")
    public ModelAndView error(@AuthenticationPrincipal CGUserDetails loggedInUser, HttpServletRequest request, HttpServletResponse response) {
        // Appropriate HTTP response code (e.g. 404 or 500) is automatically set by Spring. 
        // Here we just define response body.
    	//ServletWebRequest servletWebRequest = new ServletWebRequest(request);
       // Map<String, Object> errorAttributes = getErrorAttributes(servletWebRequest, true);
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
### [L] Insecure Configuration 
#### To review: Make sure this debug feature is deactivated before delivering the code in production.
```
        try {
            retVal = sdf.parse(dateString);
        } catch (Exception e) {
            // unable to parse the date
            e.printStackTrace();
            logger.debug("error parsing date: {}",dateString);
        }
        return retVal;
    }
```
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
### [L] Others 
#### (1) To review: Make sure not using resource integrity feature is safe here.
```
    	<meta http-equiv="X-UA-Compatible" content="IE=edge">
    	<meta name="viewport" content="width=device-width, initial-scale=1">
    	
		<title>contionlinecontact.com</title>
						
		<script src="https://ajax.googleapis.com/ajax/libs/jquery/1.11.3/jquery.min.js"></script>
		<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/js/bootstrap.min.js"></script>
		<link rel="stylesheet" type="text/css" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css"/>	
		
		<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/font-awesome/4.5.0/css/font-awesome.min.css">	
```
#### (2) To review: Make sure not using resource integrity feature is safe here.
```
    	<meta name="viewport" content="width=device-width, initial-scale=1">
    	
		<title>contionlinecontact.com</title>
						
		<script src="https://ajax.googleapis.com/ajax/libs/jquery/1.11.3/jquery.min.js"></script>
		<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/js/bootstrap.min.js"></script>
		<link rel="stylesheet" type="text/css" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css"/>	
		
		<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/font-awesome/4.5.0/css/font-awesome.min.css">		
			
		<link rel="stylesheet" type="text/css" href="/cite/resources/style/main-29de97ce.css"/>
```
- Fetching external resources, for example from a CDN, without verifying their integrity could impact the security of an application if the CDN gets compromised and resources are replaced by malicious ones. 
- Resources integrity feature will block resources inclusion into an application if the pre-computed digest of the expected resource doesn't match with the digest of the retrieved resource.
- **This is a risk if**
  - The resources are fetched from external CDNs.
#### Sensitive Code Example
```
<script src="https://cdnexample.com/script.js"></script> <!-- Sensitive -->
```
#### Solutions
- Implement resources integrity checks for all static resources 
  - (where "static" means that the resource's content doesn't change dynamically based on the browser)
- Use versioned resources instead of using "latest" version of the resources
- Compliant solution:
```
<script src="https://cdnexample.com/script.js" integrity="sha384-oqVuAfXRKap7fdgcCY5uykM6+R9GqQ8K/uxy9rx7HNQlGYl1kPzQho1wx4JwY8wC"></script> <!-- Compliant: integrity value should be replaced with the digest of the expected resource -->
```
***

## Vulnerabilities 
SonarQube listed no vulnerabilities for col-ordering.
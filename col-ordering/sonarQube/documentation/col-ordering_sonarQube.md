# Col-Ordering SonarQube Documentation 
This document lists the Security Hostspots for Col-Ordering. SonarQube did not list any vulnerabilities on this page.
- [H] [CSRF](https://github.com/KellyTTan/Documentation/blob/main/col-ordering/sonarQube/documentation/col-ordering_sonarQube.md#security-hotspots)
- [L] [Insecure Configuration](https://github.com/KellyTTan/Documentation/blob/main/col-ordering/sonarQube/documentation/col-ordering_sonarQube.md#l-insecure-configuration) 
- [L] [Others](https://github.com/KellyTTan/Documentation/blob/main/col-ordering/sonarQube/documentation/col-ordering_sonarQube.md#l-others) 

## Security Hotspots 
### [H] Cross-Sit Request Forgery (CSRF)
#### (1) To review: [Make sure allowing safe and unsafe HTTP methods is safe here.](https://github.com/KellyTTan/Documentation/blob/main/references/sonarQube/security_hotspots.md#make-sure-allowing-safe-and-unsafe-http-methods-is-safe)
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
#### (2) To review: [Make sure allowing safe and unsafe HTTP methods is safe here.](https://github.com/KellyTTan/Documentation/blob/main/references/sonarQube/security_hotspots.md#make-sure-disabling-spring-securitys-csrf-protection-is-safe)
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

***
### [L] Insecure Configuration 
#### (1) To review: [Make sure this debug feature is deactivated before delivering the code in production.](https://github.com/KellyTTan/Documentation/blob/main/references/sonarQube/security_hotspots.md#to-review-make-sure-this-debug-feature-is-deactivated-before-delivering-the-code-in-production)
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

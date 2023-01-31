# Zap SQL Injection Research 
The contents of this document are based on researching sql injection false positives from OWASP ZAP reports.
The **Notes from forums** section contain notable information taken from online discussions on how and why ZAP may be producing sql injection false positives.
It is reported that false positives for this specific vulnerability are quite common. 
Examples of different values injected by ZAP are also listed below. 
The links to the specific discussion forums can be found by clicking on [ref]


## Notes from forums 
#### How ZAP performs their SQL Injection attack: [[ref]](https://github.com/zaproxy/zaproxy/issues/3662)
>  1. Query a page 
>  2. Query the page with "query AND 1=1--"
>  3. Query the page with "query OR 1=1--" and look for differences.

#### Gzipped files can cause unreliable scans [[ref]](https://github.com/zaproxy/zaproxy/issues/1871)
> - Disable compression on the HTTP server or remove the `Accept-Encoding: gzip`, `deflate`, etc. header in the request 
> - Scans cannot be properly performed when content is gzipped

#### Possible reasons for false positives [[ref]](https://github.com/zaproxy/zaproxy/issues/3662#issuecomment-350826118)
> - When ZAP is doing an active scan for the form to create objects, it does not understand that creating them (or changing or deleting them, for that matter) will change the page showing a list of them. 
> - Therefore, when the first thread sends a payload, which, it expects, will change the page (as a result of an SQL injection), 
  and then another thread creates an objects thus changing the page, 
  then the first thread thinks it has succeeded, creating an alert.
> - This problem reliably disappears if the number of parallel scanning threads is reduced to 1.

#### Why ZAP may raise false positives [[ref]](https://security.stackexchange.com/questions/191758/false-positive-sql-injection-by-zap-with-adding-new-parameter-query)
> - ZAP quite possibly raises an alert simply because the response code is 200. 
> - It cannot know for sure if the query is read by the backend or not, therefore it gives it the confidence of medium.

#### Testing given the values on ZAP reports
> - Manually send the two requests (strings that Zap injected during active scan) and compare the responses 
***

## Examples of request that have been injected by Zap
When ZAP performs a SQL Injection attack, it querys the page with two different values and then compares the two outcomes.
The values that are injected for ctagold secondary preapproval page are: 
[123-456-7890%] and [123-456-7890XYZABCDEFGHIJ].
Some of the values that have been previously reported to be injected by ZAP include:
- [query AND 1=1 -- ] and [query OR 1=1 -- ]
- [query] and [query+AND+1%3D1+--+]
- [ZAP AND 1=1 -- ] and [ZAP AND 1=2 -- ]
- [A5343185' AND '1'='1' -- ] and [A5343185' OR '1'='1' -- ]
- [ZAP" AND "1"="1" -- ] and [ZAP" AND "1"="2" -- ]

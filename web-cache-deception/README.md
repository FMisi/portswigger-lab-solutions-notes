## Lab: Exploiting path mapping for web cache deception

Hint: File: /def.js

Identify a target endpoint

    In Burp's browser, log in to the application using the credentials wiener:peter.

    Notice that the response contains your API key.

Identify a path mapping discrepancy

    In Proxy > HTTP history, right-click the GET /my-account request and select Send to Repeater.

    Go to the Repeater tab. Add an arbitrary segment to the base path, for example change the path to /my-account/abc.

    Send the request. Notice that you still receive a response containing your API key. This indicates that the origin server abstracts the URL path to /my-account.

    Add a static extension to the URL path, for example /my-account/abc.js.

    Send the request. Notice that the response contains the X-Cache: miss and Cache-Control: max-age=30 headers. The X-Cache: miss header indicates that this response wasn't served from the cache. The Cache-Control: max-age=30 header suggests that if the response has been cached, it should be stored for 30 seconds.

    Resend the request within 30 seconds. Notice that the value of the X-Cache header changes to hit. This shows that it was served from the cache. From this, we can infer that the cache interprets the URL path as /my-account/abc.js and has a cache rule based on the .js static extension. You can use this payload for an exploit.

Craft an exploit

    In Burp's browser, click Go to exploit server.

    In the Body section, craft an exploit that navigates the victim user carlos to the malicious URL that you crafted earlier. Make sure to change the arbitrary path segment you added, so the victim doesn't receive your previously cached response:
    <script>document.location="https://YOUR-LAB-ID.web-security-academy.net/my-account/wcd.js"</script>

    Click Deliver exploit to victim. When the victim views the exploit, the response they receive is stored in the cache.

    Go to the URL that you delivered to carlos in your exploit:
    https://YOUR-LAB-ID.web-security-academy.net/my-account/wcd.js

    Notice that the response includes the API key for carlos. Copy this.

    Click Submit solution, then submit the API key for carlos to solve the lab.

## Lab: Lab: Exploiting path delimiters for web cache deception


Identify a target endpoint

    In Burp's browser, log in to the application using the credentials wiener:peter.

    Notice that the response contains your API key.

Identify path delimiters used by the origin server

    In Proxy > HTTP history, right-click the GET /my-account request and select Send to Repeater.

    Go to the Repeater tab. Add an arbitrary segment to the path. For example, change the path to /my-account/abc.

    Send the request. Notice the 404 Not Found response with no evidence of caching. This indicates that the origin server doesn't abstract the path to /my-account.

    Remove the arbitrary segment and add an arbitrary string to the original path. For example, change the path to /my-accountabc.

    Send the request. Notice the 404 Not Found response with no evidence that the response was cached. You'll use this response as a reference to help you identify characters that aren't used as delimiters.

    Right-click the request and select Send to Intruder.

    Go to the Intruder tab. Make sure the Sniper attack type is selected and add a payload position after /my-account as follows: /my-account§§abc.

    Go to the Payloads tab. Under Payload settings [Simple list], add a list of characters that may be used as delimiters.

    Under Payload encoding, deselect URL-encode these characters.

    Click Start attack. The attack runs in a new window.

    When the attack finishes, sort the results by Status code. Notice that the ; and ? characters receive a 200 response with your API key. All other characters receive the 404 Not Found response. This indicates that the origin server uses ; and ? as path delimiters.

Investigate path delimiter discrepancies

    Go to the Repeater tab that contains the /my-accountabc request.

    Add the ? character after /my-account and add a static file extension to the path. For example, update the path to /my-account?abc.js.

    Send the request. Notice that the response doesn't contain evidence of caching. This may indicate that the cache also uses ? as a path delimiter.

    Repeat this test using the ; character instead of ?. Notice that the response contains the X-Cache: miss header.

    Resend the request. Notice that the value of the X-Cache header changes to hit. This indicates that the cache doesn't use ; as a path delimiter and has a cache rule based on the .js static extension. You can use this payload for an exploit.

Craft an exploit

    In Burp's browser, click Go to exploit server.

    In the Body section, craft an exploit that navigates the victim user carlos to the malicious URL you crafted earlier. Make sure to change the arbitrary string, so the cache creates a unique key and carlos caches their account details instead of receiving your previously cached response:
    <script>document.location="https://YOUR-LAB-ID.web-security-academy.net/my-account;wcd.js"</script>

    Click Deliver exploit to victim. When the victim views the exploit, the response they receive is stored in the cache.

    Go to the URL that you delivered to carlos:
    https://YOUR-LAB-ID.web-security-academy.net/my-account;wcd.js

    Notice that the response includes the API key for carlos. Copy this.

    Click Submit solution, then submit the API key for carlos to solve the lab.

## Lab: Exploiting origin server normalization for web cache deception


Identify a target endpoint

    In Burp's browser, log in to the application using the credentials wiener:peter.

    Notice that the response contains your API key.

Investigate path delimiter discrepancies

    In Proxy > HTTP history, right-click the GET /my-account request and select Send to Repeater.

    Go to the Repeater tab. Change the path to /my-account/abc, then send the request. Notice the 404 Not Found response. This indicates that the origin server doesn't abstract the path to /my-account.

    Change the path to /my-accountabc, then send the request. Notice that this returns a 404 Not Found response with no evidence of caching.

    Right-click the message and select Send to Intruder.

    Go to the Intruder tab. Make sure the Sniper attack type is selected and add a payload position after /my-account as follows: /my-account§§abc.

    Go to the Payloads tab. Under Payload settings [Simple list], add a list of characters that may be used as delimiters. Under Payload encoding, deselect URL-encode these characters.

    Click Start attack. The attack runs in a new window.

    When the attack finishes, sort the results by Status code. Notice that only the ? character receives a 200 response with your API key. This indicates that the origin server only uses ? as a path delimiter. As ? is generally universally used as a path delimiter, move on to investigate normalization discrepancies.

Investigate normalization discrepancies

    In Repeater, remove the arbitrary abc string and add an arbitrary directory followed by an encoded dot-segment to the start of the original path. For example, /aaa/..%2fmy-account.

    Send the request. Notice that this receives a 200 response with your API key. This indicates that the origin server decodes and resolves the dot-segment, interpreting the URL path as /my-account.

    In Proxy > HTTP history, notice that the paths for static resources all start with the directory prefix /resources. Notice that responses to requests with the /resources prefix show evidence of caching.

    Right-click a request with the prefix /resources and select Send to Repeater.

    In Repeater, add an encoded dot-segment after the /resources path prefix, such as /resources/..%2fYOUR-RESOURCE.

    Send the request. Notice that the 404 response contains the X-Cache: miss header.

    Resend the request. Notice that the value of the X-Cache header changes to hit. This may indicate that the cache doesn't decode or resolve the dot-segment and has a cache rule based on the /resources prefix. To confirm this, you'll need to conduct further testing. It's still possible that the response is being cached due to a different cache rule.

    Modify the URL path after /resources to a arbitrary string as follows: /resources/aaa. Send the request. Notice that the 404 response contains the X-Cache: miss header.

    Resend the request. Notice that the value of the X-Cache header changes to hit. This confirms that there is a static directory cache rule based on the /resources prefix.

Craft an exploit

    Go to the Repeater tab that contains the /aaa/..%2fmy-account request. Attempt to construct an exploit as follows: /resources/..%2fmy-account. Send the request. Notice that this receives a 200 response with your API key and the X-Cache: miss header.

    Resend the request and notice that the value of the X-Cache header updates to hit.

    In Burp's browser, click Go to exploit server.

    In the Body section, craft an exploit that navigates the victim user carlos to a malicious URL. Make sure to add an arbitrary parameter as a cache buster, so the victim doesn't receive your previously cached response:
    <script>document.location="https://YOUR-LAB-ID.web-security-academy.net/resources/..%2fmy-account?wcd"</script>

    Click Deliver exploit to victim. When the victim views the exploit, the response they receive is stored in the cache.

    Go to the URL that you delivered to carlos in your exploit:
    https://YOUR-LAB-ID.web-security-academy.net/resources/..%2fmy-account?wcd

    Notice that the response includes the API key for the user carlos. Copy this.

    Click Submit solution, then submit the API key for carlos to solve the lab.


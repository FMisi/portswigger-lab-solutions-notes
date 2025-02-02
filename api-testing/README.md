Lab: Exploiting an API endpoint using documentation



    In Burp's browser, log in to the application using the credentials wiener:peter and update your email address.

    In Proxy > HTTP history, right-click the PATCH /api/user/wiener request and select Send to Repeater.

    Go to the Repeater tab. Send the PATCH /api/user/wiener request. Notice that this retrieves credentials for the user wiener.

    Remove /wiener from the path of the request, so the endpoint is now /api/user, then send the request. Notice that this returns an error because there is no user identifier.

    Remove /user from the path of the request, so the endpoint is now /api, then send the request. Notice that this retrieves API documentation.

    Right-click the response and select Show response in browser. Copy the URL.

    Paste the URL into Burp's browser to access the documentation. Notice that the documentation is interactive.

    To delete Carlos and solve the lab, click on the DELETE row, enter carlos, then click Send request.

Lab: Exploiting a mass assignment vulnerability

    In Burp's browser, log in to the application using the credentials wiener:peter.

    Click on the Lightweight "l33t" Leather Jacket product and add it to your basket.

    Go to your basket and click Place order. Notice that you don't have enough credit for the purchase.

    In Proxy > HTTP history, notice both the GET and POST API requests for /api/checkout.

    Notice that the response to the GET request contains the same JSON structure as the POST request. Observe that the JSON structure in the GET response includes a chosen_discount parameter, which is not present in the POST request.

    Right-click the POST /api/checkout request and select Send to Repeater.

    In Repeater, add the chosen_discount parameter to the request. The JSON should look like the following:

    {
        "chosen_discount":{
            "percentage":0
        },
        "chosen_products":[
            {
                "product_id":"1",
                "quantity":1
            }
        ]
    }

    Send the request. Notice that adding the chosen_discount parameter doesn't cause an error.

    Change the chosen_discount value to the string "x", then send the request. Observe that this results in an error message as the parameter value isn't a number. This may indicate that the user input is being processed.

    Change the chosen_discount percentage to 100, then send the request to solve the lab.

Lab: Finding and exploiting an unused API endpoint

    In Burp's browser, access the lab and click on a product.

    In Proxy > HTTP history, notice the API request for the product. For example, /api/products/3/price.

    Right-click the API request and select Send to Repeater.

    In the Repeater tab, change the HTTP method for the API request from GET to OPTIONS, then send the request. Notice that the response specifies that the GET and PATCH methods are allowed.

    Change the method for the API request from GET to PATCH, then send the request. Notice that you receive an Unauthorized message. This may indicate that you need to be authenticated to update the order.

    In Burp's browser, log in to the application using the credentials wiener:peter.

    Click on the Lightweight "l33t" Leather Jacket product.

    In Proxy > HTTP history, right-click the API/products/1/price request for the leather jacket and select Send to Repeater.

    In the Repeater tab, change the method for the API request from GET to PATCH, then send the request. Notice that this causes an error due to an incorrect Content-Type. The error message specifies that the Content-Type should be application/json.

    Add a Content-Type header and set the value to application/json.

    Add an empty JSON object {} as the request body, then send the request. Notice that this causes an error due to the request body missing a price parameter.

    Add a price parameter with a value of 0 to the JSON object {"price":0}. Send the request.

    In Burp's browser, reload the leather jacket product page. Notice that the price of the leather jacket is now $0.00.

    Add the leather jacket to your basket.

    Go to your basket and click Place order to solve the lab.

Lab: Exploiting server-side parameter pollution in a query string



    In Burp's browser, trigger a password reset for the administrator user.

    In Proxy > HTTP history, notice the POST /forgot-password request and the related /static/js/forgotPassword.js JavaScript file.

    Right-click the POST /forgot-password request and select Send to Repeater.

    In the Repeater tab, resend the request to confirm that the response is consistent.

    Change the value of the username parameter from administrator to an invalid username, such as administratorx. Send the request. Notice that this results in an Invalid username error message.

    Attempt to add a second parameter-value pair to the server-side request using a URL-encoded & character. For example, add URL-encoded &x=y:

    username=administrator%26x=y

    Send the request. Notice that this returns a Parameter is not supported error message. This suggests that the internal API may have interpreted &x=y as a separate parameter, instead of part of the username.

    Attempt to truncate the server-side query string using a URL-encoded # character:

    username=administrator%23

    Send the request. Notice that this returns a Field not specified error message. This suggests that the server-side query may include an additional parameter called field, which has been removed by the # character.

    Add a field parameter with an invalid value to the request. Truncate the query string after the added parameter-value pair. For example, add URL-encoded &field=x#:

    username=administrator%26field=x%23

    Send the request. Notice that this results in an Invalid field error message. This suggests that the server-side application may recognize the injected field parameter.

    Brute-force the value of the field parameter:
        Right-click the POST /forgot-password request and select Send to Intruder.

        In the Intruder tab, add a payload position to the value of the field parameter as follows:

        username=administrator%26field=§x§%23
        In Intruder > Payloads, click Add from list. Select the built-in Server-side variable names payload list, then start the attack.
        Review the results. Notice that the requests with the username and email payloads both return a 200 response.

    Change the value of the field parameter from x# to email:

    username=administrator%26field=email%23

    Send the request. Notice that this returns the original response. This suggests that email is a valid field type.

    In Proxy > HTTP history, review the /static/js/forgotPassword.js JavaScript file. Notice the password reset endpoint, which refers to the reset_token parameter:

    /forgot-password?reset_token=${resetToken}

    In the Repeater tab, change the value of the field parameter from email to reset_token:

    username=administrator%26field=reset_token%23

    Send the request. Notice that this returns a password reset token. Make a note of this.

    In Burp's browser, enter the password reset endpoint in the address bar. Add your password reset token as the value of the reset_token parameter . For example:

    /forgot-password?reset_token=123456789

    Set a new password.

    Log in as the administrator user using your password.

    Go to the Admin panel and delete carlos to solve the lab.


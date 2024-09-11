Methodology:

//TODO: methodology

change the value of the sub claim from wiener to administrator. Send the request again

change the value of the alg parameter to none. Send the request again

remove the signature from the JWT, but remember to leave the trailing dot after the payload. Send the request again

change back the signature.

Part 1: Copy the JWT and brute-force the secret. You can do this using hashcat as follows:
hashcat -a 0 -m 16500 <YOUR-JWT> /path/to/jwt.secrets.list

Part 2 - Generate a forged signing key

    Using Burp Decoder, Base64 encode the secret that you brute-forced in the previous section.

    In Burp, go to the JWT Editor Keys tab and click New Symmetric Key. In the dialog, click Generate to generate a new key in JWK format. Note that you don't need to select a key size as this will automatically be updated later.

    Replace the generated value for the k property with the Base64-encoded secret.

    Click OK to save the key.

Part 3 - Modify and sign the JWT

    Go back to the GET /admin request in Burp Repeater and switch to the extension-generated JSON Web Token message editor tab.

    In the payload, change the value of the sub claim to administrator

    At the bottom of the tab, click Sign, then select the key that you generated in the previous section.

    Make sure that the Don't modify header option is selected, then click OK. The modified token is now signed with the correct signature.

    Send the request and observe that you have successfully accessed the admin panel.

if not, then:




===========================================================================================================================

Lab: JWT authentication bypass via unverified signature



    In the lab, log in to your own account.

    In Burp, go to the Proxy > HTTP history tab and look at the post-login GET /my-account request. Observe that your session cookie is a JWT.

    Double-click the payload part of the token to view its decoded JSON form in the Inspector panel. Notice that the sub claim contains your username. Send this request to Burp Repeater.

    In Burp Repeater, change the path to /admin and send the request. Observe that the admin panel is only accessible when logged in as the administrator user.

    Select the payload of the JWT again. In the Inspector panel, change the value of the sub claim from wiener to administrator, then click Apply changes.

    Send the request again. Observe that you have successfully accessed the admin panel.

    In the response, find the URL for deleting carlos (/admin/delete?username=carlos). Send the request to this endpoint to solve the lab.

Lab: JWT authentication bypass via flawed signature verification



    In the lab, log in to your own account.

    In Burp, go to the Proxy > HTTP history tab and look at the post-login GET /my-account request. Observe that your session cookie is a JWT.

    Double-click the payload part of the token to view its decoded JSON form in the Inspector panel. Notice that the sub claim contains your username. Send this request to Burp Repeater.

    In Burp Repeater, change the path to /admin and send the request. Observe that the admin panel is only accessible when logged in as the administrator user.

    Select the payload of the JWT again. In the Inspector panel, change the value of the sub claim to administrator, then click Apply changes.

    Select the header of the JWT, then use the Inspector to change the value of the alg parameter to none. Click Apply changes.

    In the message editor, remove the signature from the JWT, but remember to leave the trailing dot after the payload.

    Send the request and observe that you have successfully accessed the admin panel.

    In the response, find the URL for deleting carlos (/admin/delete?username=carlos). Send the request to this endpoint to solve the lab.

Lab: JWT authentication bypass via weak signing key


Part 1 - Brute-force the secret key

    In Burp, load the JWT Editor extension from the BApp store.

    In the lab, log in to your own account and send the post-login GET /my-account request to Burp Repeater.

    In Burp Repeater, change the path to /admin and send the request. Observe that the admin panel is only accessible when logged in as the administrator user.

    Copy the JWT and brute-force the secret. You can do this using hashcat as follows:
    hashcat -a 0 -m 16500 <YOUR-JWT> /path/to/jwt.secrets.list

    If you're using hashcat, this outputs the JWT, followed by the secret. If everything worked correctly, this should reveal that the weak secret is secret1.

Note

Note that if you run the command more than once, you need to include the --show flag to output the results to the console again.
Part 2 - Generate a forged signing key

    Using Burp Decoder, Base64 encode the secret that you brute-forced in the previous section.

    In Burp, go to the JWT Editor Keys tab and click New Symmetric Key. In the dialog, click Generate to generate a new key in JWK format. Note that you don't need to select a key size as this will automatically be updated later.

    Replace the generated value for the k property with the Base64-encoded secret.

    Click OK to save the key.

Part 3 - Modify and sign the JWT

    Go back to the GET /admin request in Burp Repeater and switch to the extension-generated JSON Web Token message editor tab.

    In the payload, change the value of the sub claim to administrator

    At the bottom of the tab, click Sign, then select the key that you generated in the previous section.

    Make sure that the Don't modify header option is selected, then click OK. The modified token is now signed with the correct signature.

    Send the request and observe that you have successfully accessed the admin panel.

    In the response, find the URL for deleting carlos (/admin/delete?username=carlos). Send the request to this endpoint to solve the lab.

Lab: JWT authentication bypass via jwk header injection



    In Burp, load the JWT Editor extension from the BApp store.

    In the lab, log in to your own account and send the post-login GET /my-account request to Burp Repeater.

    In Burp Repeater, change the path to /admin and send the request. Observe that the admin panel is only accessible when logged in as the administrator user.

    Go to the JWT Editor Keys tab in Burp's main tab bar.

    Click New RSA Key.

    In the dialog, click Generate to automatically generate a new key pair, then click OK to save the key. Note that you don't need to select a key size as this will automatically be updated later.

    Go back to the GET /admin request in Burp Repeater and switch to the extension-generated JSON Web Token tab.

    In the payload, change the value of the sub claim to administrator.

    At the bottom of the JSON Web Token tab, click Attack, then select Embedded JWK. When prompted, select your newly generated RSA key and click OK.

    In the header of the JWT, observe that a jwk parameter has been added containing your public key.

    Send the request. Observe that you have successfully accessed the admin panel.

    In the response, find the URL for deleting carlos (/admin/delete?username=carlos). Send the request to this endpoint to solve the lab.

Note

Instead of using the built-in attack in the JWT Editor extension, you can embed a JWK by adding a jwk parameter to the header of the JWT manually. In this case, you need to also update the kid header of the token to match the kid of the embedded key.

Lab: JWT authentication bypass via jku header injection


Part 1 - Upload a malicious JWK Set

    In Burp, load the JWT Editor extension from the BApp store.

    In the lab, log in to your own account and send the post-login GET /my-account request to Burp Repeater.

    In Burp Repeater, change the path to /admin and send the request. Observe that the admin panel is only accessible when logged in as the administrator user.

    Go to the JWT Editor Keys tab in Burp's main tab bar.

    Click New RSA Key.

    In the dialog, click Generate to automatically generate a new key pair, then click OK to save the key. Note that you don't need to select a key size as this will automatically be updated later.

    In the browser, go to the exploit server.

    Replace the contents of the Body section with an empty JWK Set as follows:
    {
        "keys": [

        ]
    }

    Back on the JWT Editor Keys tab, right-click on the entry for the key that you just generated, then select Copy Public Key as JWK.

    Paste the JWK into the keys array on the exploit server, then store the exploit. The result should look something like this:
    {
        "keys": [
            {
                "kty": "RSA",
                "e": "AQAB",
                "kid": "893d8f0b-061f-42c2-a4aa-5056e12b8ae7",
                "n": "yy1wpYmffgXBxhAUJzHHocCuJolwDqql75ZWuCQ_cb33K2vh9mk6GPM9gNN4Y_qTVX67WhsN3JvaFYw"
            }
        ]
    }

Part 2 - Modify and sign the JWT

    Go back to the GET /admin request in Burp Repeater and switch to the extension-generated JSON Web Token message editor tab.

    In the header of the JWT, replace the current value of the kid parameter with the kid of the JWK that you uploaded to the exploit server.

    Add a new jku parameter to the header of the JWT. Set its value to the URL of your JWK Set on the exploit server.

    In the payload, change the value of the sub claim to administrator.

    At the bottom of the tab, click Sign, then select the RSA key that you generated in the previous section.

    Make sure that the Don't modify header option is selected, then click OK. The modified token is now signed with the correct signature.

    Send the request. Observe that you have successfully accessed the admin panel.

    In the response, find the URL for deleting carlos (/admin/delete?username=carlos). Send the request to this endpoint to solve the lab.

Lab: JWT authentication bypass via kid header path traversal


Note

In this solution, we'll point the kid parameter to the standard file /dev/null. In practice, you can point the kid parameter to any file with predictable contents.
Generate a suitable signing key

    In Burp, load the JWT Editor extension from the BApp store.

    In the lab, log in to your own account and send the post-login GET /my-account request to Burp Repeater.

    In Burp Repeater, change the path to /admin and send the request. Observe that the admin panel is only accessible when logged in as the administrator user.

    Go to the JWT Editor Keys tab in Burp's main tab bar.

    Click New Symmetric Key.

    In the dialog, click Generate to generate a new key in JWK format. Note that you don't need to select a key size as this will automatically be updated later.

    Replace the generated value for the k property with a Base64-encoded null byte (AA==). Note that this is just a workaround because the JWT Editor extension won't allow you to sign tokens using an empty string.

    Click OK to save the key.

Modify and sign the JWT

    Go back to the GET /admin request in Burp Repeater and switch to the extension-generated JSON Web Token message editor tab.

    In the header of the JWT, change the value of the kid parameter to a path traversal sequence pointing to the /dev/null file:
    ../../../../../../../dev/null

    In the JWT payload, change the value of the sub claim to administrator.

    At the bottom of the tab, click Sign, then select the symmetric key that you generated in the previous section.

    Make sure that the Don't modify header option is selected, then click OK. The modified token is now signed using a null byte as the secret key.

    Send the request and observe that you have successfully accessed the admin panel.

    In the response, find the URL for deleting carlos (/admin/delete?username=carlos). Send the request to this endpoint to solve the lab.


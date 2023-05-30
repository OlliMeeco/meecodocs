# H1 SVX API Authentication (Sandbox)

The Sandbox environment allows you to experiment freely in an environment that is always updated to include the latest and the greatest functionalities.
To connect to the Sandbox API of SVX, follow these steps:

1. You need access to the SVX Portal. If you don’t have an account yet, you can register one here: [Signup page](https://www.meeco.me/signup). You will be onboarded by using an email flow.
2. Login to your account of the Portal via this link: [Portal](https://portal-sandbox.securevalueexchange.com/)
3. To access the API’s your account needs to be assigned to an organisation. 
4. Navigate to the ‘Applications’ section under ‘Devtools’
5. Click on “Create application” to configure a new connection: Complete the form by giving this connection a specific name 
6. Now your application is available . Click on the three dots “…” and select “view” to look at the connection specifications.
7. Use the Domain, Client ID and Client Secret to configure your connection.
8. For Postman, use this configuration:
>Grant Type: Client Credentials
>
>Access Token URL: https://login-sandbox.securevalueexchange.com/oauth2/token
>
>Client ID: Get from portal
>
>Client Secret: Get from portal
>
>Scope: openid
>
>Client Authentication: Send client credentials in body

9. This configuration will allow you to retrieve an access token that should be used to call the API’s listed [here](https://api-reference-dev.svx.exchange/)

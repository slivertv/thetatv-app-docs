# slivertv-api-docs
Public APIs for the SLIVER.tv App


API Reference:
https://documenter.getpostman.com/view/945111/SW7UapnJ


# Introduction
Creating applications that integrate with SLIVER.tv involves a few steps:
1. Creating a user on SLIVER.tv to be your developer account
2. Registering your application with a SLIVER.tv staff member
3. Authenticating your users to make API calls on their behalf


# Authentication
SLIVER.tv uses OAuth 2.0 to authenticate users with your application. After registering your application with SLIVER.tv, you'll receive a _Client ID_, and a _Client Secret_.

* Client IDs are public, and used by your application to identify itself. 
* Client Secrets are private, and should be treated like a password. Do not expose this to end users.

> NOTE: Client IDs are **required for all API calls** made by an application - regardless if the API call requires authenticated or not. Any application not including a Client ID may have privaleges revoked.

Authenticating SLIVER.tv users with your apps has the following flow:
1. Have your user login and grant permissions to your app on SLIVER.tv.
2. This will provide you with a one-time use code.
3. Make a server-to-server call with the code to retrieve the users access token and refresh token.
4. Make calls on the users behalf, using the access token.
5. If the access token expires, use the refresh token to obtain a new access token.


### User Authentication
To begin the process of authenticating the users, open a window for them to login and grant your application permissions:

> https://www.sliver.tv/account/grant-app?client_id={YOUR_CLIENT_ID}&redirect_uri={YOUR_REDIRECT_URI}

Note: The redirect_uri must match the URIs you provide when you setup your application.

**Permission is Granted:**
If the user accepts the permissions, the user will be redirected to your specified URI, with the query parameter `code`.

**Permission is NOT Granted:**
The user will be redirected to your specified URI without the `code` query parameter.


### Retrieving an Access and Refresh Token
After you've received your one-time-use code, make a server-to-server call to:

> https://api.sliver.tv/v1/oauth/token?client_id={CLIENT_ID}&client_secret={CLIENT_ID}&grant_type=authorization_code&code={CODE_GIVEN}


# Making API Calls
Every API call your makes, you must include the Client ID in the header:

> Client-ID: {CLIENT_ID}

If you are making an authorized call on the users behalf, include the users access token in the header:

> Authorization: Bearer {ACCESS_TOKEN}



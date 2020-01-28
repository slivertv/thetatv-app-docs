# THETA.tv API Docs
Public APIs for THETA.tv Applications

API Reference:
https://documenter.getpostman.com/view/945111/SW7UapnJ

# Introduction
There are two types of applications: internal and external applications. Internal applications are applications that streamers can discover and install in their channel (e.g. chat bot). External applications are applications that run outside of THETA.tv and just use THETA.tv OAuth 2.0 to authenticate users with the external application.

Creating internal applications involves the following steps:
1. Create a user on THETA.tv to be your developer account (you can also use your normal user account)
2. Ask a THETA.tv staff member to register your application
3. Develop your application
4. (Optional) Provide functionality to authenticate users to make API calls on their behalf 
5. Ask a THETA.tv staff member to list your application
6. Wait for streamers to install your application

Creating external applications involved the following steps:
1. Create a user on THETA.tv to be your developer account (you can also use your normal user account)
2. Ask a THETA.tv staff member to register your application
3. Develop your application
4. Provide functionality to authenticate users to make API calls on their behalf 

## Application Registration
An application will have the following details. You must have these details ready when asking a THETA.tv staff member to register your application
- name: the name of your application. This name will be shown in various places to the streamer, users, and the streamer's channel. Example: Potato Bot
- description: a thorough description of what your application does. This will be shown to streamers in the list of applications they can install. 
- avatar: an image that will be shown in chat whenever your application sends a message
- category: we will establish the category of your application based on the description you provide
- redirect urls: a list of urls user grants will redirect to

> NOTE: for internal applications, **YOU MUST DISCLOSE ALL FUNCTIONALITY OF YOUR APPLICATION IN THE DESCRIPTION**. If your application is found to perform additional functionality not mentioned in the description, or if your application doesn't perform all functionality mentioned in the description, it can/will have privileges revoked. 

## Channel Installation (internal applications only)
By default, your application is not discoverable by streamers until you ask a THETA.tv staff member to list your application. Once installed in a channel, permission is automatically granted to your application to make API calls on behalf of the streamer/channel. 
Channel authentication is required when your application is making API calls on behalf of the channel/streamer. To get the list of streamers that have installed your application, and get the access tokens, you can make the API call to list all streamers/channels that have installed your application.

> NOTE: Your application cannot restrict access or limit functionality to any specific channel/streamer. If your application is found to restrict access or limit functionality to a channel/streamer, it will be removed.

## User Authentication (optional for internal applications)
User authentication is only required if your application is making API calls on behalf of users. THETA.tv uses OAuth 2.0 to authenticate users with your application. After registering your application with THETA.tv, you'll receive a _Client ID_, and a _Client Secret_.

* Client IDs are public, and used by your application to identify itself. 
* Client Secrets are private, and should be treated like a password. Do not expose this to end users.

> NOTE: Client IDs are **required for all API calls** made by an application - regardless if the API call requires authenticated or not. Any application not including a Client ID may have privileges revoked.

Authenticating THETA.tv users with your application has the following flow:
1. Have the user login and grant permission to your application on THETA.tv.
2. This will provide you with a one-time use code.
3. Make a server-to-server call with the code to retrieve the user's access token and refresh token.
4. Make calls on the user's behalf, using the access token.
5. If the access token expires, use the refresh token to obtain a new access token.

To begin the process of authenticating users, open a window for them to login to THETA.tv and grant your application permissions:

    https://www.theta.tv/account/grant-app?client_id={YOUR_CLIENT_ID}&redirect_uri={YOUR_REDIRECT_URI}

> Note: The redirect_uri must match the URIs you provide when you register your application.

**Permission is Granted:**
If the user accepts the permissions, the user will be redirected to your specified URI, with the query parameter `code` appended.

    https://www.yoursite.com/oauth/grant?code={THETA_CODE}

**Permission is NOT Granted:**
The user will be redirected to your specified URI **without** the `code` query parameter.

    https://www.yoursite.com/oauth/grant

### Retrieving an Access and Refresh Token
After you've received your one-time-use code, make a server-to-server call to:

    https://api.theta.tv/v1/oauth/token?client_id={CLIENT_ID}&client_secret={CLIENT_ID}&grant_type=authorization_code&code={CODE_GIVEN}


## Making API Calls
Every API call your application makes, you must include the Client ID in a query parameter:

    client_id: {CLIENT_ID}

Some API calls further require application authentication. These API calls should be done server-to-server with the application's secret in a query paramter:

    client_secret: {CLIENT_SECRET}

If you are making an authorized call on the streamer/channel or user's behalf, include the user's access token in the header:

    Authorization: Bearer {ACCESS_TOKEN}

## Application Functionality
Application functionality is restricted to the APIs available in the API reference. If you need additional APIs for your application, ask a THETA.tv staff member.

> NOTE: application functionality cannot distract or take away users from the streamer's attention. If your application is found to perform functionality that the streamer did not initiate, it can have privileges revoked.

## Chat
Your application can make use of the chat messages. THETA.tv currently uses the PubNub service to drive chat. The steps to listening to chat messages are:

1. Get your favorite flavor of PubNub SDK
2. Get a PubNub client id from THETA.tv staff
3. Subscribe to PubNub channels to receive read-only messages

PubNub SDKs: https://www.pubnub.com/docs

Please follow PubNub documentation for subscribing and listening to channels.

### Subscribing to THETA.tv Chat

 When subscribing to channels through the PubNub SDK, you'll need to provide a channel name. These channel names are named using the following convention:
    "chat.{CHANNEL_ID}"

Where `{CHANNEL_ID}` is the user ID of the channel's owner.
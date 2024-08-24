# App Connection - Overview

### What is an App Connection?

An App Connection is a 3rd party app integration with OctoEverywhere that allows any OctoPrint app to leverage OctoEverywhere for remote access to user’s printers. App Connections enable a great partnership between 3rd party apps and OctoEverywhere for our users, allowing users to use the best OctoPrints apps for remote monitoring and control with a super easy and seamless setup.

#### Benefits to Your App:

- Apps that integrated App Connections will be advertised on the OctoEverywhere website as being integrated with OctoEverywhere.
- Many OctoEverywhere users now have the ability to monitor on-the-go where they hadn’t before and are looking for the best OctoPrint apps on all platforms to make that experience better.
- Your existing users will have a safe and secure remote access option that full works with your app.
- App Connections return information about the OctoPrint instnace like the local network IP and can even **setup an OctoPrint API key for you**. This means when a user sets up a printer with an App Connection, your app has everything it needs to get going!

#### OctoEverywhere Offers Your App:

- Very easy and straight forward developer integration.
  - OctoEverywhere offers a fully ready to use portal, which handles user login, user account setup, user printer setup, and more.
  - Once setup, you can use all of your existing OctoPrint connection logic. OctoEverywhere simply gives you a unique hostname you can use to access the users' OctoPrint instance.
- Full access to the OctoPrint instance, the exact same raw access you would have to all the APIs, web sockets, and webcam streams as you would have on a local connection.
- A full secure managed connection to OctoPrint. You can choose domain name security, basic http auth, or Bearer tokens for added security.
- OctoEverywhere app APIs that allow you to query information about the printer from OctoEverywhere, including it’s current local IP, it’s connection state to OctoEverywhere, and the user’s current limits.
- Direct contact with our development team. If you have any questions or features requests, [please reach out via support system](https://octoeverywhere.com/support).

### What Integration Options Do I Have?

App Connections currently offer four easy integration points:

1. [App Connection](App-Connection-Usage.md)
   1. Gives you a secure and unique hostname to connect to your user's printer from anywhere. The App Connection is secured with unique credentails that are only transmitted over the secure SSL channels.
2. [App Connection Portal](App-Portal-Integration.md)
   1. Allows app developers a easy to integrate "OAuth style" App Connection creation process.
   2. Full hosted by OctoEveywhere providing a simple and easy user setup flow.
3. [Gadget AI Print Failure Detection Status In Your App](https://octoeverywhere.stoplight.io/docs/octoeverywhere-api-docs/b538c771f5cef-get-gadget-s-status-for-app-connections)
   1. AI failure detection in your app! Using the Gadget Status API for App Connections, you can poll print's current Gadget AI Failure Detection status and display it in your app.
3. [App Connection OctoEverywhere APIs](App-OctoEverywhere-API.md)
   1. A set of APIs from OctoEverywhere's service that return real-time information about the connected printer.
   2. Provides the connection status of the printer to OctoEverywhere as well as the state of the App Connection.
   3. Provides the local IP of the printer for easy local discovery.
   4. Provides OctoEveryhwere limits for the printer.
4. [OctoEverywhere OctoPrint Plugin Local API](App-Local-Plugin-API.md)
   1. Provides detection of if the OctoEverywhere plugin is installed in OctoPrint.
   2. Provides access to the OctoEverywhere Printer ID, which allows your app to identify the printer specifically to the OctoEverywhere service.

### App Connection Life Cycle

An App Connection is created when a portal flow is successful. The credentials and context of an App Connections gives your app access to only a single printer for a given user. If a user want's set up multiple printers, they will all be unique sets of App Connection contexts.

Once created, an App Connection context and credentials remain valid until:

1. The user deletes their OctoEverywhere account.
2. The user removes the printer from their account.
3. The user revokes the App Connection from the OctoEverywhere website.

You can use the OctoEverywhere APIs described below to query OctoEverywhere for the state an App Connection at anytime.

### OctoEverywhere Supporter Status

App Connections require more load on the OctoEverywhere service than standard remote access usage. Since OctoEverywhere is a community-funded project, we have to balance the community funding with the cost of running the service. Thus, we require users to have Supporter Perks to use App Connections. 

If a user's account loses Supporter Perks, they will not have access to any App Connections they have set up. **However, if the user becomes an OctoEverywhere supporter again, all of the existing App Connections will become valid once again, so no re-setup is required!**

When a user loses Supporter Perks, all App Connection API calls will return a [`605` HTTP status code, as denoted on the App Connections Usage page.](App-Connection-Usage.md)

**If a `605` HTTP status code is returned, the app should link the user to the following URL, giving the user details on what's wrong and how to fix it.** Using this common page ensures if any of the App Connection supporter details change in the future, they will be consistent. Ideally, OctoEverywhere would like to include App Connections as a free feature for all users in the future.

https://octoeverywhere.com/appportal/v1/nosupporterperks?appid=<your_app_id>


### Authentication

Every app connection starts with a unique and high-entropy subdomain being generated. This keeps the connection unique, however the DNS name is transmitted in the clear for DNS resolution, so it can be sniffed during a man in the middle attack. For that reason, we also require a header based authencation system, which are trasnsmitted via the secure SSL tunner and thus are 100% secure.

The supported header based authencation systems are basic http auth or bearer auth tokens. Apps can chose which ever to use based on which is easier to implment, they are both equally secure.

The authentication type is specified at the start of an App Connection Portal flow. For details see the [App Portal Integration](App-Portal-Integration.md) page.

### Optional - OctoPrint Auto API Key Generation

This amazing feature allows the App Connection Portal to be a true one stop shop for setting up an OctoPrint printer in your app. Since the App Portal already returns the credentails for the OctoEverywhere based connection, the OctoPrint's device local IP, and now the OctoPrint API key for your app, when the App Portal is complete your app has everything it needs to finish the setup!

For more details, [checkout this doc about the feature](App-Connection-zOctoPrint-Auto-API-Key.md).

## Sounds Great! Let's Integrate!

#### Step 1 - Integrate the App Connection Portal

**Integration Time:** \~1 Hour

To create a new App Connection, you must use the App Connection Portal. This easy to integrate portal provides a great user flow and easily allows users connect printers to your app!

[Get started with the App Connection Portal.](App-Portal-Integration.md)

#### Step 2 - Use The App Connection To Talk To OctoPrint, Moonraker, or Bambu Lab

**Integration Time:** \~1 Hour

Once you have the App Connection created, use it to communicate with OctoPrint, Moonraker, or Bambu Lab.

[Get started with usage of the App Connection](App-Connection-Usage.md).

#### Step 3 - (optional) - Integrate with OctoEverywhere APIs

**Integration Time:** \~20 Minutes

The OctoEverywhere App APIs give you access to real-time details for a printer once it has been setup via the portal. This information can be used to make sure the App Connection is still valid, check if the printer is connected to OctoEverywhere, get the printer's local IP address, and get user usage limits.

[Get started with the OctoEverywhere App Connection APIs](App-OctoEverywhere-API.md).

#### Step 4 - (optional) - Integrate with Gadget AI Failure Detection

**Integration Time:** \~1 Hour

The OctoEverywhere App APIs give you access to real-time details for a printer once it has been setup via the portal. This information can be used to make sure the App Connection is still valid, check if the printer is connected to OctoEverywhere, get the printer's local IP address, and get user usage limits.

[Get started with the OctoEverywhere App Connection APIs](https://octoeverywhere.stoplight.io/docs/octoeverywhere-api-docs/b538c771f5cef-get-gadget-s-status-for-app-connections).

#### Step 4 - (optional) - Integrate with the local plugin APIs

**Integration Time:** \~1 Hour

The OctoEverywhere plugin installed in OctoPrint has a simple API that allows detection of it the plugin is installed and basic information. Gathering this information and sending it to the service when using the App Connection Portal can make an even better user experience.

[Get started with the OctoEverywhere plugin local APIs](App-Local-Plugin-API.md).

### Give Me Feedback!

This entire thing was created for you, the developer, to make your life easier. If there are any changes or tweaks I can make to anything in this process, please let me know.

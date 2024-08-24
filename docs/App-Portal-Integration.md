# App Connection - Portal 📱

Check out the [App Connection Overview](App-Connection-Overview.md) page for an overview of the App Connection entire system. 

The App Connection Portal has the following goals:

1)	Be a great and easy experience for users.
2)	Be an easy and complete app developer integration tool.
3)	Be robust and complete - able to handle all types user scenarios.

### Cool! What does it look like?

[Give it a try!](https://octoeverywhere.com/appportal/v1/?appid=devtest) 

**Note:** The experience is optimized for mobile / tablet layouts.

#### A High Level User Experience
1) A user of your app want's to setup a remote connection to their printer with OctoEverywhere.
2) The user sees an in-app web view of the OctoEverywhere App Connection Portal
    1) The user sees a welcome message
    2) Optional flows depending on the user's state:
          1) If the user doesn't have an OctoEveywhere account... the portal will help them create one.
          2) If the user doesn't have a printer connected to OctoEverywhere... the portal will walk them through the 2 minute setup.
          3) If the user isn't an OctoEverywhere supporter... the portal will describe why a supporter role is required (for now) and will help them upgrade if they desire.
    5) The user confirms the printer they want to connect to and authorizes the app to connect.
6) The web portal closes and the user can now use the full app from anywhere!

#### A High Level Example App Integration
1) A user of your app want's to setup a remote connection to their printer with OctoEverywhere.
2) Your app opens a in-app web view and navigates to the OctoEverywhere App portal starting URL.
    1) Your app passes a few GET parameters to setup the App Connection portal
3) Your app watches the navigations of the web view until the portal navigates to the completion URL.
4) Your app reads the returned GET parameters from the completion URL
5) Your app uses the returned base URL to connect to the printer from anywhere!



### A Few Important Notes

- We would love your feedback!
  - Is there something broken? Or some feature we could add to make your app more powerful? [Reach out to our dev team directly and let us konw!](https://octoeverywhere.com/support)
- The `appApiToken` and `auth*` parameters returned on the completion URL will only be given to you app once, and can not be queried from OctoEverywhere again. If they are lost, a new App Conneciton must be made.

### Starting An App Connection Portal

To start a portal, you should open an in app web view and navigate to the following URL with the required and optiaonl GET parameters:
https://octoeverywhere.com/appportal/v1/

All of the required and optional GET parameters can be found in the [API doc for Starting An App Portal.](../reference/App-Connection.v1.yaml/paths/~1appportal~1v1/get)

Example: https://octoeverywhere.com/appportal/v1/?appid=devtest

### Handling Portal Completion

On successful or failure completion, the portal will navigate to `appportal/v1/complete` or the specified `returnUrl` with the resulting GET parameters. Your app should monitor the web view's navigation and when it sees the completion URL handle the portal result.

The returned GET parameters can be parsed by your app and stored to use the app connection.

Remember that the `appApiToken` and `auth*` parameters will only be returned this one time and cannot be retrieved again. If you lose them, you must make a new App Connection.\

#### Completion GET Params

- `success` (boolean) - Indicates if the App Connection Portal was successful or not.
- `id` (url endoded string) - The newly created App Connection Id. This is the unique id for the App Connection made from this portal process. It should be stored to use to access the printer and the OctoEverywhere APIs.
- `url` (url encoded string) - This is the substute URL your app can use to access OctoPrint APIs. Remember that the whichever auth header you use must be set on all requests for access.
- `appApiToken` (url encoded string) - This is the OctoEverywhere API access token for this app connection. The OctoEverywhere API allows you to query information about the App Connection status and the printer's connection status.
- `printername` (url encoded string) - This is the user assigned name of this printer.
- `userPrinterAccessUrl` - (url encoded string) - This is a user firend URL the user can use to access their printer. This is returned so it can be shown in app UI for the user's reference, but this URL will not work for the app at all since it doesn't accept the app's auth.
- `printerLastKnownLocalIp` (optional - string) - If known, this is the last local IP of the printer. This can be used for local printer discovery.
- `authbasichttpuser` (url encoded string) - The basic http user name for the App Connection.
- `authbasichttppassword` (url encoded string) - The basic http password for the App Connection.
- `authBearerToken` (url encoded string) - The bearer token for the App Connection.
- `octoPrintApiKey` (url encoded string - optional) - The generated OctoPrint API token if it could be created. [See this guide for details.](App-Connection-zOctoPrint-Auto-API-Key.md)
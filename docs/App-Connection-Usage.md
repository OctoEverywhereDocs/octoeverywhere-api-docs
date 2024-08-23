# App Connection - Usage

### Using an App Connection

Once you have an App Connection setup, you want to use it to talk to OctoPrint! It's super easy to do. 

The App Connection acts as a stand in for any request you would make to OctoPrint locally. For example, if you wanted to call the login api locally, it might look something like `http://192.168.1.5/api/login`. If you wanted to call the login api for the App Connection, it would look like `https://app-theverylongappconnectionid.octoeverywhere.com/api/login`. 

**Note:** You should always use `https` when making requests to OctoEverywhere to keep the user's data secure.

Every part of the HTTP request will be relayed to OctoPrint, including the headers, the body, and the HTTP method. All parts of the response from OctoPrint will be returned, including the headers, the body, and the HTTP status code. The OctoEverywhere relay system supports basic HTTP calls, file download / upload, webcam streaming, and websocket connections.

For the most part, all your app needs to do is replace the local IP address with the App Connection hostname for any OctoPrint communication, and it will just work 99% of the time.

#### OctoEverywhere Special Considerations

There are only a few special things you must consider when making calls using the OctoEverywhere App Connection subdomains.

1) If your app connection is setup to use authentication (which is recommended) you must include *either* the basic HTTP auth header or the Bearer auth header *for all requests*. This includes websocket requests and webcam streaming requests.
2) There's a special subset of error codes that OctoEverywhere will return if there are issues with the App Connection or issues with communication to the OctoPrint instance. These error codes are described below.
3) Handle the special  error codes listed below with UI to the user, things like if the printer is offline, if the App Connection is revoked, or if the user's account is no longer a OctoEverywhere supporter.
4) Handle switching between locally connecting to OctoPrint when it's available and using the App Connection when it's not.
5) Handle some of the special limits OctoEverywhere has, like webcam streaming time limits, webcam back to back streaming limits, and file upload/download size limits. [These limits can be queried using the OctoEverywhere App Connection API.](App-OctoEverywhere-API.md)

#### OctoEverywhere Custom Error Codes

OctoEverywhere needs to express connection errors, but it can't use HTTP status codes that might overlap what OctoPrint is trying to use. For that reason, we define custom error codes that your app can use to identify errors. If any of the following error codes are returned when your app is making a query to OctoPrint through OctoEverywhere, your app can know there was a problem in OctoEverywhere's connection to the printer.

**Note:** For consientcy, the same error codes are for the [OctoEverywhere App Connection API.](App-OctoEverywhere-API.md)

- **600** - **Server Error / Plugin Error / Unknown Error**
  - *Temporary* - Something went wrong, try again later.
- **601** - **Printer is Not Connected To OctoEverywhere**
  - *Temporary* - Indicates the OctoPrint instance isn't currently connected to OctoEverywhere.
    - There are many reasons for this, including the printer is off or doesn't have a network connection.
- **602** - **OctoEverywhere's Connection to OctoPrint Timed Out.**
  - *Temporary* - The OctoPrint instance is connected to OctoEveywhere, but it took too long to respond to this request.
- **603** - **App Connection Not Found**
  - *Permanent* - The App Connection subdomain used didn't map to a valid App Connection.
- **604** - **App Connection Revoked/Expired**
  - *Permanent* - The App Connection can no longer be used.
    - The App Connection was revoked by the user.
    - The App Connection has expired.
    - The owner's removed the printer from their account.
    - The owner's account was deleted.
- **605** - **App Connection Owner's Account Is No Longer A Supporter.**
  - *Temporary* - The owner account is no longer an OctoEverywhere supporter. If the account is upgraded to supporter status again, this App Connection will continue working as-is.
- **606** - **Invalid App Connection Credentials**
  - *Permanent* - The App Connection requires basic http auth or a Bearer token. The credentials were either missing or incorrect.
- **607** - **File Download Limit Exceeded**
  - *Permanent* - The HTTP **response** body was exceeded the max size for this user's account. The per file limit for this user can be found using the [App Connection Info Api](App-OctoEverywhere-API.md)
- **608** - **File Upload Limit Exceeded**
  - *Permanent* - The HTTP **request** body was exceeded the max size for this user's account. The per file limit for this user can be found using the [App Connection Info Api](App-OctoEverywhere-API.md)
- **609** - **Webcam Back to Back Limit Exceeded**
  - *Temporary* - The user has viewed the webcam too many times per a time window. This time limits can be found in the App OctoEverywhere API.
- **610** - **Plugin Update Required**
  - *(uncommon) Temporary* - The user must update their plugin for this API to work. 
- **611** - **No Beta Access**
  - *(uncommon) Temporary* - This API requires beta access that the user doesn't have.
- **612**
  - *(uncommon) Temporary* - There was an error in the plugin that prevented this service call from being successful. Usually caused the a plugin bug, this isn't expected to happen often. If a user hits this constantly, they should contact OctoEverywhere support.
- **613**
  - *(uncommon) Temporary* - Currently only used by the [Live Links creation API](https://octoeverywhere.stoplight.io/docs/octoeverywhere-api-docs/632a7f5513f9c-create-live-link-for-app-connections) when the `EndWhenCurrentPrintIsComplete` flag is passed. The `EndWhenCurrentPrintIsComplete` requires that there be a print running on the printer, and thus this error code is returned if there is no print running. In the future, this error code could be used by other APIs, but the API doc for each API would show it.
# App Connection - OctoPrint API Key Generation 📱

As of January of 2022, the OctoEverywhere App Portal can now generate a OctoPrint API key on behalf of your app to make the user setup process even easier!

At OctoEverywhere, we get 2-3 support tickets a week asking how to setup or find the OctoPrint API key as part of an app setup. The OctoPrint API key is necessary to keep things secure, but it's a confusing and manual process for users to figure out. This magical process fixes that with a super easy user flow.

### Great! How do I enable this?
It’s easy! All you need to do is add a new flag to your App Portal request. The new flag is:

`OctoPrintApiKeyAppName` - A **url encoded** string that represents the app name you want to show up in the OctoPrint API key settings page. The string must be between 2 and 60 characters long.

When the flag is present at the start of the App Portal session, the App Portal will attempt to generate a key and return it via the following “finish page” GET request. 

`octoPrintApiKey` - A **url encoded** string that’s the OctoPrint granted API key!

### What to Expect As A Response

If the key `octoPrintApiKey` exists, the API key was generated successfully and will be ready to use. If the key `octoPrintApiKey` doesn’t exist, the API key could not be generated. There are many reasons why:
-	The OctoPrint server was offline and not connected to OctoEverywhere.
-	The plugin version was too old and doesn’t support this.
-	The plugin doesn’t have auth.
-	The user failed the email-based access challenge.
-	Other reasons.
From our telemetry, it looks like about 95% of the time the API key generation will be successful. But your app will need to handle the case where it’s not and prompt the user to get the API Access Key or old Authentication Token manually.

### A Quick Overview of How It’s Done Securely
For those developers who are interested in the details, here’s a quick in-depth explanation. **This content isn’t necessary to understand to use the feature, it's just here if you're interested.** 😀

It all starts with the plugin doing an RSA challenge to the server to ensure the server is an authentic OctoEverywhere sever. (The plugin is already connected over a secure WebSocket using a authentic SSL cert, but this adds another layer of security.)

The plugin is also able to generate a API key for itself once installed, which gives it powers to make OctoPrint authencated calls. This API key is not transmitted to the OctoEverywhere services, it’s stored locally on the OctoPrint device. This way the service doesn’t store any OctoPrint auth information for the user.

Once the plugin establishes trust with the service, the service can make a special call which will prompt the plugin to add it’s API access key to the call, thus giving it permission. The special call can only be made by the OctoEverywhere service and can’t be directly invoked with any public exposed APIs from OctoEverywhere.

When your App requests a generated key as part of the App Portal setup the service uses advance security logic to determine if we need to challenge the user to ensure they are authentic. To access the app portal the user must login to OctoEverywhere, using the 2FA if setup, but since we are going to issue a OctoPrint API key we need to be more secure. If required, the App Portal will do a quick user flow where the user must allow access to grant the OctoPrint API key using a second factor auth via unique email sent to them. Only after the user proves they have access to the email account, and thus can approve the access, does the service generate the OctoPrint API key. If the user can’t access their email, they are allowed to finish the App Portal setup as normal, but no OctoPrint API key will be returned.

That's it! Magic 🪄
 

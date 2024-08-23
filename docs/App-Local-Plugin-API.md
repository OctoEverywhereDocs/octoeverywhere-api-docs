# Local Plugin API

### TODO

I didn't have time to fill this out as much as I would like, but basically...

#### To Detect If The OctoEverywhere Plugin Is Installed

Your app can detect if the OctoEverywhere plugin in installed on any local instance of OctoPrint you have permission to by querying the following URL:
`http://<local OctoPrint IP>/api/plugin/octoeverywhere`

If it **does not** return 404, the plugin is installed.

#### Getting the OctoEverywhere Printer Id 

Your app can provide the Printer ID to the App Connection Portal for a better integration. This will make the portal aware of exactly what printer the user intends to connect to.

If your app has local access to the OctoPrint instance, you can query the OctoEverywhere plugin using the following URL and parsing the resulting JSON.
`http://<local OctoPrint IP>/api/plugin/octoeverywhere`
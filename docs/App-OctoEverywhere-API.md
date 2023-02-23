# App Connection API

The OctoEverywhere App API allows apps to get information from the OctoEverywhere service relating to an existing App Connection they have established.

Right now the information that can be queried is limited, but it can be expanded in the future. If there's any functionality of information you would like to see added to better your app, please reach out and we can discuss it. 

### The App Connection Info API

[Look here to find details on how to call the API and what it returns.](../reference/App-Connection-API.v1.yaml/paths/~1api~1appconnection~1info/get)

The `info` API returns you all kinds of good stuff about the current state of the printer. There are three main uses:

1) Determine the state of an App Connection.
2) Get real-time info about the printer.
3) Get information about the user's current limits in terms of max file transfer size, webcam streaming, etc.

#### App Connection State

If your app can't connect to the printer from the App Connection URL, it's helpful to know why. Using the `info` API you can determine the state of the App Connection, which can be one of three states:

1) The App Connection is **valid** and the printer **is** connected OctoEverywhere.
2) The App Connection is **valid** and the printer **is not** connected to OctoEverywhere.
3) The App Connection is **no longer valid**.

You can determine the state based on the HTTP stauts code and the returned JSON body. See the API defintion for details.

#### Real-Time Printer

Real-time information includes the printer's connection status to OctoEverywhere, the printer's name, and it's local IP address (if known).


#### User Limit Information

It can be helpful for your app to know the limitations of the user's account to handle errors when limits are hit. Examples of user limitations are:

1) Max size of an uploaded or downloaded file
2) Max webcam stream length
3) Limit of back to back webcam streams

You can find these limits and future limits with the `info` API.


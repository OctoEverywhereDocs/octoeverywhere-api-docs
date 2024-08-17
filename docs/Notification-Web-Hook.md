# OctoEverywhere Notification Webhook

[OctoEverywhere's realtime notification engine](https://octoeverywhere.com/notifications?source=devdocs_webhook&handler=webhook) can be extended with custom webhooks! Webhooks allow OctoEverywhere to send instant notifications to any HTTP endpoint you specify. 

### What Can You Do With WebHooks?

With little effort and technical knowledge, your imagination is the limit! You could...

- Play the Mario Kart "race start" sound anytime you begin a print.
- Ring a siren and flash lights when [Gadget](https://octoeverywhere.com/gadget) detects a print failure.
- Make your [Emo](https://living.ai/emo/) dance when a print completes successfully.
- Tweet realtime print progress and full-resolution snapshots as you're printing.

What can you come up with? We would love to see your creations, [share them on our Discord!](https://discord.gg/octoeverywhere-797933815812980797)

### Wow! How do I set this up?

There are two ways you can receive the  HTTP POST notifications from OctoEverywhere:

- LAN Webhook Requests
- Public Internet Webhook Requests

#### LAN Webhook Requests

In LAN mode, OctoEverywhere will send webhook http requests from the OctoEverywhere plugin on your local network. This allows you to run a service on your local network that's **not exposed to the public internet.** This can make setting up an HTTP endpoint much easier.

Then URL for LAN HTTP requests can be any valid LAN address. Such as IP addresses `192.168.1.10`, local LAN domains `octopi.local`, Docker network bridges `notificationHandler`, etc.

#### Public Internet Webhook Requests

The webhook HTTP requests will come from the OctoEverywhere service in this mode. The HTTP request can be sent to any HTTP endpoint publicly accessible on the public internet.

If you're using internet-based webhooks, **it's recommended that you use https for your webhook URL** to keep the connection secure.

### Secret Key - Optional

When you set up your webhook in OctoEverywhere, you can optionally specify any string less than or equal to 128 chars that will be sent in every webhook request.

This allows your HTTP server to verify that the request came from OctoEverywhere since only OctoEverywhere and you know the secret key.


### What is sent in the REST endpoint?

The request is an **HTTP POST** request with a json body payload. Bookmark this page, because over time we will add new notification types and JSON properites. The HTTP POST will have a JSON body defined as:

- **PrinterId**
  - (string) This is a unique ID that defines your printer. This ID will not change as long as your printer is attached to your account.
- **SecretKey**
  - (string) If you set a secret key, it will be included here.
- **PrintId**
  - (string) A string that's unique for each print. This string is created when a print starts and remains until the print is complete or stopped. This is useful for tracking which notifications are associated with which print jobs.
- **EventType**
	- (int, enum) This enum maps to the notification type. The event types are defined below.
- **PrinterName**
  - (string) The name you assigned your printer. This name will change if the printer is renamed on OctoEverywhere.
- **SnapshotUrl**
	- (string, optional) If a snapshot can be taken, this will be a URL where the image can be viewed or downloaded. Note this image URL will only remain valid for about 7 days.
- **QuickViewUrl**
  - (string) A url to OctoEverywhere's Quick View, which provides a secure internet based way to quickly view the full printer state, pause, and cancel prints.
- **FileName**
	- (string, optional) the file name in OctoPrint for the current file being printed.
- **DurationSec**
	- (int, optional) the duration of the print, since the start, in seconds.
- **Progress**
	- (int, optional) the current print progress as a percentage. Where 0 is 0% and 100 is 100%.
- **TimeRemainingSec**
	- (int, optional) the amount of time estimated to be remaining by OctoPrint, in seconds.
- **ZOffsetMM**
	- (int, optional) the current z-axis offset in millimeters.
- **Error**
	- (string, optonal) for EventTypes that indicate an error, this string might contain some kind of error message from OctoPrint describing the issue.

### Event Types

The following defines the EventType enum. **Remember that over time, more event types will be added to this list.**

1. **Started**
2. **Complete**
3. **Failed**
4. **Paused**
5. **Resumed**
6. **Print Progress**
    - Fired due to a % progress milestone or a timer event.
7. **Gadget Possible Failure Warning**
    - Gadget thinks the print might have a failure.
8. **Gadget Paused Print Due To Failure**
    - Gadget paused the print because it detected a failure.
    - Only fires when Gadget Smart Pause is enabled.
9. **Error**
    - Fired due to any error from the printer.
10. **First Layer Complete**
    - Fired when the first layer of the print is complete.
11. **Filament Change Required**
    - Fired when the printer reports a filament change is required. This can be due to a color swap or because the filament ran out.
12. **User Interaction Required**
    - Fires when the printer requests user interaction.
13. **Non-Supporter Notification Limit**
    - Fired when the user's account hits the daily notification limit.
    - Standard accounts are limited to 3 webhook notifications a day; all supporter roles get unlimited notifications, [learn more here.](https://octoeverywhere.com/supporter?source=web_hook_dev_doc)
14. **Third Layer Complete**
    - Just like FirstLayerComplete, but this fires on the third layer. Some users might find it more useful to check their print after the first few layers are done or both! It's up to you!
15. **Bed Cooldown Complete**
    - Fired when the print bed has cooled down after a print ends.


### POST Responses And Constraints

**Your webhook should return HTTP 200 OK.** If your webhook fails too many times in a row, the webhook will be disabled. You can setup the webhook again on the notification page.

**The HTTP request will timeout in 10 seconds.** Your endpoint must return a 200 OK before the timeout or the request will be considered a failure.

If you have any issues, questions, or feature requests, reach out to our [development team](https://octoeverywhere.com/support). We would love to hear from you!
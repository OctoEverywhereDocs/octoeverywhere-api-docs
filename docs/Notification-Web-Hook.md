# OctoEverywhere Notification WebHook

OctoEverywhere's powerful [notification](https://octoeverywhere.com/notifications) engine gets even better with custom webhooks! Webhooks allow anyone in our community to create a public internet endpoint that can receive realtime REST calls when printer notifications are fired.

### What Can You Do With WebHooks?

With a little bit of effort and technical knowledge, your imagination is the limit! You could...

- Play the Mario Kart race start sound anytime you begin a print.
- Ring a siren and flash lights when [Gadget](https://octoeverywhere.com/gadget) detects a print failure.
- Make your [Emo](https://living.ai/emo/) dance when a print completes successfully.
- Tweet realtime print progress and full-resolution snapshots as you're printing.

What can you come up with? We would love to see your creations, [Tweet them to us!](https://twitter.com/octoeverywhere)

### Wow! How do I set this up?

This does take some technical knowledge. Once you're able to set up a publically internet-accessible REST endpoint, our service can call to send notifications. When your endpoint is ready, go to the [notification setup page for webhooks,](https://octoeverywhere.com/notifications?handler=webhook) insert the link, enable whatever notifications you would like to receive, and you're done.

### What is sent to the REST endpoint?

The request is an **HTTP POST** request with a json body payload. **Note that over time we might add to the information to the JSON object, but we will never remove existing data.** The JSON body is defined as:

- **PrinterId**
  - (string) This is a unique id that defines your printer. This ID will not change as long as your printer is attached to your account. To find the Printer ID for each of your printers, [view this page](https://octoeverywhere.freshdesk.com/en/support/solutions/articles/69000795523-find-your-printer-id)
- **PrintId**
  - (string) A string that's unique for each print. This string is created when a print starts and remains until the print is complete or stopped. This is useful for tracking which notifications are associated with which print jobs.
- **EventType**
	- (int, enum) This enum maps to the notification type. The types are defined below.
- **PrinterName**
  - (string) The name you assigned your printer. This name will change if the printer is renamed on OctoEverywhere.
- **SnapshotUrl**
	- (string, optional) If a snapshot can be taken, this will be a URL where the image can be viewed or downloaded. Note this image URL will only remain valid for about 7 days.
- **QuickViewUrl**
  - (string) A url to OctoEverywhere's Quick View, which provides a secure internet based way to quickly view the full printer state, pause, and cancel prints.
- **FileName**
	-(string, optional) the file name in OctoPrint for the current file being printed.
- **DurationSec**
	-(int, optional) the duration of the print, since the start, in seconds.
- **Progress**
	-(int, optional) the current print progress as a percentage. Where 0 is 0% and 100 is 100%.
- **TimeRemaningSec**
	- (int, optional) the amount of time estimated to be remaining by OctoPrint, in seconds.
- **ZOffsetMM**
	- (int, optional) the current z-axis offset in millimeters.
- **Error**
	- (string, optonal) for EventTypes that indicate an error, this string might contain some kind of error message from OctoPrint describing the issue.

### EventType

The following defines the EventType enum. *Remember that as event types are added, they will be appended to this list*

1. Started
2. Complete
3. Failed
4. Paused
5. Resumed
6. PrintProgress
    - Fired due to a % progress milestone or a timer event.
7. GadgetPossibleFailureWarning
    - Gadget thinks the print might have a failure.
8. GadgetPausedPrintDueToFailure
    - Gadget paused the print because it detected a failure.
    - Only fires when Gadget Smart Pause is enabled.
9. Error
    - Fired due to any error from OctoPrint.
10. FirstLayerComplete
11. FilamentChangeRequired
12. UserInteractionRequired
    - Fires when the printer requests user interaction.
13. NonSupporterNotificationLimit
    - Fired when the user's account hits the daily notification limit.
    - Standard accounts are limited to 3 webhook notifications a day; all supporter roles get unlimited notifications.  [Learn More](https://octoeverywhere.com/supporter?source=web_hook_dev_doc)






### Other Details To Note:

- If your webhook fails to return **HTTP 200** too many times in a row, the webhook will be disabled on your account. But don't worry; you can re-add it anytime using the notification setup system.
- Our system will respect a maximum request timeout of 10 seconds. Your endpoint should return a 200 OK before the timeout, or the request will be considered a failure.
- If you have any issues, questions, or feature requests, reach out to our [development team](https://octoeverywhere.com/support), and we would be happy to help!
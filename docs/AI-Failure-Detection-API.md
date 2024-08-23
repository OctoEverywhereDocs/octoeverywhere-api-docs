---
stoplight-id: 3xadck728cc0t
---

# AI Failure Detection APIs 🤖

OctoEverywhere empowers 3D printing with free and unlimited 3D printing AI failure detection with a service we lovingly Gadget. Gadget processes **over 4 million images** a day, consuming **over 430 gigabytes** of data each day.

Our AI failure detection service uses bleeding-edge ML models to process millions of images daily with incredibly high accuracy. The models are continuously updated with real-time feedback from the community, making them more adaptive, robust, and accurate every day. 

To complement our cutting-edge ML image inference models, OctoEverywhere developed a temporal combination model that looks at several signals to make key decisions, like when to warn the user about a possible failure or issue a command to pause the print.

Putting these systems together on our high availability worldwide server network produces incredible results. Our community agrees that OctoEverywhere offers the most accurate and timely AI failure detection on the market today.

## You Can Do It To! 🚀

OctoEverywhere AI Failure Detection APIs give you access to the same incredible AI failure detection service used by OctoEverywhere's community. The API is designed to be simple and easy to use, making integration a one-day job. Your app or service simply needs to provide jpg images to our service, and our ML service will return everything you need to understand the print quality and take action if required.

## Pricing 

You only pay for what you use. **All developers get 5000 API calls per month 100% for free. 5k API calls will get you about 55 hours of free AI failure detection per month.** 

Our pricing model additive tiers, the more you need, the less it costs.

- 0-5000 API calls : 100% Free
- 5k-1m API calls - $0.0004 USD per API call.
- 1m-20m API calls - $0.00025 USD per API call.
- 20m+ API calls - $0.00015 USD per API call.



We offer volume pricing for customers with high API demands. [Please get in touch with us to discuss details.](https://octoeverywhere.com/support?source=dev_docs_ai_failure_detection)

## API Overview

### 1) Create A Print Context

Every new print must create a new context for the ML models to operate on. To make a new context, make a POST request to the [Create Context API](https://octoeverywhere.stoplight.io/docs/octoeverywhere-api-docs/sd17hl8caalt1-create-context), providing your API key and the optional AI sensitivity parameters.

The [Create Context API](https://octoeverywhere.stoplight.io/docs/octoeverywhere-api-docs/sd17hl8caalt1-create-context) will return the Context ID, a primary processing API URL, and a fallback API URL. The primary processing URL is where you should make all future Process API calls unless they fail. If there's a failure from the primary processing URL, you should switch to the fallback URL and use it for all Process API calls for the remainder of the context lifetime. [More details.](url)

### 2) Process An Image

To start the AI failure detection, send a jpg image to the [Process API](https://octoeverywhere.stoplight.io/docs/octoeverywhere-api-docs/ck1qrradzvhim-process-api) using the URL returned from the [Create Context API](https://octoeverywhere.stoplight.io/docs/octoeverywhere-api-docs/sd17hl8caalt1-create-context) call. 

The [Process API](https://octoeverywhere.stoplight.io/docs/octoeverywhere-api-docs/ck1qrradzvhim-process-api) accepts POST requests with the image included using the `multipart/form-data` Content Type. Multipart form data payloads are built into all modern language HTTP libraries, including Python, CPP, C#, Java, JS, and more. [More details.]()

The [Process API](https://octoeverywhere.stoplight.io/docs/octoeverywhere-api-docs/ck1qrradzvhim-process-api) is where the magic happens. Using our ML image models, the Process API will process the new image into the previous context. The temporal combination model will then produce an output based on the past context, current image, and other signals.   

- **Score** `int` - This is the temporal combination model print quality score. The score ranges from 0-100. 0 indicates a perfect print and 100 indicates a strong probability of a failure. You can use this score directly to indicate the current print quality to the user.
- **WarningSuggested** `boolean` Set to true if the temporal combination model is confident there might be a print issue and the user should be informed. This decision is based on many signals and is only sent when there's high confidence of the warning state.
- **PauseSuggested** `boolean` - Set to true if the temporal combination model is confident that there is probably a print failure and that the print should be paused. This decision is based on many signals and is only sent when there's high confidence that the print has failed.
- **NextProcessIntervalSec** `int` - This indicates minimum amount of time that should elapse from this call before the next Process API call. This value is dynamic based on our server load; it averages around 40 seconds. Your integration is free to choose a longer time if desired.

### 3) Repeat

That's it! Continuously call the Process API with a new snapshot to keep the AI model updated with the print state. 

You must wait at least the time interval required by `NextProcessIntervalSec`, but you may wait for a longer interval if you wish. Each time you call the Process API more signals will be gathered by the temporal combination model, which will allow it to gain confidence if the print's state.

## Errors

Coming soon! 

## High Availability

OctoEverywhere's AI failure detection service achieves high availability and robustness by controlling the rate at which process APIs are called and load balancing between regions.

### ProcessRequestUrl & FallbackProcessRequestUrl

These values are used to load balance AI requests between regions. They are returned by the Create Context API and should be for the lifetime of the context. 

The `ProcessRequestUrl` should be used for all Process API calls unless the call returns a failure. If any failure is returned, be it from networking, server, or otherwise, the `FallbackProcessRequestUrl` can then be used for the remainder of the context lifetime.

The `ProcessRequestUrl` allows the service to drive traffic intelligently when the service is healthy. If something in the service fails, the `FallbackProcessRequestUrl` acts as a global routing URL that ensures the Process API calls will be handled by a different region.


### NextProcessIntervalSec

The `NextProcessIntervalSec` value is used to globally set the rate at which the servers can accept Process requests. The goal is to accept as many process requests as possible for the best failure detection possible. However, during peak times, the interval will be increased so the servers don't get overwhelmed.

Your service or app is always welcome to call the Process API at any rate, as long as it's longer than the `NextProcessIntervalSec` last returned. Calling the Process API more frequently will produce better and faster print failure detection but will cost more due to increased API usage.




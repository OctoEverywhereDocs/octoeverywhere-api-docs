---
stoplight-id: 3xadck728cc0t
---

# AI Failure Detection APIs 🤖

OctoEverywhere empowers the 3D printing community with free and unlimited AI print failure detection with a service we lovingly [Gadget](https://octoeverywhere.com/gadget?source=dev_docs_ai). **Gadget processes over 4 million images and 430 gigabytes of data daily. 🤯**

Our AI failure detection service uses bleeding-edge ML models to process millions of images with incredibly high accuracy. The models are continuously updated with real-time feedback from the community, making them more adaptive, robust, and accurate every day.

To complement our cutting-edge ML image inference models, OctoEverywhere developed a temporal combination model that looks at several signals to make key decisions, like when to warn the user about a possible failure or issue a command to pause the print.

Combining these systems on our high availability worldwide server network produces incredible results. Our community agrees that OctoEverywhere offers the most accurate and timely AI failure detection on the market today.

## You Can Do It To! 🚀

**OctoEverywhere AI Failure Detection APIs give you access to the same incredible AI failure detection service used by OctoEverywhere's community.** The API is designed to be simple and easy to use, making integration a one-day job. Your app or service simply needs to provide jpg images to our service, and our ML service will return everything you need to understand the print quality and take action if required.

## Pricing 

You only pay for what you use. **All developers get 5000 API calls per month 100% for free. 5k API calls will get you about 55 hours of free AI failure detection per month.** 

Our pricing model additive tiers, the more you need, the less it costs.


Number Of API Calls | Price Per API Call in USD
---------|---------
0-5,000 | Free
5,000-1m | $0.0004
1m-20m |  $0.00025
20m+ | $0.00015



We offer volume pricing for customers with high API demands. [Please get in touch with us to discuss details.](https://octoeverywhere.com/support?source=dev_docs_ai_failure_detection)

## SDKs

We currently offer an [AI Failure Detection SDK for Python,](https://github.com/OctoEverywhere/Gadget-Python-Sdk) and more languages will be introduced soon.

**We recommend using the [Python SDK](https://github.com/OctoEverywhere/Gadget-Python-Sdk/blob/main/gadgetsdk/_gadgetinspectionsession.py) as a reference implementation.** The SDK is a great way to understand the APIs, parameters, and calling patterns of the APIs.

## API Overview

### 1) Create A Print Context

Every new print must create a new context for the ML models to operate on. There is no charge to call this API. To make a new context, make a POST request to the [Create Context API](https://octoeverywhere.stoplight.io/docs/octoeverywhere-api-docs/kgomtjwkt3dj9-create-context), providing your API key and the optional AI confidence parameters. [Full details.](https://octoeverywhere.stoplight.io/docs/octoeverywhere-api-docs/sd17hl8caalt1-create-context)

The [Create Context API](https://octoeverywhere.stoplight.io/docs/octoeverywhere-api-docs/kgomtjwkt3dj9-create-context) will return the Context ID, a primary processing API URL, and a fallback API URL. The primary processing URL is where you should make all future [Process API](https://octoeverywhere.stoplight.io/docs/octoeverywhere-api-docs/hb9xvo995a4px-process) calls unless they fail. If there's a failure from the primary processing URL, you should switch to the fallback URL and use it for all [Process API](https://octoeverywhere.stoplight.io/docs/octoeverywhere-api-docs/hb9xvo995a4px-process) calls for the remainder of the context lifetime. [Full details.](https://octoeverywhere.stoplight.io/docs/octoeverywhere-api-docs/sd17hl8caalt1-create-context)

### 2) Process An Image

To start the AI failure detection, send a jpg image to the [Process API](https://octoeverywhere.stoplight.io/docs/octoeverywhere-api-docs/hb9xvo995a4px-process) using the `ProcessRequestUrl` URL returned from the [Create Context API](https://octoeverywhere.stoplight.io/docs/octoeverywhere-api-docs/kgomtjwkt3dj9-create-context) call. 

The [Process API](https://octoeverywhere.stoplight.io/docs/octoeverywhere-api-docs/hb9xvo995a4px-process) accepts POST requests with the image included using the `multipart/form-data` Content Type. Multipart form data payloads are built into all modern language HTTP libraries, including Python, C#, Java, JS, C++, and more. [More details.](https://octoeverywhere.stoplight.io/docs/octoeverywhere-api-docs/hb9xvo995a4px-process)

The [Process API](https://octoeverywhere.stoplight.io/docs/octoeverywhere-api-docs/hb9xvo995a4px-process) is where the magic happens. Using our ML image models, the Process API will process the new image into the previous context. The temporal combination model will then produce an output based on the past context, current image, and other signals.   

- **Score** `int` - This is the temporal combination model print quality score. The score ranges from 0-100. 0 indicates a perfect print and 100 indicates a strong probability of a failure. You can use this score directly to indicate the current print quality to the user.
- **WarningSuggested** `boolean` Set to true if the temporal combination model is confident there might be a print issue and the user should be informed. This decision is based on many signals and is only sent when there's high confidence of the warning state.
- **PauseSuggested** `boolean` - Set to true if the temporal combination model is confident that there is probably a print failure and that the print should be paused. This decision is based on many signals and is only sent when there's high confidence that the print has failed.
- **NextProcessIntervalSec** `int` - This indicates minimum amount of time that should elapse from this call before the next [Process API](https://octoeverywhere.stoplight.io/docs/octoeverywhere-api-docs/hb9xvo995a4px-process) call. This value is dynamic based on our server load; it averages around 40 seconds. Your integration is free to choose a longer time if desired.

### 3) Repeat

That's it! Continuously call the [Process API](https://octoeverywhere.stoplight.io/docs/octoeverywhere-api-docs/hb9xvo995a4px-process) with a new snapshot to keep the AI model updated with the print state. 

Your program **must wait for time interval** required by `NextProcessIntervalSec` between [Process API](https://octoeverywhere.stoplight.io/docs/octoeverywhere-api-docs/hb9xvo995a4px-process) calls, but your program can also opt to wait longer if desired. Each time you call the [Process API](https://octoeverywhere.stoplight.io/docs/octoeverywhere-api-docs/hb9xvo995a4px-process) more signals will be gathered by the temporal combination model, which will allow it to gain confidence in the print's state.

## Error Handling

If any API fails, it will return a non HTTP 200 error code and a common json error object as the HTTP response body on failure.

`"ErrorType":"OE_INTERNAL_ERROR", "ErrorDetails"; "A string with error details."`

`ErrorType` will have one of the well-known error strings below. `ErrorDetails` will be a string with details on the failure.

### Well Known Error Types

Client Actionable Errors:

- `OE_BAD_ARGS` - The arguments required to pass for the API were missing or invalid.
- `OE_ARGS_PARSE_FAILED` - The API handler failed to parse the request json body.
- `OE_CONTEXT_RATE_LIMITED` - Indicates this context id has made too many requests and has been rate-limited.
- `OE_IMAGE_DECODE_FAILED` - Only returned from the [Process API](https://octoeverywhere.stoplight.io/docs/octoeverywhere-api-docs/hb9xvo995a4px-process) indicating the jpeg image failed to decode.
- `OE_INVALID_API_KEY` - The API key in the `X-API-Key` header was missing or incorrect.
- `OE_API_KEY_BLOCKED_PAYMENT_FAILED` - The API key account owner has an outstanding balance that needs to be paid before the [Process API](https://octoeverywhere.stoplight.io/docs/octoeverywhere-api-docs/hb9xvo995a4px-process) can be used.

Backend Server Errors:


- `OE_BACKEND_THROTTLED` - The [Process API](https://octoeverywhere.stoplight.io/docs/octoeverywhere-api-docs/hb9xvo995a4px-process) request had to be temporally throttled. Try again after the next delay interval.
- `OE_INTERNAL_ERROR` - A generic error for something going wrong in the service. Try again after the next delay interval.


## High Availability

OctoEverywhere's AI failure detection service achieves high availability and robustness by controlling the rate at which [Process APIs](https://octoeverywhere.stoplight.io/docs/octoeverywhere-api-docs/hb9xvo995a4px-process) are called and load balancing between regions.

### ProcessRequestUrl & FallbackProcessRequestUrl

These values are used to load balance AI requests between regions. They are returned by the [Create Context API](https://octoeverywhere.stoplight.io/docs/octoeverywhere-api-docs/kgomtjwkt3dj9-create-context) and should be for the lifetime of the context. 

The `ProcessRequestUrl` should be used for all [Process API](https://octoeverywhere.stoplight.io/docs/octoeverywhere-api-docs/hb9xvo995a4px-process) calls unless the call returns a failure. If any failure is returned, be it from networking, server, or otherwise, the `FallbackProcessRequestUrl` can then be used for the remainder of the context lifetime.

The `ProcessRequestUrl` allows the service to drive traffic intelligently when the service is healthy. If something in the service fails, the `FallbackProcessRequestUrl` acts as a global routing URL that ensures the [Process API](https://octoeverywhere.stoplight.io/docs/octoeverywhere-api-docs/hb9xvo995a4px-process) calls will be handled by a different region.


### NextProcessIntervalSec

The `NextProcessIntervalSec` value is used to globally set the rate at which the servers can accept Process requests. The goal is to accept as many process requests as possible for the best failure detection possible. However, during peak times, the interval will be increased so the servers don't get overwhelmed.

Your service or app is always welcome to call the [Process API](https://octoeverywhere.stoplight.io/docs/octoeverywhere-api-docs/hb9xvo995a4px-process) at any rate, as long as it's longer than the `NextProcessIntervalSec` last returned. Calling the [Process API](https://octoeverywhere.stoplight.io/docs/octoeverywhere-api-docs/hb9xvo995a4px-process) more frequently will produce better and faster print failure detection but will cost more due to increased API usage.




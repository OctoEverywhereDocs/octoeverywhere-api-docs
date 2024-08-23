---
stoplight-id: 3xadck728cc0t
---

# AI Failure Detection APIs

OctoEverywhere empowers 3D printing with free and unlimited 3D printing AI failure detection, which we love named Gadget. Gadget processes over 4 million images a day, consuming over 430 gigabytes of data each day.

Our AI failure detection service uses bleeding-edge ML models to process millions of images daily with incredibly high accuracy. Our models are continuously updated with real-time feedback from the community, making them more adaptive and accurate every day. 

To complement our cutting-edge ML image inference models, OctoEverywhere developed a temporal combination model that draws in several signals to make key decisions on when to warn the user about a possible failure or issue a command to pause the print.

All of these systems are combined using our worldwide server network to provide incredible results. Our community agrees that OctoEverywhere provides the most accurate and timely AI failure detection on the market today.

## You Can Do It To! 🚀

OctoEverywhere AI Failure Detection APIs give you access to the same incredible AI failure detection service used by OctoEverywhere's customers. Our APIs are designed to be simple and easy to use. Your app or service simply needs to provide jpeg images, and our servers will do the work to return you a simple and easy to take action on output.

## Pricing 

You only pay for what you use. Each API call costs USD $0.0004, resulting in a cost of about $0.036 per hour. We give all developers 5000 free calls per month or about 55 hours of free AI failure detection.

We offer volume pricing for customers with high API demands. [Please get in touch with us to discuss details.](https://octoeverywhere.com/support?source=dev_docs_ai_failure_detection)

## API Overview

### 1) Create A Context

Each new print must create a new context for the ML models to operate on. To make a new context, make a POST request to the Create Context API, providing your API key and the optional AI sensitivity parameters.

The Create Context API will return the context ID, a primary processing API URL, and a fallback API URL. The primary processing URL is where you should make all future Process API calls unless they fail. If there's a failure from the primary processing URL, you should switch to the fallback URL and use it for all Process API calls for the remainder of the context lifetime.

### 2) Process An Image

To start the AI failure detection, send a jpg image to the Process API using the URLs you got from the Create Context API call. The Process API accepts POST requests with the image included as a multipart Content Type. Multipart payload types are built into all modern language HTTP libraries, including Python, CPP, C#, Java, JS, and more. 

The Process API will return a score, two booleans, and a minimal time interval before the next Process API call can be made. 

- **Score** `int` - The score ranges from 0-100, which indicates the probability of a failure, where 0 is a perfect print, and 100 is a very likely failure. Your application or service can use this value directly, or you can use our temporal combination model output flags that indicate if you should show a warning to the user or pause the print. 
- **WarningSuggested** `boolean` - This is the output of the temporal combination model when it suggests a warning of a possible print failure should be sent to the user. This prediction is based on many signals including the `Score`.
- **PauseSuggested** `boolean` - This is the output of the temporal combination model when it suggests pausing the print due to the high likelihood of a print failure. This prediction is based on many signals including the `Score`.
- **NextProcessIntervalSec** `int` - The minimum amount of time that should elapse from this call before the next Process API call. This value is dynamic based on our server load; it averages around 40 seconds.

### 3) Repeat

That's it! Keep calling the Process API with a new snapshot at least or after the minimum next Process time interval. Each time you call the Process API more signals will be gathered by the temporal combination model, which will allow it to gain confidence if the print is a good or bad state.

## Errors

TODO 

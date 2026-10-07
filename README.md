# Dataverse Request Inspector (Azure Function)

An HTTP-triggered Azure Function built on the **.NET Isolated Worker Model** designed to intercept, log, and inspect incoming webhook requests from **Microsoft Dataverse**. It serves as a debugging utility to visualize execution contexts, headers, payloads, and target record attributes.

This project was built following the Microsoft Learn module: [Integrate Dataverse Azure solutions](https://learn.microsoft.com/en-us/training/modules/integrate-dataverse-azure-solutions/).

## Key Features

* **HTTP Trigger Endpoint:** Exposes a secure function-level endpoint (`[HttpTrigger]`) to receive incoming Dataverse webhooks.
* **Payload Inspection:** Automatically parses and pretty-prints the JSON request body using `Newtonsoft.Json`.
* **Target Attribute Logging:** Extracts the Dataverse execution context data (`InputParameters["Target"]`) and loops through changing `Attributes` keys and values.
* **Comprehensive Diagnostics:** Captures and logs incoming request headers, query string parameters, and the initiating user's ID.

## How It Works

1. **Dataverse Event Triggers:** A plugin step or webhook is fired in Dataverse (e.g., on the creation or update of an account).
2. **Payload Serialization:** Dataverse serializes its entire asynchronous execution context into a JSON payload and transmits it via an HTTP POST request to this function.
3. **Logging & Extraction:** The function writes headers, query paths, and the formatted JSON body to Azure Monitor and/or App Insights (not configured by default) by safe-extracting the primary entity `Target` properties.
4. **Response:** Returns an `HTTP 200` status along with the `InitiatingUserId` string of the user who performed the action in Dataverse.

## Prerequisites & Configuration

* **.NET SDK** (matching your target isolated worker runtime version)
* **Azure Functions Core Tools**
* A configured **Microsoft Dataverse Webhook** pointing to this function's deployed or local URL.

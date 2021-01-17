---
title: Docdown API Reference

includes:
  - errors

code_clipboard: true
---

# Introduction

Welcome to the <a href="https://docdown.io">Docdown API</a>.

Docdown automates your document workflows.

1. Upload your PDF documents and define the fillable areas.
2. Setup your custom workflow with visual Workflow Builder.
3. Use online forms or this API to trigger your workflow and generate your document.

# Authentication

> To authorize your requests:

```shell
curl "https://api.docdown.io/v1/" \
  -H "Authorization: bearer API_KEY"
```

> Make sure to replace `API_KEY` with your API key.

Docdown uses API keys to allow access to the API. You can get your API key <a href="https://app.docdown.io/login?redirect=/settings/account" target="_blank">here</a>.

Docdown expects the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: bearer API_KEY`

<aside class="notice">
Replace <code>API_KEY</code> with your personal API key.
</aside>

# Incoming Webhook Trigger

Add an `Incoming Webhook` trigger step in your workflow from the Workflow Editor and select the document to be used in this workflow. The webhook endpoint and request schema is then provided to be used to trigger this workflow.

<!-- ![Defining Incoming Webhoooks](/images/incoming-webhooks.jpg) -->

## Trigger a workflow

> To trigger the workflow:

```shell
curl "https://api.docdown.io/v1/trigger/WORKFLOW_ID" \
  -H "Authorization: bearer API_KEY" \
  -H "Content-Type: application/json" \
  -d json
```

> The above command returns JSON structured like this:

```json
{
  "workflowRunId": "5196de15-b03d-4687-bc7a-570b53890534",
  "resourceUrl": "https://api.docdown.io/v1/resource/file/5196de15-b03d-4687-bc7a-570b53890534?mode=blob"
}
```

### HTTP Request

`POST https://api.docdown.io/v1/trigger/WORKFLOW_ID`

<aside class="notice">
Replace the endpoint with the endpoint from incoming webhook step.
</aside>

# Resource

`Resource` is the output of your workflow execution. Both the generated files and output data
of each workflow execution can be requested.

## Get the generated PDF of specific workflow execution

> To trigger the workflow:

```shell
curl "https://api.docdown.io/v1/resource/file/WORKFLOW_RUN_ID" \
  -H "Authorization: bearer API_KEY"
```

> The above command returns either binary PDF file or base64 encoded string.

### HTTP Request

`GET https://api.docdown.io/v1/resource/file/WORKFLOW_RUN_ID`

### Query Parameters

| Parameter | Default  | Description                                                                           |
| --------- | -------- | ------------------------------------------------------------------------------------- |
| mode      | "base64" | If set to "blob", the result will be in binary format, else in base64 encoded string. |

<aside class="notice">
Replace the <code>WORKFLOW_ID</code> from the endpoint URL given in the Incoming Webhook step of the  Workflow Editor.
</aside>

## Get the output data of the workflow execution

```shell
curl "https://api.docdown.io/v1/resource/file/WORKFLOW_RUN_ID" \
  -H "Authorization: bearer API_KEY"
```

> The above command returns JSON structured like this:

```json
{
  "field_1": "field1_value",
  "field_2": "field2_value"
}
```

### HTTP Request

`GET https://api.docdown.io/v1/resource/file/WORKFLOW_RUN_ID`

<aside class="notice">
Replace the <code>WORKFLOW_ID</code> from the endpoint URL given in the Incoming Webhook step of the  Workflow Editor.
</aside>

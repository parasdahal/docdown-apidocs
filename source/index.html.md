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

### HTTP Request

`GET https://api.docdown.io/v1/auth`

> To verify your identity:

```shell
curl "https://api.docdown.io/v1/auth" \
  -H "Authorization: bearer API_KEY"
```

> The above endpoint returns JSON structured like this:

```json
{
  "id": "5196dq15-b03d-4687-bd7x-570b53890534",
  "name": "My Name",
  "email": "my@email.com"
}
```

# Documents

You can request an array of all the documents you have setup in your account. The endpoint responds with `Document` objects, which includes `id`, `name` and `fields` attributes.

The `fields` attribute is a list of fields you have setup in the document, each with the object structure `{"name":"Field", "type":"string"}`. The `type` attribute can have the following values: `string`, `number`, `boolean` and `image/png` (base64 encoded PNG image).

> To get an array of documents:

```shell
curl "https://api.docdown.io/v1/documents" \
  -H "Authorization: bearer API_KEY"
```

> The above endpoint returns JSON structured like this:

```json
[
  {
    "id": "ad97a8ed-1aa3-40fc-96ee-ae68c71eceb9",
    "name": "fw9.pdf",
    "fields": [
      {
        "name": "field1",
        "type": "string"
      },
      {
        "name": "field2",
        "type": "boolean"
      },
      {
        "name": "field3",
        "type": "number"
      },
      {
        "name": "field4",
        "type": "image/png"
      }
    ]
  }
]
```

### HTTP Request

`GET https://api.docdown.io/v1/documents`

# Workflows

You can request an array of all the workflows you have setup in your account. The endpoint responds with `Workflow` objects, which includes `id`, `name`, `active` and `steps` attributes.

> To get an array of workflows:

```shell
curl "https://api.docdown.io/v1/workflows" \
  -H "Authorization: bearer API_KEY"
```

> The above endpoint returns JSON structured like this:

```json
[
  {
    "id": "bad7de6f-2d5f-485b-97cc-1bb69bf01955",
    "name": "Google Form Workflow",
    "active": true,
    "steps": [
      {
        "id": "dbf31408-f296-49b1-81db-2ce79ca74ef5",
        "name": "Incoming Webhook",
        "type": "incomingWebhook",
        "order": 1
      },
      {
        "id": "b5c519af-7c82-4231-b91e-9b8c5716d0fe",
        "name": "Email",
        "type": "email",
        "order": 2
      }
    ]
  }
]
```

### HTTP Request

`GET https://api.docdown.io/v1/workflows`

# Workflow History

You can request an array of all the workflows executions for a particular Workflow. The endpoint responds with `History` objects, which contains `id`, `workflow`, `workflowId` and `file_name`, `file_url` attributes. This object also includes the received value for fields of each step of the workflow, prefixed with `step{order}-`.

> To get an array of completed workflow executions:

```shell
curl "https://api.docdown.io/v1/workflows/completed/<WORFKLOW_ID>" \
  -H "Authorization: bearer API_KEY"
```

> The above endpoint returns JSON structured like this:

```json
[
  {
    "id": "1f1fbfb7-9001-4256-a85c-6c291525aa97",
    "workflow": "Google Form workflow",
    "workflowId": "2de38ef5-5677-49b6-84d1-875b67e35020",
    "file_name": "file.pdf",
    "file_url": "https://api.docdown.io/v1/resource/file/1f1fbfb7-9001-4256-a85c-6c291525aa97?mode=blob",
    "step1-first_name": "",
    "step1-last_name": ""
  }
]
```

### HTTP Request

`GET https://api.docdown.io/v1/workflows/completed/<WORKFLOW_ID>`

### Parameters

| Parameter   | Description            |
| ----------- | ---------------------- |
| WORKFLOW_ID | The id of the worklfow |

# Incoming Webhook Trigger

Add an `Incoming Webhook` trigger step in your workflow from the Workflow Editor and select the document to be used in this workflow. The webhook endpoint and request schema is then provided to be used to trigger this workflow.

<!-- ![Defining Incoming Webhoooks](/images/incoming-webhooks.jpg) -->

## Trigger a workflow

> To trigger the workflow:

```shell
curl "https://api.docdown.io/v1/trigger/<WORKFLOW_ID>" \
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

`POST https://api.docdown.io/v1/trigger/<WORKFLOW_ID>`

### Parameters

| Parameter   | Description            |
| ----------- | ---------------------- |
| WORKFLOW_ID | The id of the worklfow |

# Resource

`Resource` is the output of your workflow execution. Both the generated files and output data
of each workflow execution can be requested.

## Get the generated PDF of specific workflow execution

> To trigger the workflow:

```shell
curl "https://api.docdown.io/v1/resource/file/<WORKFLOW_RUN_ID>" \
  -H "Authorization: bearer API_KEY"
```

> The above command returns either binary PDF file or base64 encoded string.

### HTTP Request

`GET https://api.docdown.io/v1/resource/file/<WORKFLOW_RUN_ID>`

### Parameters

| Parameter       | Description                      |
| --------------- | -------------------------------- |
| WORKFLOW_RUN_ID | The id of the worklfow execution |

### Query Parameters

| Parameter | Default  | Description                                                                           |
| --------- | -------- | ------------------------------------------------------------------------------------- |
| mode      | "base64" | If set to "blob", the result will be in binary format, else in base64 encoded string. |

<aside class="notice">
Replace the <code>WORKFLOW_RUN_ID</code> with <code>workflowRunId</code> from the response of <code>/v1/trigger/WORKFLOW_ID</code>.
</aside>

## Get the output data of the workflow execution

```shell
curl "https://api.docdown.io/v1/resource/data/<WORKFLOW_RUN_ID>" \
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

`GET https://api.docdown.io/v1/resource/data/<WORKFLOW_RUN_ID>`

### Parameters

| Parameter       | Description                      |
| --------------- | -------------------------------- |
| WORKFLOW_RUN_ID | The id of the worklfow execution |

<aside class="notice">
Replace the <code>WORKFLOW_RUN_ID</code> with <code>workflowRunId</code> from the response of <code>/v1/trigger/WORKFLOW_ID</code>.
</aside>

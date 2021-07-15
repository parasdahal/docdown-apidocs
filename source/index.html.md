---
title: Docdown API Reference

includes:
  - errors
search: true
---

# Introduction

Welcome to the <a href="https://docdown.io">Docdown API</a>.

Docdown automates your document generation workflows.

1. Upload your PDF documents and define the template fields.
2. Setup your custom automations with visual Workflow Builder.
3. Use web forms, integrations, or this REST API to trigger your workflow and generate your document.

# Authentication

> To verify your authentication:

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

> Make sure to replace `API_KEY` with your API key.

Docdown uses API keys to allow access to the API. You can get your API key <a href="https://app.docdown.io/login?redirect=/settings/account" target="_blank">here</a>.

Docdown expects the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: bearer API_KEY`

<aside class="notice">
Replace <code>API_KEY</code> with your personal API key.
</aside>

### HTTP Request

`GET https://api.docdown.io/v1/auth`

# Documents

> To get all documents in your Docdown account:

```shell
curl "https://api.docdown.io/v1/documents" \
  -H "Authorization: bearer API_KEY"
```

> The above endpoint returns JSON structured like this:

```json
[
  {
    "id": "2118cff9-dd53-4355-b103-c4ba30dd08fe",
    "name": "certificate.pdf",
    "fields": [
      {
        "key": "photo",
        "label": "Photo",
        "required": false,
        "type": "string",
        "helpText": "Image URL."
      },
      {
        "key": "certify",
        "label": "Certify",
        "type": "boolean",
        "required": false,
        "helpText": "True or non-empty value marks the field as checked."
      },
      {
        "key": "full_name",
        "label": "Full Name",
        "type": "string",
        "required": false,
        "helpText": "Text field that expects text data."
      },
      {
        "key": "date_of_signature_dd_mm_yyyy",
        "label": "Date of Signature (DDMMYYYY)",
        "type": "string",
        "required": false,
        "helpText": "Text field that expects text data."
      },
      {
        "key": "signature",
        "label": "Signature",
        "children": [
          {
            "key": "signerName",
            "type": "string",
            "required": true,
            "helpText": "Full legal name of the signer."
          },
          {
            "key": "signerEmail",
            "type": "string",
            "required": true,
            "helpText": "Email address of the signer. Signer will receive completed document and audit trail at this address after completion."
          },
          {
            "key": "signature",
            "type": "string",
            "helpText": "Signature image URL.",
            "required": true
          }
        ]
      }
    ]
  }
]
```

> To get a single document:

```shell
curl "https://api.docdown.io/v1/documents/get/<DOCUMENT_ID>" \
  -H "Authorization: bearer API_KEY"
```

You can request an array of all the documents you have setup in your account. The endpoint responds with `Document` objects, which includes `id`, `name` and `fields` attributes.

The `fields` attribute is a list of field schema for all the fields you have setup in the document. Each schema object represents what the API expects when sending data to the API.

### HTTP Request

`GET https://api.docdown.io/v1/documents`

### Parameters

| Parameter   | Description            |
| ----------- | ---------------------- |
| DOCUMENT_ID | The id of the document |

# Workflows

> To get an array of workflows:

```shell
curl "https://api.docdown.io/v1/workflows" \
  -H "Authorization: bearer API_KEY"
```

> The above endpoint returns JSON structured like this:

```json
[
  {
    "id": "2325667e-96d0-4063-863b-8a813aeef812",
    "name": "Generate certificate from Zapier",
    "active": true,
    "steps": [
      {
        "id": "8b1dea48-5370-45ca-aaed-abd3ef2ccea0",
        "name": "Incoming Webhook",
        "type": "incomingWebhook",
        "order": 1
      },
      {
        "id": "58a36313-17a2-457b-ac4c-e2a3e73f57b5",
        "name": "Email",
        "type": "email",
        "order": 2
      }
    ]
  }
]
```

You can request an array of all the workflows you have setup in your account. The endpoint responds with `Workflow` objects, which includes `id`, `name`, `active` and `steps` attributes.

### HTTP Request

`GET https://api.docdown.io/v1/workflows`

# Executing a Workflow

To execute a workflow, send a POST request to `https://api.docdown.io/v1/trigger/<WORKFLOW_ID>` with the field data in the request body.

> To trigger the workflow:

```shell
curl "https://api.docdown.io/v1/trigger/<WORKFLOW_ID>" \
  -H "Authorization: bearer API_KEY" \
  -H "Content-Type: application/json" \
  -d \"{
    "photo": "https://upload.wikimedia.org/wikipedia/commons/thumb/6/68/Joe_Biden_presidential_portrait.jpg/480px-Joe_Biden_presidential_portrait.jpg",
    "certify": true,
    "full_name": "Joe Biden",
    "date_of_signature_dd_mm_yyyy": "01012021",
    "signature":{
        "signerName": "Joe Biden",
        "signerEmail": "potus@us.gov",
        "signature": "https://upload.wikimedia.org/wikipedia/commons/thumb/d/d9/Joe_Biden_Signature.svg/176px-Joe_Biden_Signature.svg.png"
    }\"
  }
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

## Get the generated PDF file from workflow execution

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

## Get the data of the workflow execution

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

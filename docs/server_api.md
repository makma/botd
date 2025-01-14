# Server botd API

### Production endpoint `https://botd.fpapi.io/api/v1`

## API token

There are two possible variants how to pass API token:
1) `Auth-Token` header (preflight requests will be sent)
2) `token` query string parameter.

## POST `/detect`

### Request headers

```js
'Content-Type': 'text/plain'
```

### Request body

```js
{
    "mode": "<mode>",
    "timestamp": 1623762359,
    "performance": 12767,
    "version": "0.0.1",
    "signals": {
        "s1": {
            "state": 1,
            "value": <object>,
        },
        ...
    }
}
```

Receives all needed data from client and builds a report.

`mode` - two modes are supported: `requestId` and `allData`.

Other data are user properties to make an analysis.

### Response body

### `mode` is `allData`:

```json
{
    "bot": {
        "automationTool": {
            "status": "processed",
            "probability": 0,
            "type": "<type>"
        },
        "browserSpoofing": {
            "status": "processed",
            "probability": 0,
            "type": "<type>"
        },
        "searchEngine": {
            "status": "processed",
            "probability": 0,
            "type": "<type>"
        }
    },
    "vm": {
        "status": "processed",
        "probability": 0,
        "type": "<type>"
    },
    "requestId": "<requestId>",
    "ip": "<ipAddress>",
    "tag": "<tag>"
}
```

### `bot.automationTool`

Results of detecting possible browser automation tools.

`automationTool.status` - possible values = `"processed" | "error" | "notEnoughData"`

`automationTool.probability` - if `automationTool.status` is `processed` possible values = `0.0 - 1.0`, otherwise the field won't be presented

`automationTool.type` - optional field, possible values = `"phantomjs" | "headlessChrome" | ...`

### `bot.browserSpoofing`

Results of detecting possible browser spoofing.
For example user agent string says it's Chrome on Windows, but other signals tell it is
Safari on MacOS.

`browserSpoofing.status` - possible values = `"processed" | "error" | "notEnoughData"`

`browserSpoofing.probability` - if `browserSpoofing.status` is `processed` possible values = `0.0 - 1.0`, otherwise the field won't be presented

`browserSpoofing.type` - optional field, possible values = `"userAgent" | "os" | ...`

### `bot.searchEngine`

Results of detecting a legitimate search engine, e.g. Google or Bing.

`searchEngine.status` - possible values = `"processed" | "error" | "notEnoughData"`

`searchEngine.probability` - if `searchEngine.status` is `processed` possible values = `0.0 - 1.0`, otherwise the field won't be presented

`searchEngine.type` - optional field, possible values = `"google" | "bing" | ... `

### `vm`

Results of detecting a virtual machine.

`status` - possible values = `"processed" | "error" | "notEnoughData"`

`probability` - if `status` is `processed` possible values = `0.0 - 1.0`, otherwise the field won't be presented

`type` - optional field, possible values = `"vmware" | "parallels" | "virtualBox" | ... `

### `requestId`

Request identifier, e.x. `01F9XY24VDZ9F4HHR4FSCRYTSH`

### `ip`

Client ip address, e.x. `82.200.40.10`

### `tag`

String which associated with the request. It's set up by Botd user.

***
### `mode` is `requestId`:

```json
{
    "requestId": "<requestId>"
}
```

You can use method `/results` then to get full detection report by `requestId`.

## GET `/results`

### Query parameters

`id` - request id. 

Or you can pass request id through `botd-request-id` variable in cookie.

### Response format

The response will be the same as `/detect` has for `mode = "allData"`.

## Error format

```js
{
  "code": "TokenNotFound",
  "message": "token not found"
}
```

`code` - error code, possible values = `"BotdApiFailed", "TokenRequired", "TokenNotFound", "RequestCannotBeParsed"`.

`message` - error description.

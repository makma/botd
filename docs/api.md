# Botd JavaScript API
_Botd also has a [server-side API](server_api.md). The responses in both JS and server APIs are identical._

## `Botd.load`

```js
Botd.load({ token: string,
  mode?: "requestId" | "allData" }): Promise<BotDetector>
```

Builds an instance of `BotDetector`. We recommend calling it as early as possible,
ideally during application startup. It returns a promise which you can chain on to call `BotDetector` methods later.

`token` is your free account token required to access the server-side bot detection API.
This is a required parameter.

`mode`: Two modes are supported: `requestId` (default) and `allData`.

When `requestId` mode is used, only `requestId` field is returned back to the browser.
It's a safe way to detect a bot server-side without leaking results to the browser.
This mode is recommended for production usage.

When `allData` mode is used, all data from the bot detection result is returned back to the browser.
This mode is not recommended for production, but can be used during development and testing.

## `BotDetector.detect`

```js
botDetector.detect({ tag?: string }): Promise<Record<string, unknown>>
```

Performs bot detection. Internally it will make a network request to our server-side bot detection API
and return back the `requestId` which you can use later to retrieve bot detection results (or `allData` if you configured it this way).

Note that the `requestId` will implicitly be stored in a cookie for future convenience.

**`tag`** is an optional metadata string that you can associate with each bot detection event.

The response object format is described [here](server_api.md#response-body).


## `BotDetector.getResult`

```js
botDetector.getResult(): Promise<Record<string, unknown>>
```
Will retrieve an existing bot detection result by `requestId`, previously stored in cookies as an implicit parameter.
Internally works by calling the  `/results` endpoint in the [server API](server_api.md#get-results).
For server-side retrieval, use our server API instead.

## Error handling:

You should always call both `load` and `detect` with a `.catch` function where you should handle possible errors.

```js
//example
botdPromise
  .then(botd => botd.detect())
  .then(result => {
    console.log(result);
  })
  .catch(err => console.error(err))
```

### Error format

You can get this information in [Server botd API](server_api.md#error-format).

Additional possible error codes in Botd: `BotdFailed`, `DetectNotCalled`

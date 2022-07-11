# HTTP Preload Manifest v1

## Sample Manifest

```json
{
  "$schema": "https://raw.githubusercontent.com/http-preload/manifest/master/preload-v1.schema.json",
  "manifestVersion": 1,
  "conditions": {
    "supportsModulepreload": "(userAgentData, headers) => userAgentData.brands.some((e)=>e.brand==='Chromium'&&parseInt(e.version)>=66)"
  },
  "resources": {
    "/index.html": [
      {"rel": "preload", "href": "/assets/index.css", "as": "style", "crossorigin": "anonymous"},
      {"rel": "dns-prefetch", "href": "https://example.com"},
    ],
    "/index.html supportsModulepreload": [
      {"rel": "modulepreload", "href": "/src/foobar.js", "fetchpriority": "high"},
      {"rel": "modulepreload", "href": "/lib/foo.js"},
      {"rel": "modulepreload", "href": "/lib/bar.js"},
      {"rel": "modulepreload", "href": "/src/qux.js"}
    ]
  }
}
```

## Explanation

### $schema

Value of "$schema" can be a relative path targeting offline copy of "preload-v1.schema.json", or an URL of public online "preload-v1.schema.json". This field is neccesary for IDEs to enabling content assistant of Preload Manifest.

### manifestVersion

The value of field `manifestVersion` may only be "1" if "preload-v1.schema.json" is used as json schema for the json manifest.

### conditions

This field is optional, the value of `conditions` must be an object if present.

Each key of the object must be an ECMAScript identifier (ASCII subset), it may be isXxx, supportsFeatureA or supportFeatureA_supportsFeatureB 

Each value of the object may be a source string of ECMAScript function expression (or arrow function)

The condition expressions will be evaluated and invoked at server runtime. it takes 2 parameters `(userAgentData, headers)` and should return boolean value.

Parameter `userAgentData` is retieved from Client Hints headers `Sec-CH-UA` `Sec-CH-UA-Mobile` `Sec-CH-UA-Platform`, or picked from header `User-Agent` if no Client Hints headers present. See sample userAgentData [./samples/userAgentData.txt](samples/userAgentData.txt)

Parameter `headers` is an object of all request headers, psuedo headers like `:method`,  `:path`, `:authority`, ... are also included in `headers` if http version is 2.0 or newer. See sample headers [./samples/headers.txt](./samples/headers.txt)

### resources

The value of `conditions` must be an object.

Each key of the object starts with a request URI path, optionally followed by one or more spaces then followed by a condition name which is defined in the `conditions` object.

Each value of the object should be an array of objects which are the JSON representation for HTML `<link>` elements with the following `rel`s:

+ preload
+ modulepreload
+ dns-prefetch
+ preconnect
+ prefetch
+ prerender


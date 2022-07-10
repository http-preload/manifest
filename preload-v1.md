# HTTP Preload Manifest v1

## Sample

```json
{
  "$schema": "../preload-v1.schema.json",
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

If "preload-v1.schema.json" is used as json schema for the json manifest, then the manifestVersion may only be "1".

### conditions

Each key of the object must be an ECMAScript identifier (ASCII subset), it may be isXxx, supportsFeatureA or supportFeatureA_supportsFeatureB 

Each value of the object may be a source string of ECMAScript function expression (or arrow function)

The condition expressions will be evaluated and invoked at server runtime. it takes 2 parameter `(userAgentData, headers)` and should return boolean value.

Parameter `userAgentData` is retieved from Client Hints headers `Sec-CH-UA` `Sec-CH-UA-Mobile` `Sec-CH-UA-Platform`, or picked from header `User-Agent` if no Client Hints headers present. Sample userAgentData [samples/userAgentData.txt](samples/userAgentData.txt)

Note: The `userAgentData` has same data structure as BOM `navigator.userAgentData` in secure contexts but may not have the same brand enum values for Firefox and Safari, since they doesn't support `navigator.userAgentData`.

Parameter `headers` is an object of all request headers, psuedo headers are also included in `headers` if http version is 2.0 or newer. Sample headers [./samples/headers.txt](./samples/headers.txt)

### resources

Each key of the object should starts with a request URI path, optional followed by one or more spaces and a condition name which is defined in "condition" object.

Note: Use of a not-defined condition identifier results in ReferenceError.

Each value of the object should be an array of objects which are the JSON representation for HTML `<link>` elements with the following "rel"s:

+ preload
+ modulepreload
+ dns-prefetch
+ preconnect
+ prefetch
+ prerender


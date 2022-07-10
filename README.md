# HTTP Preload

*This is currently an editor's draft .*



**Table of contents**

<!--ts-->
   * [The basic idea](#the-basic-idea)
   * [Background](#background)
   * [The preload manifest](#the-preload-manifest)
   * [Preload manifest processing](#preload-manifest-processing)
   * [Community support](#community-support)
      * [Preload manifest tools](#preload-manifest-tools)
      * [Server-side implementations](#server-side-implementations)
      <!--te-->

<!-- ubuntu run gh-md-toc --insert --no-backup --hide-footer --skip-header README.md -->

## The basic idea

Preloading resources by seding `Link` HTTP headers with Preolad `rel`s (`rel="preload"`, `rel="modulepreload"`) or [Resource Hints](https://www.w3.org/TR/resource-hints/) `rel`s (`rel="dns-prefetch"`, `rel="preconnect"`, etc.).

```http
HTTP/1.1 200 OK
Content-Type: text/html; charset=UTF-8
Content-Length: 3453
Link: </critical.js>;rel="preload";as="script"
```

The preload `Link` headers can be sent with status code [103 Early Hints](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/103) if user-agent supports and web-developer prefers.

```http
HTTP/1.1 103 Early Hints
Link: </critical.js>;rel="preload";as="script"
```

The preload `Link` headers can be sent fixedly, or be sent conditionally by checking [Client Hints](https://developer.mozilla.org/en-US/docs/Web/HTTP/Client_hints) and request headers with JavaScript function which runs in a server-side JavaScript engine (e.g. Node.js, GraalVM, njs).

```js
function supportsModulepreload(userAgentData, headers) {
  return userAgentData.brands.some((e)=>e.brand==='Chromium'&&parseInt(e.version)>=66);
}
```



## Background

You can preload resources by adding a `<link>` tag with `rel="preload"` to the head of your HTML document.

```html
<link rel="preload" as="script" href="critical.js" />
```

It works fine, but what if we preload it conditionally? and how do we preload it ealier?



## The preload manifest

*TODO explain the structure of preload manifest file and how each part is used.*

See [./preload-v1.md](./preload-v1.md) 



## Preload manifest processing

*TODO describe the brief steps of reading and using preload manifest*

 See source code of reference implementation [preload-middleware](../preload-middleware) for guide.



## Cache control

You might need to set `Cache-Control` max-age to at least 3 seconds for resources to be preloaded, or don't set `Cache-Control`, in corder to avoid duplicated requests.

 

## Community support

### Preload manifest tooling

*TODO add tooling support*



### Server-side implementations

+ [preload-middleware](../preload-middleware) for Node.js server frameworks like Express, Koa
+ [preload-servlet-filter](../preload-servlet-filter) or [preload-servlet4-filter](../preload-servlet4-filter) for Servlet containers like Tomcat
+ [preload-njs-filter](../preload-njs-filter) for Nginx njs



**Comparison of HTTP Preload server-side implementations**

| Feature \\ Implementation | preload-middleware | preload-servlet-filter | preload-njs-filter |
| ------------------------- | ------------------ | ---------------------- | ------------------ |
| Basic manifest support    | Y                  | Y                      | Y                  |
| Condition expressions     | Y                  | Y [^1]                 | Y [^2]             |
| Status 103 early hints    | Y (http/1.1, h2)   | Y (http/1.1, h2)       | N [^3]             |
| File watch & hot-reload   | Y                  | Y                      | N [^4]             |



**Footnote:**

[^1]: Requires a JavaScript Engine like GraalVM
[^2]: Requires custom build of njs
[^3]: Due to njs lacks of public API to send 1xx status
[^4]: Due to njs lacks of built-in file watch API


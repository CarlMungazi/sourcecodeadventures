---
title: "Peeking under the hood: express.js"
layout: post
categories: posts
published: true
---

*Peeking under the hood is a series in which I look at the dependencies of popular tools and list what they do as well as anything else I find interesting. It is not intended to be a deep dive or explanation about how everything works. Inspiration for this series came from [this](https://medium.com/friendship-dot-js/i-peeked-into-my-node-modules-directory-and-you-wont-believe-what-happened-next-b89f63d21558) blog. Most of the module descriptions are taken from the `README` file.*

---

## Accepts
This module has actually been extracted from [Koa](https://www.npmjs.com/package/koa). It allows for higher level contention negotiation based on [negotiator](#negotiator)

## Array-flatten 
This module gives you three functions that help you flatten arrays in different ways. A good write-up on array flattening can be found [here](https://www.codetuts.tech/flatten-deep-nested-array-object/)

## Content-disposition 
A module for creating and parsing HTTP [Content-Disposition](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Disposition) headers. According to MDN, this header indicates *...if the content is expected to be displayed inline in the browser, that is, as a Web page or as part of a Web page, or as an attachment, that is downloaded and saved locally*

## Content-type
A module for creating and parsing HTTP [Content-Type](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Type) headers. As a **response**, this header tells the client what media type (text/css, text/html, image etc) is being returned. In **requests**, the client tells the server what media type is being sent

## Cookie 
This module is a basic HTTP cookie parser and serializer for HTTP servers. Read more about cookies [here](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies)

## Cookie-signature
This module is used to sign and unsign cookies

## Debug
A debugging utility modelled after node core's debugging technique. This a good [article](https://blog.risingstack.com/how-to-debug-nodej-js-with-the-best-tools-available/) on debugging in node.js

## Depd
This library allows module authors to display deprecation messages to developers using their modules

## Encode-url
Another of those small modules. This one deals with [percent encoding](https://developer.mozilla.org/en-US/docs/Glossary/percent-encoding), which MDN describes as *'a mechanism to encode 8-bit characters that have specific meaning in the context of URLs'*. It consists of the following variables and function (comments have been taken out):
```javascript
var ENCODE_CHARS_REGEXP = /(?:[^\x21\x25\x26-\x3B\x3D\x3F-\x5B\x5D\x5F\x61-\x7A\x7E]|%(?:[^0-9A-Fa-f]|[0-9A-Fa-f][^0-9A-Fa-f]))+/g
var UNMATCHED_SURROGATE_PAIR_REGEXP = /(^|[^\uD800-\uDBFF])[\uDC00-\uDFFF]|[\uD800-\uDBFF]([^\uDC00-\uDFFF]|$)/g
var UNMATCHED_SURROGATE_PAIR_REPLACE = '$1\uFFFD$2'

function encodeUrl (url) {
  return String(url)
    .replace(UNMATCHED_SURROGATE_PAIR_REGEXP, UNMATCHED_SURROGATE_PAIR_REPLACE)
    .replace(ENCODE_CHARS_REGEXP, encodeURI)
}
```
## Escape-html
A 78-line module for escaping a string of content so it can be interpolated in HTML content

## Etag
This module creates [HTML ETags](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/ETag). An ETag is a HTTP response header which is an identifier for a specific version of a resource. They help caches be more efficient and help to save bandwith because web servers do not need to send a full response if the content has not changed

## Finalhandler
This module has about seven of its own dependencies. It exports a node.js function which is invoked as the final step in responding to a HTTP request. **Fun fact:** It includes a function which creates a HTML document. See below:
```javascript
function createHtmlDocument (message) {
  var body = escapeHtml(message)
    .replace(NEWLINE_REGEXP, '<br>')
    .replace(DOUBLE_SPACE_REGEXP, ' &nbsp;')

  return '<!DOCTYPE html>\n' +
    '<html lang="en">\n' +
    '<head>\n' +
    '<meta charset="utf-8">\n' +
    '<title>Error</title>\n' +
    '</head>\n' +
    '<body>\n' +
    '<pre>' + body + '</pre>\n' +
    '</body>\n' +
    '</html>\n'
}
```

## Fresh
A module for testing the "freshness" of HTTP responses. Read [this](https://developer.mozilla.org/en-US/docs/Web/HTTP/Caching#Freshness) for more information on the subject 

## Merge-descriptors
This module merges two objects using the property descriptors from the source object. See below for the exported function and an explanation of what it going on:
```javascript
var hasOwnProperty = Object.prototype.hasOwnProperty // private variable 

function merge(dest, src, redefine) { // exported function
  if (!dest) {
    throw new TypeError('argument dest is required')
  }

  if (!src) {
    throw new TypeError('argument src is required')
  }

  if (redefine === undefined) {
    // Default to true
    redefine = true
  }

  Object.getOwnPropertyNames(src).forEach(function forEachOwnPropertyName(name) {
    if (!redefine && hasOwnProperty.call(dest, name)) {
      // Skip descriptor
      return
    }

    // Copy descriptor
    var descriptor = Object.getOwnPropertyDescriptor(src, name)
    Object.defineProperty(dest, name, descriptor)
  })

  return dest
}
```
1. If the `destination` or `source` objects are falsy values, a `TypeError` is thrown. A `TypeError` is an object which represents an error when a given value is not of the expected type.
2. Give `redefine` a default boolean value of `true` if it has not been already set. Setting this value to `false` means if the `destination` and `source` objects both have a property with the same name, we do not want to override the property on `destination` with the value in the property on `source`
3. For every enumerable and non-enumerable `property` in the `source` object:
  - If `redefine` is **true** AND the `destination` object **DOES** have the given property, **CONTINUE** on to the next step
  - If `redefine` is **false** OR the `destination` object does **NOT** have the given property, **STOP** execution and move on to the next property
  - Store the `property`'s property descriptor in a variable
  - Define the `property` on the `destination` object along with the stored property descriptor
4. Return the `destination` object with its new properties

## Methods
This module provides an export that is just like [`http.METHODS`](https://nodejs.org/api/http.html#http_http_methods) from node.js core with the following differences:
  * All method names are lower-cased
  * Contains a fallback list of methods for Node.js versions that do not have a
    `http.METHODS` export (0.10 and lower)
  * Provides the fallback list when using tools like `browserify` without pulling
    in the `http` shim module

## On-finished
This module provides a way to execute a callback when a HTTP request closes, finishes, or errors. The [`ee-first`](https://www.npmjs.com/package/ee-first) module is its only dependency. `ee-first` gets the first event in a set of event emitters and event pairs, then cleans up after itself.

## Parse-url
A module for parsing a URL with memoization. It exports two functions: `parseurl(req)` and `parseurl.original(req)`. The former parses the URL of the given request object (by looking at the `req.url` property) and returns the result, whilst the latter parses the original URL of the given request object and returns the result.

## Path-to-regexp
This module turns an Express-style path string such as `/user/:name` into a regular expression. You can view a demo of this module [here](http://forbeslindesay.github.io/express-route-tester/)

## Proxy-addr
A module for determining the address of a proxied request

## Qs
One of the larger modules. It provides functionality for [querystrings](https://en.wikipedia.org/wiki/Query_string) parsing and stringifying

## Ranger-parser
A module for parsing the headers in HTTP responses. This [blog](http://bizcoder.com/everything-you-need-to-know-about-http-header-syntax-but-were-afraid-to-ask) has more information on the subject

## Send
This module is a library for streaming files from the file system as a http response

## Serve-static
This module helps you serve up entire folders mapped to URLs. It works with the `send` module above

## Setprototypeof
This module provides a cross platform way of setting the prototype of an instantiated object. It supports all modern browsers and goes back as far as IE8. The code which does all this is below:
```javascript
Object.setPrototypeOf || ({__proto__:[]} instanceof Array ? setProtoOf : mixinProperties);

function setProtoOf(obj, proto) {
	obj.__proto__ = proto;
	return obj;
}

function mixinProperties(obj, proto) {
	for (var prop in proto) {
		if (!obj.hasOwnProperty(prop)) {
			obj[prop] = proto[prop];
		}
	}
	return obj;
}
```

## Statuses
A utility module for dealing with HTTP statuses in node. The status codes are in a `json` file which is imported and then used in various ways

## Type-is
This module infers the content-type of a request. A HTML Content-Type entity header is used to indicate the media type of the resource

## Unpipe
This module unpipes a stream from all destinations. This module is a dependency of the [accepts](#accepts) module

## Utils-merge
This module is a function which merges two objects (`obj a` and `obj b`, for example) and then returns the object given as the first argument (`obj a`) with properties from the second object (`obj b`) added onto it. The function declaration is below. Interesting blogs on object merging can be found [here](https://davidwalsh.name/merge-objects) and [here](http://2ality.com/2014/12/es6-oop.html)
```javascript
exports = module.exports = function(a, b){
  if (a && b) {
    for (var key in b) {
      a[key] = b[key];
    }
  }
  return a;
};
```
1. Check if `a` and `b` are falsy values. If they are, return `a` as it is but if they are not, continue to step 2
2. For every `property` in `b`
  - Add that `property` to `a`
3. Return `a`

## Vary
A module for manipulating the HTTP Vary header. This header is used in content caching and serving different content to users depending on their devices. Visit this [blog](http://www.bizcoder.com/the-insanity-of-the-vary-header) for more on the topic

---

### Reflections:
1. It was interesting to see two modules which both merge objects but do so in very different ways. `merge-desc` only adds properties directly found directly on the source object and it also merges non-enumerable properties whilst `utils-merge` only iterates over an object's enumerable properties as well as those found in its prototype chain. The differences in the methods used below are listed below
  - `Object.getOwnPropertyDescriptor()` is used by `merge-desc`. It returns a property descriptor for an own property (that is, a property directly present on an object and not in the object's prototype chain) 
  - `Object.getOwnPropertyNames()` is used by `merge-desc`. It returns an array of all properties (including non-enumerable properties) found directly upon a given object. It does not return properties using the [Symbol](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol) data type
  - `for..in` is used by `utils-merge`. It iterates over an object's enumerable properties as well as those in its prototype chain
2. Reading through the module `README` files was a lesson in writing good documentation and APIs
3. I know now what the `TypeError` object does and how useful it is for creating more specific error messages
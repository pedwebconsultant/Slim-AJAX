![Slim-AJAX Logo](https://www.dropbox.com/s/12iwqa7wj3d1247/Slim-AJAX.png?raw=1)

An Advanced light-weight (almost 6k) AJAX API version for all modern browsers. 

# Philosophy
Although jQuery AJAX is one the most complete AJAX APIs fow Web development, it is not independent from the rest of the jQuery library. Moreover, by the introduction of advanced Web development frameworks like React, Angular, and Vue, the existence of an independent advanced light-weight AJAX API is necessary more than ever. Slim-AJAX is meant to fulfill this requirement for developers who think beyond the Internet Explorer and old browsers era. 

Slim-AJAX incorporates some well-known and frequently used design patterns including *singletone, chaining, lazy-instantiation,* and *factory pattern* to provide advanced, user-friendly, and professional API.

## Client-side usage 
To use Slim-AJAX API on the client side, simply add the following script tag preferably as the first script in your html file. 
```javascript
<script src="slim-ajax.min.js"></script> 
```
This will add the API to the variable `slimAjax` on the `window` object. 

# Slim-AJAX API
```javascript
import slimAjax from 'slim-ajax';

slimAjax.request("GET", "/", true, [username, password]).send(data);   // [] here means optional
slimAjax.request("POST", "/", true, [username, password]).send(data);
slimAjax.get("/", true, [username, password]).send(data);      
slimAjax.post("/", true, [username, password]).send(data);
```
Each of the *request, get,* and *post* methods as the top-level methods of Slim-AJAX API supports chaining true the following methods:

### **- setHeaders({headerName: value})**
  
This method gets an object containing the http headers and their corresponding values as a set of key-value pairs. It clearly sets those headers in the AJAX request to their corresponding values.  
#### Example:
```javascript
slimAjax.post(...).setHeaders({'Content-Type', 'text/html'});
```

### **- setPropsHandlers({property/event: value})**

This method gets an object containing any of the properties and handlers supported by Slim-Ajax API as a set of key-value pairs. Similar to the previous method, it sets those properties and handlers to their corresponding values.
    
Slim-AJAX supports the following properties and handlers:

|property             | Description                                                                    |
|:--------------------|:-------------------------------------------------------------------------------|
|contentType          | sets the value of 'Content-Type' http header                                   |
|responseType         | sets the value of the 'responseType' property of the XMLHttpRequest object     |
|overrideMimeTypeWith | its value will be used to override the MimeType of the response                |
|timeout              | sets the value of the 'timeout' property of the XMLHttpRequest object          |
|noCache              | `noCache: true` prevents browsers from caching the response                    |
|cors                 | `cors: true` makes the Cross-Origin Resource Sharing possible                  |

**Note:** By XMLHttpRequest specification, `responseType` can be only set for asynchronous requests. So in the case that it is explicitly set on a synchronous request, Slim-AJAX will ignore the setting while printing a `console.error` message.

|Handler      | Description                                             |
|:------------|:--------------------------------------------------------|
|onSuccess    | equivalent to (readyState === 4 && status === 200)      |
|onFailure    | complement of "onSuccess"                               |
|onLoadStart  | "onloadstart" event handler of the XMLHttpRequest object|
|onProgress   | "onprogress" event handler of the XMLHttpRequest object |
|onAbort      | "onabort" event handler of the XMLHttpRequest object    |
|onError      | "onerror" event handler of the XMLHttpRequest object    |
|onLoad       | "onload" event handler of the XMLHttpRequest object     |
|onTimeout    | "ontimeout" event handler of the XMLHttpRequest object  |
|onLoadEnd    | "onloadend" event handler of the XMLHttpRequest object  |

**Note:** The http `'Content-Type'` header can be also set via `setHeaders('Content-Type', ...)`. However, the "contentType" property is just a convenience since this http header is commonly used in almost all AJAX requests.

#### Example:
```javascript
slimAjax.post(...).setPropsHandlers({
  contentType: 'text/html',
  timeout: 100,
  onSuccess: (resObject, globals) => {
    // ...
  },
  onFailure: (resObject) => {
    // ...
  },
  onLoad: (event, resObject, globals) => {
    // ...
  }
});
```

**Note:** Read more about arguments passed to `onSuccess, onFailure`, and `onLoad` handlers on [globals](#slimajaxglobals) and on [resObject](#resobject-argument-passed-to-the-main-handlers) in this documentation. 
    
### **- setUploadHandlers({event: handler})**

This method sets the event handler on the `XMLHttpRequest.upload` object. The handlers are the same as those in the previous method **EXCEPT** `onSuccess` and `onFailure` which are only available for `XMLHttpRequest` object.
  
#### Example:
```javascript
slimAjax.post(...).setUploadHandlers({
  onAbort: (event) => {
    // ...
  },
  onLoad: (event, resObject, globals) => {
    // ...
  }
});
  ```
### **- send(data)**

This method is equivalent to `send()` method on `XMLHttpRequest`. However, it has some improved functionality depending on the http method in the request. For `GET` method, it essentially sends the encoded url regardless of the `data` passed to it while, for the `POST` method, it actually sends the passed data through the request. This method returns the `XMLHttpRequest` object created under the hood.

**Note:** All of the above methods except the `send()` method support chaining. So, after calling send() chaining other methods is not possible. This implies that `send()` should be always used as the last method in the chain.

# Global and static properties of the Slim-AJAX API 
## slimAjax.globals
Slim-AJAX API provides developers with a `global` object which will be always passed to `onSuccess`, and `onLoad` event handlers regardless of the top-level method i.e. *request, get,* and *post*. This global value will be passed to `onSuccess`, and `onLoad` event handlers as the last argument which we call it `globals` in examples for the sake of clarity.

### Examples
1.
    ```javascript
    slimAjax.globals = {
      mytext: 'Slim-AJAX API',
      myFunc: function(){
        //...
      }
    };

    slimAjax.request(...).send(); 
    slimAjax.get(...).send(); 
    slimAjax.post(...).send(); 
    ```   

2.
    ```javascript
    slimAjax.globals = "This text will be automatically passed to the main methods discussed above";

    slimAjax.request(...).send(); 
    slimAjax.get(...).send(); 
    slimAjax.post(...).send(); 
    ```
## Static properties
Every top-level method of Slim-AJAX API (i.e. `request(), get(),` and `post()`) supports a static property called `defaults`. This is a JavaScript object containing key-value pairs of *"properties and handlers"*. The main point of such a static value is to share the most general settings on that particular top-level method for further use. So, after `slimAjax.post.defaults = ...` is done, any further use of `post()` top-level method will automatically set all the corresponding properties and/or handlers in the `default` object for that `post()` request.

**Notes:** 
1. Although `slimAjax.post.defaults = ...` will set the default properties and handlers for any future `post()` call, any of those already set properties and handlers can be overwritten by using the API methods explained before.

2. The `defaults` object is only meant for setting properties and handlers for the `XMLHttpRequest` object. Event handlers for `XMLHttpRequest.upload` has to be always set separately via `setUploadHandlers()` method.

### Examples

1.
    ```javascript
    slimAjax.get.defaults = {
      contentType: 'text/html',
      onSuccess: function(resObject, globals){
          //...
      }
    }
      
    slimAjax.get(...).send();   
    ```   

2.
    ```javascript
    slimAjax.post.defaults = {
      contentType: 'text/html',
      onSuccess: function(resObject, globals){
          //...
      }
    }
    
    slimAjax.post(...).setPropsHandlers({ 		// overwriting the "contentType" which is set by defaults
      contentType: 'application/json'
    }).send(data);   
    ```
# resObject argument passed to the main handlers
Another object that is passed to `onSuccess, onFailure,` and `onLoad` handlers is the `resObject`. This is the object that contains main data of the response as well as two important methods of `XMLHttpRequest` object which are mainly used after the response is ready. However, the `resObject` contains different data regarding the the handler to which it is passed as well as the value of the `responseType` property of the request. 

## resObject passed to `onSuccess` and `onLoad` handlers
Depending on the type of the `responseType` set for the AJAX request, the `resObject` contains different members:

|responseType       | members of `resObject`                                          |
|:------------------|:----------------------------------------------------------------|
|"" or "text"       | response, responseText, getResponseHeader, getAllResponseHeaders|
|"document"         | response, responseXML, getResponseHeader, getAllResponseHeaders |
 
## resObject passed to `onFailure` handler

The members of  passed to this event handler are:

|members of `resObject`                               | Circumstance                                     |
|:----------------------------------------------------|:-------------------------------------------------| 
|status, getResponseHeader, getAllResponseHeaders     | for failure caused by the status other than 200  |
|readyState, getResponseHeader, getAllResponseHeaders | for failure caused by the readyState other than 4|

<br />

**Note:** Neither `globals` nor `resObject` will be passed to other event handlers.

## Defining event handlers
### Examples
1. 
    ```javascript
    slimAjax.get.defaults = {                 // Note the differences between these
                                              // handlers in terms of passed arguments
      contentType: 'text/html',
      onSuccess: function(resObject, globals){
          //...
      },
      onFailure: function(resObject){
          //...
      },
      onLoad: function(event, resObject, globals){
          //...
      }
    }    
    slimAjax.get(...).send();   
    ```
2.     
    ```javascript
    slimAjax.post(...).setPropsHnadlers({
      contentType: 'json',
      onSuccess: function(resObject, globals){
          //...
      },
      onFailure: function(resObject){
          //...
      },
      onLoad: function(event, resObject, globals){
          //...
      }
    }).send(data);   
    ```
3.      
    ```javascript
    slimAjax.request(...).setPropsHnadlers({
      contentType: 'form',
      onAbort: function(event){
        //...
      },
      onError: function(event){
        //...
      }
    }).send();
    ```

**PROTIPS:**
 
1. Among all existing events in Slim-AJAX API, the `onSuccess` is the only one that can accept an array of functions. In that case, all those functions will be called in the order. Note that every function receives `reObject` and `globals` as if it was the only assigned handler to that event.

2. The `onFailure` event can be viewed as a diagnostic tool in the case that the response is not successful. If it is assigned a handler, then it will be launched for every `readyState` change less than 4 as well as `status` else than 200. This implies that care must be taken into account when this event is meant to apply some actions else than diagnosing the reason of the response failure. 

### Example
```javascript
slimAjax.request(...).setPropsHnadlers({
    contentType: 'form',
    onSuccess: [
      function(resObject, globals){
        //...
      },
      function(resObject, globals){
        //...
      },
      function(resObject, globals){
        //...
      }
    ]
}).send();   
```

# Differences between top-level methods
## slimAjax.request():
The general AJAX method that requires an http method as well as a url. The `defaults` object of this method is set to:
```javascript
{
  contentType: "",
  responseType: "",                // Just for asynchronous requests
  overrideMimeTypeWith: null,
  timeout: 0,
  noCache: true,
  cors: false
}
```

## slimAjax.get():
The convenience method fo http `GET` requests that requires a url. The `defaults` object of this method is set to:
```javascript
{
  contentType: "text/html",
  responseType: "text",            // Just for asynchronous requests
  timeout: 0,
  noCache: true,
  cors: true
}
```
## slimAjax.post():
The convenience method fo http `POST` requests that requires a url. The `defaults` object of this method is set to:
```javascript
{
  contentType: "application/json",
  responseType: "json",            // Just for asynchronous requests
  timeout: 0,
  noCache: true,
  cors: true
}
```

# Username & password in top-level methods

If for the AJAX request a basic authentication is required, slim-AJAX API will handle that automatically. For having that done, it is only necessary that *NEITHER* username *NOR* password be **empty** strings. Under this condition, Slim-AJAX API will automatically set the `Authorization` http header to the required value made out of the username and password.

# Convenience shorthand strings for the content-types

Since setting `Content-Type` http header is such a common task in almost every AJAX request, there are a list of shorthand strings for the most frequently used http content types. **_However_**, these shorthand strings can only be used for setting `contentType` property via `setPropsHandlers()` method or inside the `defaults` object. The following table lists the available shorthand strings.

Shorthand | Corresponding formal http MimeType
----------|------------------------------
text      | text/plain
html      | text/html
css       | text/css
js        | application/javascript
json      | application/json
form      | application/x-www-form-urlencoded
xml       | text/xml
pdf       | application/pdf
jpeg      | image/jpeg
png       | image/png
gif       | image/gif
svg       | image/svg+xml
wma       | audio/x-ms-wma
mp4       | video/mp4
ai        | application/postscript
zip       | application/zip

### Examples

1. 
    ```javascript
    slimAjax.get.defaults = {
        contentType: "json",
        // ...
    }

    slimAjax.get(...).send();
    ```
2. 
    ```javascript
    slimAjax.post(...).setPropsHandlers({
        contentType: "form",
        // ...
    }).send(data);
    ```
**Note:** For setting http content-type to MimeType other than those in the above table, either the value of `contentType` property or `Content-Type` header must be explicitly set to that MimeType.

## People
The author of Slim-AJAX is [Pedram Emami](https://www.linkedin.com/in/pedram-emami/).

## License
[MIT](LICENSE)

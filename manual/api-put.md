[POST](api-post.md) | [TOC](README.md) | [Error Handling](error-handling.md)
- - -

# PUT

PUT inserts new elements and attributes into the storage.  
The position for the insert is defined via an XPath.  
XPath uses different notations for elements and attributes.

The notation for attributes use the following structure at the end.  
    `<XPath>/@<attribute>` or `<XPath>/attribute::<attribute>`
The attribute values can be static (text) and dynamic (XPath function).  
Values are send as request-body.
Whether they are used as text or XPath function is decided by the
Content-Type header of the request:
- `text/plain`: static text
- `text/xpath`: XPath function

If the XPath notation does not match the attributes, elements are assumed.  
For elements, the notation for pseudo elements is supported:
    `<XPath>::first, <XPath>::last, <XPath>::before or <XPath>::after
Pseudo elements are a relative position specification to the selected element.

The value of elements can be static (text), dynamic (XPath function) or be an
XML structure. Again, the value is transmitted with the request-body and the
type of processing is determined by the Content-Type:  
- `text/plain`: static text
- `text/xpath`: XPath function
- `application/xslt+xml`: XML structure

The PUT method works resolutely and inserts or overwrites existing data.
The XPath processing is strict and does not accept unnecessary spaces.
The attributes `___rev` / `___uid` used internally by the storage are
read-only and cannot be changed.

In general, if no target can be reached via XPath, status 404 will occur. In all
other cases the PUT method informs the client about changes with status 204 and
the response headers `Storage-Effects` and `Storage-Revision`. The header
`Storage-Effects` contains a list of the UIDs that were directly affected by
the change and also contains the UIDs of newly created elements. If no changes
were made because the XPath cannot find a writable target, the header
`Storage-Effects` can be omitted completely in the response. 

Syntactic and symantic errors in the request and/or XPath and/or value can cause
error status 400 and 415. If errors occur due to the transmitted request body,
this causes status 422.


## Contents Overview

* [Request](#request)
  * [Example](#example)
* [Response](#response)
  * [Example](#example-1)
* [Response codes / behavior](#response-codes--behavior)  
  * [HTTP/1.0 204 No Content](#http10-204-no-content)
  * [HTTP/1.0 400 Bad Request](#http10-400-bad-request)
  * [HTTP/1.0 404 Resource Not Found](#http10-404-resource-not-found)
  * [HTTP/1.0 413 Payload Too Large](#http10-413-payload-too-large)  
  * [HTTP/1.0 415 Unsupported Media Type](#http10-415-unsupported-media-type)
  * [HTTP/1.0 422 Unprocessable Entity](#http10-422-unprocessable-entity)
  

#### Request

```
PUT /<xpath> HTTP/1.0
Storage: 0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ (identifier)
Content-Length: (bytes)
Content-Type: application/xslt+xml
     Request-Body:
XML structure
```
```
PUT /<xpath> HTTP/1.0
Storage: 0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ (identifier)
Content-Length: (bytes)
Content-Type: text/plain
    Request-Body:
Value as plain text
```
```
PUT /<xpath> HTTP/1.0
Storage: 0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ (identifier)
Content-Length: (bytes)
Content-Type: text/xpath
    Request-Body:
Value as XPath function 
```

##### Example

```
PUT /xmex/books/attribute::attrA HTTP/1.0
Storage: 0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ
Content-Type: text/plain
Content-Length: 5

Value
```
```
PUT /xmex/books/@attrA HTTP/1.0
Storage: 0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ
Content-Type: text/xpath
Content-Length: 25

concat(name(/*), "-Test")
```
```
PUT /xmex/books HTTP/1.0
Storage: 0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ
Content-Type: application/xslt+xml
Content-Length: 70

<book title="Book A"/>
<book title="Book B"/>
<book title="Book C"/>
```


## Response

```
HTTP/1.0 204 No Content
Storage-Effects: ... (list of UIDs)
Storage: 0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ
Storage-Revision: Revision (number)   
Storage-Space: Total/Used (bytes)
Storage-Last-Modified: Timestamp (RFC822)
Storage-Expiration: Timeout/Timestamp (seconds/RFC822)
```

### Example

```
HTTP/1.0 204 No Content
Date: Wed, 11 Nov 2020 12:00:00 GMT
Storage-Effects: KHDHLTQW18U4:0 KHDHLQJU18U2:0 KHDHLTQW18U4:1 KHDHLTQW18U4:2
Access-Control-Allow-Origin: *
Storage: 0000000000000000000000000000000000PE
Storage-Revision: 1
Storage-Space: 262144/305
Storage-Last-Modified: Wed, 11 Nov 12:00:00 +0000
Storage-Expiration: 900/Wed, 11 Nov 12:00:00 +0000
Execution-Time: 3
```


## Response codes / behavior

### HTTP/1.0 204 No Content
- Attributes successfully created or set

### HTTP/1.0 400 Bad Request
- XPath is missing or malformed
- XPath without addressing a target is responded with status 204

### HTTP/1.0 404 Resource Not Found
- Storage is invalid  
- XPath axis finds no target

### HTTP/1.0 413 Payload Too Large
- Allowed size of the request(-body) and/or storage is exceeded

### HTTP/1.0 415 Unsupported Media Type
- Attribute request without Content-Type text/plain

### HTTP/1.0 422 Unprocessable Entity
- Data in the request body cannot be processed



- - -

[POST](api-post.md) | [TOC](README.md) | [Error Handling](error-handling.md)
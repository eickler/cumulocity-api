Metadata:
HOST: https://developer.cumulocity.com
FORMAT: 1A

# Cumulocity API

## HTTP usage

### Authentication

All requests need to include the [HTTP "Authorization"
header](http://en.wikipedia.org/wiki/Basic_access_authentication). For
basic authentication with user name and password, the format is:

    Authorization: Basic «Base64 encoded credentials»

The credentials need to have the following structure:

    «realm»/«user name»:«password»

To authenticate against a tenant's realm, use the tenant ID. For
example, a user "smith" with password "smithspw1" in the tenant "demo"
is authenticated with the following string:

    demo/smith:smithspw1

For OAuth authentication, the format is:

    Authorization: Bearer «Base64 encoded access token»

### Application management

M2M market applications need to identify themselves for subscription and
billing purposes. This identification is carried out by adding the HTTP
header "X-Cumulocity-Application-Key".

    X-Cumulocity-Application-Key: «application key»

The application key is defined by the application and registered with
the Cumulocity application management. It is a random, shared, secret
key. This is a demo application key registered on the developer sandbox:

    X-Cumulocity-Application-Key: uL27no8nhvLlYmW1JIK1CA==

For private applications this header is optional.

### Limited HTTP clients

If you use an HTTP client that can only perform GET and POST methods in
HTTP, you can emulate the other methods through an additional
"X-HTTP-METHOD" header. Simply issue a POST request and add the header,
specifying the actual REST method to be executed. For example, to
emulate the "PUT" (modify) method, you can use:

    POST ...
    X-HTTP-METHOD: PUT

### Processing mode

Every update request (PUT, POST, DELETE) executes with a so-called
*processing mode*. The default processing mode is *PERSISTENT*, which
means that all updates will be send both to the Cumulocity database and
to real-time processing. The alternative processing mode
*TRANSIENT* will only send updates to real-time processing. As part of
real-time processing, the user can decide case by case through
Cumulocity Event Language scripts whether updates should be stored to
the database or not.

To explicitly control the processing mode of an update request, an
"X-Cumulocity-Processing-Mode" header can be used with a value of either
"PERSISTENT" or "TRANSIENT":

    X-Cumulocity-Processing-Mode: TRANSIENT

### Authorization

All requests issued to Cumulocity are subject to authorization. To
determine the required permissions, see the "Required role" entries in
the reference documentation for the individual requests. To learn more
about the different permissions and the concept of ownership in
Cumulocity, see "Managing permissions and ownership" in the Section
"[Securing M2M applications](index.php?option=com_k2&view=item&id=813)".

### Media types

Each type of data is associated with an own media type. The general
format of media types is

    application/vnd.com.nsn.cumulocity.«type»+json;ver=«version»;charset=UTF-8

Each media type contains a parameter "ver" indicating the version of the
type. At the time of writing, the latest version is "0.9". The complete
media type names are given in the respective sections of the reference
guide. As an example, the media type for an error message in the current
version is

    application/vnd.com.nsn.cumulocity.error+json;ver=0.9;charset=UTF-8

Media types are used in HTTP "Content-Type" and "Accept" headers. On
Requests that will produce a response message body the client is
recommend to specify a media type. For POST/PUT requests, when no
"Accept" header is specified, empty response body is returned. If a
media type without "ver" parameter is given, the oldest available
version will be returned by the server. If the accept header contains
the same media type in multiple versions the server will return a
representation in the latest supported version.

### Data format

Data exchanged with Cumulocity in HTTP requests and responses is encoded
in [JSON format](http://www.ietf.org/rfc/rfc4627.txt) and
[UTF-8](http://en.wikipedia.org/wiki/UTF-8) character encoding.
Timestamps and dates are accepted and emitted by Cumulocity in [ISO
8601](http://www.w3.org/TR/NOTE-datetime) format:

    Date: YYYY-MM-DD
    Time: hh:mm:ssZÂ±hh:mm
    Timestamp: YYYY-MM-DDThh:mm:ssZÂ±hh:mm

To avoid ambiguity, all times and timestamps must include timezone
information.

### Error reporting

In error cases, Cumulocity returns standard HTTP response codes as
described in
[RFC2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html). A
Client should not only be able to handle individual codes but classes of
codes as well (e.g., 4xx). The response body can contain more
information about the error, see the error media type definition below.
General error interpretations are:

<table>
<tr>
<th>Code</th>
<th>Name</th>
<th>Description</th>
</tr>
<tr>
<td>400</td>
<td>Bad Request</td>
<td>The request could not be understood by the server due to malformed syntax. The client SHOULD NOT repeat the request without modifications.</td>
</tr>
<tr>
<td>401</td>
<td>Unauthorized</td>
<td>Authentication has failed, or credential were required but not provided.</td>
</tr>
<tr>
<td>403</td>
<td>Forbidden</td>
<td>You are not authorized to access the API.</td>
</tr>
<tr>
<td>404</td>
<td>Not Found</td>
<td>Resource not found at given location.</td>
</tr>
<tr>
<td>405</td>
<td>Method not allowed</td>
<td>The employed HTTP method cannot be used on this resource (e.g., using "POST" on a read-only resource).</td>
</tr>
<tr>
<td>409</td>
<td>Update Conflict</td>
<td>Conflict on resource update, entity was changed in the meantime.</td>
</tr>
<tr>
<td>409</td>
<td>Duplicate</td>
<td>The entity already exists in the data source.</td>
</tr>
<tr>
<td>422</td>
<td>Invalid Data</td>
<td>General error with entity data format.</td>
</tr>
<tr>
<td>422</td>
<td>Non Unique Result</td>
<td>Resource constraints error. Non-unique result from the query.</td>
</tr>
<tr>
<td>422</td>
<td>Unprocessable entity</td>
<td>Resource cannot be processed.</td>
</tr>
<tr>
<td>500</td>
<td>Internal Server Error</td>
<td>An internal error in the software system has occurred and the request could not be processed.</td>
</tr>
<tr>
<td>503</td>
<td>Service Unavailable</td>
<td>The service is currently not available. This may be caused by an overloaded instance or it is down for maintenance. Please try it again in a few minutes.</td>
</tr>
</table>

## REST usage

### Interpretation of HTTP verbs

The semantics described in the [HTTP
specification](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html#sec9)
are used:

-   POST creates a new resource. In the response "Location" header, the
    URI of the newly created resource is returned.
-   GET retrieves a resource.
-   PUT updates an existing resource with the contents of the request.
    I.e., if parts of a resource are passed in the request, only those
    parts are updated. PUT can not update sub-resources that are
    identified by a separate URI.
-   DELETE removes a resource. The response will be "204 NO CONTENT".

### URI space and URI templates

Clients should not make assumptions on the layout of URIs used in
requests, but construct URIs from previously returned URIs or URI
templates. The root interface provides the entry point for clients, see
below.

URI templates contain placeholders in curly braces, which need to be
filled by the client to produce a URI. As an example, see the following
excerpt from the event API response:

    HTTP/1.1 200 OK
    Content-Type: application/vnd.com.nsn.cumulocity.eventApi+json;…
    Content-Length: …
     
    {
      …
      "events" : { "self" : "http://…" },
      "eventsForSourceAndType" : "http://…?type={type}&source={source}"
      …
    }

The client would need to fill the "type" and "source" placeholders with
the desired type and source devices of the events to be returned. The
meaning of these placeholders is documented in the respective interface
descriptions in the reference guide.

### Interface structure

In general, Cumulocity REST resources are modelled according to the
following pattern:

-   The starting point are API resources, which will provide access to
    the actual data through URIs and URI templates to collection
    resources. For example, the above event API resource provides the
    "events" URI and the "eventsForSourceAndType" URI to access
    collections of events.
-   Collection resources aggregate member resources and allow creating
    new member resources in the collection. For example, through the
    "events" collection resource, new events can be created.
-   Finally, individual resources can be edited.

### Query result paging

Collection resources support paging of data to avoid passing huge data
volumes in one block from client to server. GET requests to collections
accept two query parameters: "pageSize" indicates how many entries of
the collection should be returned. By default, 5 entries are returned.
The upper limit for one page is currently 2,000 documents, any larger
requested page size is trimmed to the upper limit. "currentPage" defines
the slice of data to be returned, starting with "1". By default, the
first page is returned.

For convenience, collection resources provide a "next" and "prev" links
to retrieve the next resp. the previous page of the results. This is an
example response for managed object collections:

    {
      "self" : "…",
      "managedObjects" : [
        …
      ],
      "statistics" : {
        "totalPages" : 7,
        "pageSize" : 5,
        "currentPage" : 2
      },
      "prev" : "http://…?pageSize=5&Page=1",
      "next" : "http://…?pageSize=5&Page=3"
    }

Total pages for querying by keys/range: To get totalPages calculated in
case of querying by keys/by range, an additional query param has to be
passed: "withTotalPages=true". Otherwise totalPages is set to null.

## Generic media types

### Error [application/vnd.com.nsn.cumulocity.error+json]

The error type provides further information on the reason of a failed
request.

<table border="0">
<thead>
<tr>
<td class="name">Name</td>
<td class="type">Type</td>
<td class="required">Occurs</td>
<td class="desc" width="100%">Description</td>
</tr>
</thead>
<tbody>
<tr>
<td class="name">error</td>
<td class="type">String</td>
<td class="required">1</td>
<td class="desc">Error type formatted as "«resource type»/«error name». For example, for inventory API and something like object not found error, error code would be "inventory/notFound".</td>
</tr>
<tr>
<td class="name">message</td>
<td class="type">String</td>
<td class="required">1</td>
<td class="desc">Short text description of the error</td>
</tr>
<tr>
<td class="name">info</td>
<td class="type">URL</td>
<td class="required">1</td>
<td class="desc">URL to an error description on the Internet.</td>
</tr>
<tr>
<td class="name">details</td>
<td class="type">Error details</td>
<td class="required">1</td>
<td class="desc">Error details. Only available in DEBUG mode.</td>
</tr>
</tbody>
</table>

Error details are provided in the following structure:

<table border="0">
<thead>
<tr>
<td class="name">Name</td>
<td class="type">Type</td>
<td class="required">Occurs</td>
<td class="desc" width="100%">Description</td>
</tr>
</thead>
<tbody>
<tr>
<td class="name">expectionClass</td>
<td class="type">String</td>
<td class="required">1</td>
<td class="desc">Class name of an exception that caused this error.</td>
</tr>
<tr>
<td class="name">exceptionMessage</td>
<td class="type">String</td>
<td class="required">1</td>
<td class="desc">Exception message content.</td>
</tr>
<tr>
<td class="name">expectionStackTrace</td>
<td class="type">String</td>
<td class="required">1</td>
<td class="desc">Strack trace of the exception</td>
</tr>
<tr>
<td class="name">-</td>
<td class="type">-</td>
<td class="required">-</td>
<td class="desc">Specific Error may add further information for diagnostic purpose</td>
</tr>
</tbody>
</table>

### PagingStatistics [application/vnd.com.nsn.cumulocity.pagingStatistics+json]

Paging statistics for collection resources are provided in the following
format:

<table border="0">
<thead>
<tr>
<td class="name">Name</td>
<td class="type">Type</td>
<td class="required">Occurs</td>
<td class="desc" width="100%">Description</td>
</tr>
</thead>
<tbody>
<tr>
<td class="name">totalRecords</td>
<td class="type">Integer</td>
<td class="required">1</td>
<td class="desc">The approximate total number of records.</td>
</tr>
<tr>
<td class="name">pageSize</td>
<td class="type">Integer</td>
<td class="required">1</td>
<td class="desc">Maximum number of records contained in this query.</td>
</tr>
<tr>
<td class="name">currentPage</td>
<td class="type">Integer</td>
<td class="required">1</td>
<td class="desc">The current returned page within the full result set, starting at "1".</td>
</tr>
</tbody>
</table>

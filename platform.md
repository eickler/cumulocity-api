## Root interface

To discover the URIs to the various interfaces of Cumulocity, a "root"
interface is provided. This root interface aggregates all the underlying
API resources. The root interface of the development sandbox is
accessible through http://«sandbox URL»/platform/. For more information
on the different API resources, please consult the respective API
section of this reference guide. Usage of the development sandbox is
subject to the [usage
terms](guides/reference-guide/developer-sandbox-usage-terms).

### Platform [application/vnd.com.nsn.cumulocity.platformApi+json]

Name

Type

Occurs

Description

self

URI

1

Link to this Resource

inventory

InventoryAPI

1

See [inventory](index.php?option=com_k2&view=item&id=828) interface.

identity

IdentityAPI

1

See [identity](index.php?option=com_k2&view=item&id=823) interface.

event

EventAPI

1

See [event](index.php?option=com_k2&view=item&id=827) interface.

measurement

MeasurementAPI

1

See [measurement](index.php?option=com_k2&view=item&id=826) interface.

audit

AuditAPI

1

See [auditing](index.php?option=com_k2&view=item&id=821) interface.

alarm

AlarmAPI

1

See [alarm](index.php?option=com_k2&view=item&id=824) interface.

user

UserAPI

1

See [user](index.php?option=com_k2&view=item&id=822) interface.

deviceControl

DeviceControlAPI

1

See [device control](index.php?option=com_k2&view=item&id=825)
interface.

### GET the Platform resource

Response body: application/vnd.com.nsn.cumulocity.platformApi+json

Example response:

    HTTP/1.1 200 OK
    Content-Type: application/vnd.com.nsn.cumulocity.platformApi+json;â€¦
    Content-Length: â€¦
     
    {
      "self" : "«URL to the platform API resource»",
      "event" : {
        "self" : "«URL to the event API resource»",
        "events" : { "self" : "«URL to event collection resource»" },
        "eventsForSourceAndType" : "«URL to event collection resource»?type={type}&source={source}"
        â€¦
      },
      "inventory" : {
        â€¦
      },
      â€¦
    }


# Root interface

To discover the URIs to the various interfaces of Cumulocity, a "root"
interface is provided. This root interface aggregates all the underlying
API resources. The root interface of the development sandbox is
accessible through http://«sandbox URL»/platform/. For more information
on the different API resources, please consult the respective API
section of this reference guide. Usage of the development sandbox is
subject to the [usage
terms](guides/reference-guide/developer-sandbox-usage-terms).

## Platform [/platform]

### Get the platform resource [GET]

+ Response 200 (application/vnd.com.nsn.cumulocity.platformApi+json)

  + Body

            {
              "self" : "«URL to the platform API resource»",
              "event" : {
                "self" : "«URL to the event API resource»",
                "events" : { "self" : "«URL to event collection resource»" },
                "eventsForSourceAndType" : "«URL to event collection resource»?type={type}&source={source}"
                …
              },
              "inventory" : {
                …
              },
              …
            }

# Akamai

## Setup Dev Environment

- [Setup Akamai Dev Environment - How-to video](https://www.youtube.com/watch?v=fy0Am-GPkQA)
- [Dev Enrironment Tutorial](https://techdocs.akamai.com/edgeworkers/docs/dev-environment-tutorial)
- [Docker Image](https://hub.docker.com/r/akamai/shell)

```
sudo docker run --rm -it -v $HOME/.edgerc:/root/.edgerc:ro --name akamai akamai/shell:latest
akamai --help
```

### Create a sandbox

Create a sandbox instance that you can use to test configuration changes in a local, isolated development environment.  

## Akamai EdgeWorkers

- [Set up the EdgeWorkers service](https://techdocs.akamai.com/edgeworkers/docs/set-up-the-edgeworkers-service)
- [Terraform module](https://registry.terraform.io/providers/akamai/akamai/latest/docs/guides/get_started_edgeworkers)
- [VS Code Extension](https://marketplace.visualstudio.com/items?itemName=akamaiEdgeworker.akamai-edgeworkers-vscode-extension)
- [Free Trial](https://techdocs.akamai.com/edgeworkers/docs/edgeworkers-free-trial)
- [Limitations](https://techdocs.akamai.com/edgeworkers/docs/limitations)

### Use Cases

- [Geo Redirect Use Case example](https://github.com/akamai/edgeworkers-examples/tree/master/edgecompute/examples/traffic-routing/redirect-geo)
- [response-manipulation-stream](https://github.com/akamai/edgeworkers-examples/tree/master/edgecompute/examples/stream/response-manipulation)
- [This example](https://github.com/akamai/edgeworkers-examples/tree/master/edgecompute/examples/typescript) demonstrates how to build an EdgeWorker written in TypeScript.

### Add EW on Akamai

- https://techdocs.akamai.com/edgeworkers/docs/add-edgeworkers-to-contract

- **To an existing contract**  
    If you have an active ​Akamai​ contract and login credentials for ​Akamai Control Center​ you can use these instructions to sign up for the EdgeWorkers Evaluation tier. The Evaluation tier lets you try EdgeWorkers for free and without a time limit.
    When you're ready to purchase EdgeWorkers you can also use Marketplace. If you need help signing up or would like more information, contact an ​Akamai​ account representative.
- **No contract**  
    If you're not already an ​Akamai​ customer or you don't have access to ​Akamai Control Center​ you can sign up for an EdgeWorkers free trial.

### Resource Tier

- https://techdocs.akamai.com/edgeworkers/docs/select-a-resource-tier

You can choose one of these resource tiers when you Create an EdgeWorker ID.  
For instructions on how to change the resource tier assigned to an EdgeWorkers function see Clone an EdgeWorker ID.  

Resource tier to support a GEO redirect use case: *Basic Compute*  
Integration with EdgeKV requires *Dynamic Compute* tier which is more expensive than the basic tier.  
EdgeWorkers are charged by the number of events invoked per month. For information about billing you should contact your ​Akamai​ account representative.  

### Events

- [Event model](https://techdocs.akamai.com/edgeworkers/docs/event-handler-functions)
- Unsupported HTTP methods: CONNECT, TRACE, OPTIONS

**onOriginRequest**: This happens just before sending the request to the origin. This event only happens if the response is not served from cache and not constructed on the edge. Use this event if you want to affect the response coming back from the origin. 

This function adds a device type header to the origin request:
```javascript
export function onOriginRequest(request) {
   if (request.device.isMobile) { 
      request.setHeader('X-MyHeader-DeviceType','Mobile'); 
   } 
   else if (request.device.isTablet) { 
      request.setHeader('X-MyHeader-DeviceType', 'Tablet'); 
   } 
   else { 
      request.setHeader('X-MyHeader-DeviceType', 'Desktop'); 
   }
}
```

### Best practices

https://techdocs.akamai.com/edgeworkers/docs/best-practices-for-performance

- Reduce EdgeWorkers invocations by using *onOrigin* events.
- Prevent empty events from adding overhead to requests
- Use *JSON.parse('string')* to instantiate larger JSON objects.
- Deploy smaller code bundles to reduce invocation time.
- Invoke multiple HTTP sub-requests in parallel.
- Cache individual subrequests.
- Cache the output of your *responseProvider*.
- As a general rule you should avoid passing sub-request headers back in the response to prevent intermittent data corruption errors that are hard to debug.

### Permissions

Provision credentials for ​Akamai​'s APIs with access to Property Manager (PAPI), ​Akamai​ Sandbox, and EdgeWorkers. 

- https://techdocs.akamai.com/edgeworkers/docs/manage-access-to-edgeworkers

| User type | Role |
| --------- | ---- |
| Dev | Viewer |
| DevOps Pipeline Account | Admin |

### Programming languages

EdgeWorkers supports ES2015 (ECMAScript 2015) compliant JavaScript.  
Typescript is also supported as a pre-compiled option via bindings to our EdgeWorkers JavaScript specification.

EW does not run a Node.js environment. Instead, lighter-weight ES2015 compliant JavaScript is executable within the EdgeWorkers environment.

You can use most self-contained JavaScript libraries that:
- Roll up into a flat .js file.
- You can include in the local EdgeWorkers code bundle.
- Do not try to make external calls.
- Fit within the EdgeWorkers system limits.

EdgeWorkers can read and write to PMUSER_ variables in the delivery property. See getVariable and setVariable for more information.  
EdgeWorkers has access to EdgeScape (stored in the request's User Location Object) and Device Characteristics (stored in the requests's Device Object).  

Debugging: [Enhanced debug headers](https://techdocs.akamai.com/edgeworkers/docs/enable-enhanced-debug-headers), [Sandbox CLI](https://techdocs.akamai.com/edgeworkers/docs/akamai-cli#sandbox-cli), and [JavaScript logging](https://techdocs.akamai.com/edgeworkers/docs/enable-javascript-logging).

### Error Handling

- https://techdocs.akamai.com/edgeworkers/docs/site-failover

You should use the Site Failover Property Manager behavior to define what logic to execute in the event that an EdgeWorkers function fails.  
- Redirect to a different location by specifying a redirect action. This action sends the end user to alternate content with an end user facing Location change - this is best suited for full HTML page requests.

## Akamai EdgeKV

- Akamai’s distributed key-value store that enables to build data-driven EdgeWorkers applications
- Access EdgeKV from an EdgeWorkers function(using JS helper library), using the management API, or CLI to perform common, day-to-day EdgeKV operations
- Enforce access control to EdgeKV databases by using tokens
- Direct access to EdgeKV from external clients is not supported
- The default geolocation is US on the staging and production network.
   - The available geoLocations on the production network also include EU (Europe) and JP (Japan). 
   - Setting it to a different geoLocation is not supported on the staging network.
- It uses eventual consistency model to perform writes and updates.
   - Inconsistency window: tipically 10 seconds, but can vary depending on network conditions.
   - During the inconsistency window, if a read operation is attempted immediately after a write operation, it can return stale data until all nodes are updated.


### Prerequirements

- [Add EdgeKV](https://techdocs.akamai.com/edgekv/docs/add-edgekv-to-your-contract) to your contract.
   - Enable EdgeKV by using the Marketplace app store found in ​Akamai Control Center​.
   - Or ask ​Akamai​ account team to enable it on your behalf. 
- Define development team [access and permissions](https://techdocs.akamai.com/edgekv/docs/manage-access-to-edgekv) to the related namespaces.
- Generate and retrieve EdgeKV-specific access token to authorize an EdgeWorker ID to access data within a given namespace.
- Entitlement for EdgeWorkers **Dynamic Compute** tier.
- As of May 2022, it requires node version 14 or higher to use the EdgeKV CLI.

### Data model design

- [EdgeKV data model](https://techdocs.akamai.com/edgekv/docs/edgekv-data-model)

#### Items
Items are key-value pairs where the key is a string and the value can be string or JSON format.  

#### Groups

Groups are logical containers for Items, and they are typically created to:
   - Organize similar data within a namespace
   - Ensure that a set of related keys can be retrieved together
   - Avoid key potential name collision

Groups cannot be manually created and deleted, they are automatically created or destroyed on demand when items are added and deleted from the group.  

#### Namespaces

Namespaces are logical containers, and should be used for groupings that are more durable over time. They are:
- Scarce resource in EdgeKV
- Logical separation of data within an organization
- The ability to reuse group/item key names between different projects or divisions without having to worry about potential name collisions.
- The ability to specify the storage location for data within the namespace using the geoLocation parameter.
- Lets administrators define user access and permissions to namespaces using Akamai Control Center group membership.

Data written to the namespace is replicated across several key locations within the specified geoLocation.

#### Account

The Account layer is the connection to your company’s existing account in ​Akamai Control Center​.

### AuthN and AuthZ

Access tokens used by an EdgeWorker ID are stored in your local edgekv_tokens.js file, which must be included in an EdgeWorkers code bundle.
- The administrative CLI provides the option to update the edgekv_tokens.js file automatically
- The administrative API requires the use of a separate tool to update the file.

### Sample code

- [Send a constructed response to a web page](https://techdocs.akamai.com/edgekv/docs/send-a-constructed-response-to-a-web-page)

```js
const edgeKv = new EdgeKV({ namespace: "default", group: "greetings" });

try {
        greeting = await edgeKv.getText({ item: key, default_value: default_greeting });
    } catch (error) {
        err_msg = error.toString();
        // use the EdgeWorkers JavaScript logger to securely log error messages.
        logger.log(encodeURI(err_msg).replace(/(%20|%0A|%7B|%22|%7D)/g, " "));
        greeting = default_greeting;
    }

```

### Limitations

- EdgeKV [limitations](https://techdocs.akamai.com/edgekv/docs/limits).  
- Akamai​ Sandbox cannot currently be used to test an EdgeWorker ID that includes EdgeKV functionality. Akaimi is working to fix that.
- EdgeKV is not supported on ​Akamai​ CDNs created to accommodate unique regional or governmental requirements (e.g. China).
- EdgeKV data cannot be used to calculate a cacheKey in an EdgeWorkers function.
- A timeout may occur when trying to initialize a namespace through the CLI.
   - Akamai recommends to retry the initialization using a larger timeout value directive with each CLI command, such as, --timeout 2000000. They are working to solve the issue.
- List groups within a namespace is not supported on sandbox mode.
- Listing groups in staging may return some sandbox groups.
   - Try to use the <groupId> and specify the sandboxId parameter to access sandbox data.

### Best practices

Ensure that you take the inconsistency window into account when writing JavaScript code that uses EdgeKV.  

For [data modelling](https://techdocs.akamai.com/edgekv/docs/best-practices-for-data-model-design):
- Identify the type of values you want to store
- Use namespaces sparingly
- Review the EdgeKV [limitations](https://techdocs.akamai.com/edgekv/docs/limits)
- Review EdgeWorkers [limitations](https://techdocs.akamai.com/edgeworkers/docs/resource-tier-limitations)
- Keep in mind that the EdgeKV data model may be different than your existing data model. Consider how you will map those two models.
- Consider the JSON nesting depth and data structure when storing data. To optimize the performance of your database use as simple a structure as possible.

For [access tokens](https://techdocs.akamai.com/edgekv/docs/access-tokens-1):
- Replace tokens before they expire.
   - You need to generate a new token and then update the *edgekv_tokens.js* file(s) with the new token and upload and activate the associated EdgeWorker bundles. 
- Do not store access tokens in your own systems, except in transient storage. For example, you can temporarily store an access token on your local filesystem until you have created and uploaded an EdgeWorker bundle, after which we recommend that you delete the local copy.
- Create a separate token for every unique use case for a given namespace.
- Limit access tokens to a namespace.
- Lock an access token to specific EdgeWorker IDs
- **(Coming soon, not possible today)** You should also consider creating separate tokens for the production and staging environments whereby the staging one can be retrieved by developers and the production one can be retrieved by the CI/CD system.

## Caching

- [Caching Strategies](https://techdocs.akamai.com/purge-cache/docs/cache-strategies)



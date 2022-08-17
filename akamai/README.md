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

## Caching

- [Caching Strategies](https://techdocs.akamai.com/purge-cache/docs/cache-strategies)



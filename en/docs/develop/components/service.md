# Service Components

Explore how to create, deploy, manage, and observe service components in Choreo.

## What is a service component?

A service component in Choreo is a component type that you can use to deploy and expose REST, GraphQL, or gRPC services. It is a fundamental building block for creating cloud-native applications in Choreo. They provide a simple and effective way of exposing functionality as a service to other components in the application or the outside world.

Service components encapsulate business logic and provide standardized interfaces, called endpoints, for communicating with other components or systems. You can deploy and scale them independently, which makes them highly flexible and adaptable to changing workloads.

With the help of the service component, developers can quickly create APIs and microservices, making it easier to implement and manage complex software systems. Service components can also be integrated with other Choreo components, such as message processors, connectors, and data sources, to create powerful end-to-end solutions.

## What are endpoints in-service components?

Endpoints are a crucial concept when developing services that need to be exposed to other services or applications. Services can expose multiple endpoints, each representing a unique entry point into the service. For example, a service may expose a REST API endpoint and a GraphQL endpoint, each providing different ways to interact with the service. Endpoints provide a way to define the specific network details for how a service can be accessed, such as the port number, protocol, and endpoint name. By defining these details, endpoints make it possible for other services and applications to discover and interact with the service in a standardized way.

Choreo defines endpoints by combining port binding, protocol, endpoint name, network visibility, endpoint schema, and additional protocol-related fields. Here are details about each field in the following table:

| Field | Description |
| ----- | ----------- |
| Name | A unique identifier for the endpoint within the service component. |
| Port | The network port on which the endpoint is accessible. |
| Type | The communication protocol used by the endpoint (e.g., HTTP, HTTPS, gRPC, etc.). |
| Network Visibility | The level of visibility granted to the endpoint. Determines the level of visibility granted to the endpoint, with the following options: <ul><li>Project: the endpoint can only be accessed by other components within the same project.</li><li>Organization: the endpoint can be accessed by any component within the same organization, but not by components outside of the organization.</li><li>Public: the endpoint can be accessed by any component, regardless of its location or organization.</li></ul> |
| Schema | specifies the structure and format of the data exchanged through the endpoint. |
| Context (HTTP and GraphQL only) | A context path that you add to the endpoint's URL for routing purposes. |

## Configuring endpoints

When you build a service component using the Dockerfile build-preset in Choreo, you can configure the endpoint details using the endpoints.yaml configuration file. You must place this file in the .choreo directory at the root of the build context path and commit it to the source repository.

!!! note
    When you create a service component with the `Ballerina preset`, Choreo automatically detects the endpoint details for REST API and GraphQL endpoints. You can override the autogenerated endpoint configuration by providing the endpoints.yaml file in the source directory


The endpoints.yaml file has a specific structure and contains the following details:

* name: A unique name for the endpoint, which Choreo will use to generate the managed API.
* port: The numeric port value that gets exposed via this endpoint.
* type: The type of traffic this endpoint is accepting, such as REST, GraphQL, or gRPC.
* visibility: The network level visibility of this endpoint, which defaults to "Project" if not specified. Accepted values are "Project", "Organization", or "Public".
* context: The context (base path) of the API that Choreo exposes via this endpoint. This field is mandatory if you set the endpoint type to REST or GraphQL.
* schemaFilePath: The schema definition file path is specified in this field and defaults to the wildcard route if not provided. For REST endpoint types, this field should be a relative path to the Docker context.

### Sample endpoints.yaml

File location:
```bash
<docker-build-context-path>/.choreo/endpoints.yaml
```

!!! note
    For components with Ballerina build preset `docker-build-context-path` should be replaced with `component-root`

File Content:
```yaml
# +required Version of the endpoint configuration YAML
version: 0.1

# +required List of endpoints to create
endpoints:
  # +required Unique name for the endpoint. (This name will be used when generating the managed API)
- name: Greeting 9090
  # +required Numeric port value that gets exposed via this endpoint
  port: 9090
  # +required Type of the traffic this endpoint is accepting. Example: REST, GraphQL, etc.
  # Allowed values: REST, GraphQL, GRPC
  type: REST
  # +optional Network level visibility of this endpoint. Defaults to Project
  # Accepted values: Project|Organization|Public.
  visibility: Project
  # +optional Context (base path) of the API that is exposed via this endpoint.
  # This is mandatory if the endpoint type is set to REST or GraphQL.
  context: /greeting
  # +optional Path to the schema definition file. Defaults to wild card route if not provided
  # This is only applicable to REST endpoint types.
  # The path should be relative to the docker context.
  schemaFilePath: greeting_openapi.yaml
```

## Exposing endpoint as managed APIs

Exposing endpoints as managed APIs is crucial to ensure secure and controlled access to the services being exposed. When a user wants to expose their written service to the outside world or to the organization at large, there is an inherent security risk involved. To mitigate this risk, Choreo platform is build with an internal (access within organization only) or external (publicly accessible) gateway that is protected with Choreo API management making the services secure by design.

!!! note
    This feature is not available for gRPC endpoints.

If you want to expose an endpoint as a managed API in Choreo, you need to set the network visibility to either Organization or Public. This allows the endpoint to be exposed through the Choreo API Gateway, which provides a number of benefits, including:

- Full lifecycle API Management
- API throttling
- secure APIs with policies
- API analytics and montioring

To expose an endpoint as a managed API, follow these steps:

* Expose APIs to external and internal cosumers
* Full lifecycle API Management
* API throttling
* Secure APIs with industry-standard authorization flows
* API analytics and monitoring

Once you deploy the service component, Choreo will expose the endpoint as a managed API through the Choreo API Gateway. You can then use the full lifecycle API management features provided by Choreo to test, deploy, maintain, monitor, and manage your API using the API management features.


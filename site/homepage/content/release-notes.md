+++
linkTitle = "Release Notes"
title = "What is new & noteworthy in Hono?"
description = "Information about changes in recent Hono releases. Includes new features, fixes, enhancements and API changes."
+++

## 2.5.0 (not released yet)

### Fixes & Enhancements

* When running in a Kubernetes cluster with nodes using cgroups v2, the 'hono.command_internal.*' Kafka topics were not
  being cleaned up. This has been fixed. Note that the solution requires the Hono protocol adapter pods to have
  a service account with an assigned RBAC role that allows to perform "get" on the "pods" resource.
* When using Pub/Sub messaging, there were potentially issues concerning the AMQP connection between protocol adapter
  and command router, leading for example to timeouts when MQTT devices subscribed/unsubscribed to the command topic.
  This has been fixed.
* The integration tests now use Apache Kafka 3.5.0 in Raft mode which no longer requires running a separate Apache Zookeeper
  instance and thus simplifies test setup and configuration.
* The command line client was still trying to connect to the insecure ports of the Sandbox. This has been changed so that
  the client now uses the TLS endpoints and requires the user to specify a trust store for validating the server certificate.
* Updated to Quarkus 3.2.6.Final

### Deprecations

* According to the [MongoDB Software Lifecycle Schedule](https://www.mongodb.com/support-policy/lifecycles) support
  for MongoDB 4.4 will end Feb 2024.
  Until that date the MongoDB based Device Registry will be tested with MongoDB 4.4, 5.0, 6.0 and 7.0 in the CI workflows.
  However, support for MongoDB 4.4 in Hono has been deprecated and will be removed in a future version altogether.
  Users are encouraged to migrate to MongoDB 6.0 or later.

## 2.4.1

### Fixes & Enhancements

* A device connected to the MQTT adapter would not get disconnected when sending a message after the device's credentials
  had already expired. This has been fixed.
* When running in a Kubernetes cluster with nodes using cgroups v2, the 'hono.command_internal.*' Kafka topics were not
  being cleaned up. This has been fixed. Note that the solution requires the Hono protocol adapter pods to have
  a service account with an assigned RBAC role that allows to perform "get" on the "pods" resource.
* When using Pub/Sub messaging, there were potentially issues concerning the AMQP connection between protocol adapter
  and command router, leading for example to timeouts when MQTT devices subscribed/unsubscribed to the command topic.
  This has been fixed.
* The command line client was still trying to connect to the insecure ports of the Sandbox. This has been changed so that
  the client now uses the TLS endpoints and requires the user to specify a trust store for validating the server certificate.

## 2.4.0

### New features

* Hono now supports using Google Pub/Sub as the messaging infrastructure. The Admin Guide and the API documentation have
  been amended with corresponding information on how to configure Hono and applications for using Pub/Sub.
* Hono's MQTT and HTTP adapters now support using JSON Web Tokens (JWT) for authentication. In particular, the adapters
  allow devices that have been connected to Google's IoT Core endpoints to connect to Hono instead. The User Guides have
  been amended with corresponding information regarding configuration and usage.
* The CoAP adapter now allows clients to retrieve the CoAP adapter's current time by means of including a query parameter
  and/or request option.
* The HTTP protocol adapter now supports authenticating devices using the JWT based authentication scheme employed by
  Google IoT Core.
* The command router now has experimental support for routing commands received via Google Pub/Sub to connected devices.
* The authentication identifier being created for devices that are using client certificate based auto-provising can now
  be specified as a pattern based on fields from the certificate's subject DN.

### Fixes & Enhancements

* The JDBC based device registry now supports searching for tenants and devices including paging. However, sorting is not
  implemented (yet) and the number of filter predicates is (currently) limited to one.
* The MQTT adapter failed to close a connection with a client after the JWT used by the client for authentication had expired.
  This has been fixed.
* Hono could not be built using Maven 3.9.0 and newer. This has been fixed.

## 2.3.2

### Fixes & Enhancements

* A device connected to the MQTT adapter would not get disconnected when sending a message after the device's credentials
  had already expired. This has been fixed.
* When running in a Kubernetes cluster with nodes using cgroups v2, the 'hono.command_internal.*' Kafka topics were not
  being cleaned up. This has been fixed. Note that the solution requires the Hono protocol adapter pods to have
  a service account with an assigned RBAC role that allows to perform "get" on the "pods" resource.
* The command line client was still trying to connect to the insecure ports of the Sandbox. This has been changed so that
  the client now uses the TLS endpoints and requires the user to specify a trust store for validating the server certificate.

## 2.3.1

### Fixes & Enhancements

* Using Maven 3.9.x for building Hono was not possible and resulted in an error. This has been fixed.
* The Device Registry User Guide has been amended with information on how to configure the trust anchor of a tenant
  using a PEM file.
* The demo keys and certificates that come with Hono for testing purposes had expired and have been recreated.

## 2.3.0

### New features

* Hono services and adapters could be started locally (not in containers) for development/test purposes.
* The MQTT adapter now has experimental support for authenticating devices using the JWT based authentication scheme
  employed by Google IoT Core.
* The protocol adapters now have experimental support for using Google Pub/Sub as the messaging infrastructure.
  Support is currently limited to publishing downstream events and telemetry messages to Pub/Sub.
* The JDBC based registry now supports the Device Registry Management API's *search Tenants* and *search Devices*
  operations. However, sorting and filtering is not yet implemented.

### Fixes & Enhancements

* The MQTT adapter did forward command messages for devices behind a gateway when the gateway subscription was
  targeting the gateway itself with topic name `command//[<gateway-id>]/req/#`. This has been fixed.
  For a subscription with such a topic name, only commands for the gateway itself are forwarded, not commands for
  devices, that the gateway acts on behalf of.
* The LoRaWAN adapter now transforms all devEUIs hex encoded and upper case. All LoRaWAN devices should therefore be
  registered upper case and hex encoded.

## 2.2.0

### New features

* All Hono components now support reading ECC based keys from PEM files that contain the Base64
  encoded DER-encoding of an ECPrivateKey as described in
  [RFC 5915, Section 4](https://datatracker.ietf.org/doc/html/rfc5915#section-4).
* The build process now supports creating container images for the `arm64` platform. Please refer to the
  [Developer Guide]({{% doclink "/dev-guide/building_hono/" %}}) for details.
* The LoraWAN protocol adapter has been extended with support for *Chirpstack V4* provider.
* Hono components now support producing JSON formatted log messages. Please refer to the
  [Admin Guide]({{% doclink "/admin-guide/monitoring-tracing-config/#logging" %}}) for details.
* Hono components can now be configured to use the OTEL Jaeger Remote Sampler extension which allows
  the sampling strategy to be retrieved from a Jaeger back-end dynamically during runtime.
  Please refer to the [Admin Guide]({{% doclink "/admin-guide/monitoring-tracing-config/#tracing" %}})
  for details.

### Fixes & Enhancements

* Documentation for versions up to 1.11 is removed as outdated.
* Optimized handling of MQTT and AMQP protocol adapter shutdown. The purpose is removal of redundant
  operations for devices which maintain a permanent connection during the restarting of adapter
  instances. This prevents delays and possible errors due the higher rate of invocations of the
  *unregister Command Consumer* operation on the Command Router service.
  When shutting down, the adapters no longer explicitly unregister Command Consumers and no longer send
  *disconnectedTtdEvent*s. Instead, the Command Router now sends the *connectedTtdEvent* and
  *disconnectedTtdEvent* on behalf of the protocol adapters.
  The Command Router now also sends a *disconnectedTtdEvent* when a command is received that is
  targeted at a device that got disconnected (and didn't reconnect) when a protocol adapter was
  shut down. This happens only once - during the processing of the first command.
  The Command Router configuration now also requires *hono.messaging* configuration if AMQP 1.0
  based messaging infrastructure is being used.
* The Mongo DB based device registry is now continuously tested to work with Mongo DB 5.0.

## 2.1.0

### New features

* The tracing configuration has been extended to now also support a rate-limiting sampler. See the
  [Monitoring & Tracing guide]({{% doclink "/admin-guide/monitoring-tracing-config/#tracing" %}}) for details.
* Add new metric for tracking the total number of Tenants in Hono.
* The Auth server component now provides an HTTP endpoint for retrieving the keys that applications should use for
  validating the signature of tokens issued by the Auth server. The keys are being transferred by means of a
  JSON Web Key set as defined by [RFC 7517](https://datatracker.ietf.org/doc/html/rfc7517). The Device Registry and
  Command Router components will use this endpoint to periodically download the keys if no key material has been configured
  explicitly.
* The handling of messages that have no payload and/or no content type has been harmonized across the protocol adapters.
  Adapters now uniformly accept messages from devices that have no payload if the device explicitly sets a non-empty content
  type on the message. Messages with a non-empty payload can be uploaded without specifying a content type. In the messages
  being forwarded to downstream consumers, the adapters will either use the devices's default content type, if defined, or
  otherwise fall back to `application/octet-stream` in such cases.
* A Linux x86_64 executable of the command line client has been added to the downloads page.

### Fixes & Enhancements

* The CoAP adapter did not properly consider the reduced minimum RAM requirements for starting up when running as a
  native executable on a SubstrateVM. This could have resulted in the adapter not starting up at all, if configured
  with less than ~150MB of RAM. This has been fixed.
* The HTTP protocol adapter and Device Registry now support a configuration property for explicitly setting a request's
  idle timeout. The timeout is configured with the property *idleTimeout*. This determines if a request will time out
  and be closed if no data is received or sent within the idle timeout period. The idle timeout is in seconds.
  A zero value means no timeout is used.
* The MQTT adapter skipped command or error (the first one) subscription if both were requested for the same device.
  This has been fixed.
* On startup, Hono components could get into a state that caused certain Kafka client metrics to not get reported.
  This has been fixed.
* Default messaging type changed to Kafka. Changed related documentation pages.  
* The native executable based Lora adapter container image failed to forward Lora meta information in messages being
  sent downstream. This has been fixed.
* The Command Router component might not have reached the *ready* state in case the Kafka broker got restarted during
  Command Router startup. This has been fixed.
* The mechanism used by the Command Router component to determine the state of protocol adapter instances has been
  improved.
* The CoAP adapter returned a `500` error code for PUT requests without a device identifier in the URI path. This has
  been fixed, now returning a response with a `404` error code instead.
* The command line client no longer throws an NPE when trying to upload an empty message without a content type to the
  AMQP adapter.

## 2.0.2

### Fixes & Enhancements

* The default value for the HTTP protocol adapter `hono.http.idleTimeout` configuration property has been increased
  from 60 to 75 seconds. This means HTTP requests with a `hono-ttd` value of 60 seconds (which is the tenant
  configuration `max-ttd` default) will not cause the idle timeout to be reached.
* The mechanism used by the Command Router component to determine the state of protocol adapter instances has been
  improved.
* The CoAP adapter returned a 500 error code for PUT requests without a device identifier in the URI path. This has
  been fixed, now returning a response with a 404 error code instead.

## 2.0.1

### Fixes & Enhancements

* The CoAP adapter did not properly consider the reduced minimum RAM requirements for starting up when running as a
  native executable on a SubstrateVM. This could have resulted in the adapter not starting up at all, if configured
  with less than ~150MB of RAM. This has been fixed.
* The HTTP protocol adapter and Device Registry now support a configuration property for explicitly setting the idle timeout.
  The timeout is configured with the property `idleTimeout`. This determines if a connection will timeout and be closed
  if no data is received or sent within the idle timeout period. The idle timeout is in seconds.
  A zero value means no timeout is used.
* The MQTT adapter skipped command or error (the first one) subscription if both are requested for the same device. This has been fixed.
* On startup, Hono components could get into a state that caused certain Kafka client metrics to not get reported.
  This has been fixed.
* The native executable based Lora adapter container image failed to forward Lora meta information in messages being
  sent downstream. This has been fixed.
* Default messaging type changed to Kafka. Changed related documentation pages.
* The Command Router component possibly did not reach the "ready" state in case the Kafka broker got restarted during
  Command Router startup. This has been fixed.
* The CoAP protocol adapter now uses Eclipse Californium 3.6.0.
* Upgraded to JJWT 0.11.5 which contains additional security guards against an ECDSA bug in Java SE versions
  15-15.0.6, 17-17.0.2, and 18 ([CVE-2022-21449](https://nvd.nist.gov/vuln/detail/CVE-2022-21449)).
  Note: if your application does not use these JVM versions, you are not exposed to the JVM vulnerability.
  The CVE is not a bug within JJWT itself - it is a bug within the above listed JVM versions, and the JJWT 0.11.5 release
  adds additional precautions within JJWT in case an application team is not able to upgrade their JVM in a timely manner.
* The Auth server failed to create a token when configured with an ECC based private key that does not use the P-256 curve.
  This has been fixed.

## 2.0.0

### New features

* The HTTP protocol adapter now allows authenticated gateway devices to omit the tenant ID from the URI of requests
  used to upload telemetry, event or command response messages.
* The CoAP protocol adapter now allows authenticated gateway devices to omit the tenant ID from the URI of requests
  used to upload telemetry, event or command response messages.
* The AMQP protocol adapter now allows authenticated gateway devices to omit the tenant ID from the address of
  messages used to upload telemetry or event messages.
* The MQTT protocol adapter now allows authenticated gateway devices to omit the tenant ID from the topic of
  messages used to upload telemetry or event messages.
* The CoAP adapter now exposes its resources using short endpoint name alternatives to the existing resources.
  Using the short endpoint names, devices can save a few bytes per request.
* The protocol adapters now support a configuration property for explicitly setting the percentage of the heap memory
  not to be used for maintaining device connections. This can be used to tune the adapter's memory utilization with
  regard to the overall amount of memory available to the JVM and the characteristics of the garbage collector being
  used.

### Fixes & Enhancements

* The Quarkus variant of the MongoDB based device registry failed to start up if the *hono.mongodb.dbName* property
  was not set. However, the DB name should not be required if a connection string is set using the
  *hono.mongodb.connectionString* property. This has been fixed.
* When a tenant or device gets disabled or deleted, any open AMQP or MQTT connections from clients having authenticated
  themselves as belonging to that tenant or device are getting closed now.
* Using OpenSSL with the Quarkus based variant of Hono components did not work as described in the Secure Communication
  guide. This has been fixed.
* The connection pool configuration for the HotRod client in the Quarkus variant of the Command Router component
  didn't support using property names in camel-case. This has been fixed.
* *HonoConnectionImpl* instances failed to release/close the underlying TCP/TLS connection when its *disconnect* or
  *shutdown* method had been invoked. This has been fixed.
* The LoraWAN protocol adapter has been extended with support for *Live Objects* provider.
* In the Quarkus variants of the MongoDB device registry and the Hono auth component, the provided metrics did not
  contain the default set of tags, as used in the other Hono components (e.g. *host* or *component-name*). This has been
  fixed.
* The device registry components now support the reporting of Kafka client metrics.
* The Java source code level of the Hono code has been raised from 11 to 17. This also means that a JDK version 17 or
  later is required for building and running Hono.
* The mechanism to delete obsolete `hono.command_internal.*` Kafka topics could have deleted still used topics in
  case the Kubernetes API server gave information about the running containers with a delay of several seconds. This has
  been fixed.
* The native executable based images of protocol adapters failed to deserialize a registration assertion if it included command
  endpoint information that had been configured for the device. This has been fixed.

### API Changes

* The MQTT adapter no longer accepts the `+` wild card character for the tenant ID in a topic filter used for
  subscribing to commands.
* Support for configuration properties that had been marked as deprecated in Hono versions before 2.0.0 has been
  removed.
* The Kura protocol adapter has been removed. Eclipse Kura gateways starting with version 4 can still connect to Hono
  using Hono's standard MQTT adapter.
* The file based device registry has been removed.
* The Spring Boot based variant of the AMQP protocol adapter has been removed.
* The Spring Boot based variant of the HTTP protocol adapter has been removed.
* The Spring Boot based variant of the MQTT protocol adapter has been removed.
* Usage and implementations of the Device Connection API have been removed.
* The Spring Boot based variant of the Command Router has been removed.
* The *amqp-device* client has been moved from the *client* module to the new *client-device-amqp* module. The
  `org.eclipse.hono.client.device.amqp.AmqpAdapterClientFactory` class is superseded by the new
  `org.eclipse.hono.client.device.amqp.AmqpAdapterClient` class.
* Removed deprecated *startServiceClient*, *stopServiceClient* and *updateLastGateway* methods of class
  *org.eclipse.hono.adapter.AbstractProtocolAdapterBase*.
* Removed deprecated *org.eclipse.hono.util.BufferResult* class.
* Removed deprecated *org.eclipse.hono.service.http.AbstractEventBusHttpEndpoint* class.
* The *client* module has been removed. Its deprecated classes have been removed and the remaining classes have been
  moved to the *client-common*, *amqp-connection* and *amqp-common* modules. The classes for configuring AMQP client
  connections have been moved from the *core* to the *amqp-connection* module.
* The *core* module no longer supports Java 8 but has been changed to require at least Java 17 as all the other modules.
* The *core* module is no longer an OSGi bundle.
* The `org.eclipse.hono.service.auth.AuthenticationService`'s method signatures have been changed to return a Future
  instead of accepting a `Handler<AsyncResult>`.
* The `org.eclipse.hono.client.amqp.connection.ConnectionFactory`'s method signatures have been changed to return a Future
  instead of accepting a `Handler<AsyncResult>`.
* Removed unused classes `org.eclipse.hono.util.EventBusMessage` and `org.eclipse.hono.util.MessageTap`.
* Moved `org.eclipse.hono.util.BaseMessageFilter` to service-base module.
* Moved `org.eclipse.hono.util.AmqpErrorException` to amqp-connection client module.
* Moved `org.eclipse.hono.util.TimeUntilDisconnectNotification` to application client module.
* The `org.eclipse.hono.util.RequestResponseResult` class hierarchy's dependency on the Proton-J `ApplicationProperties` class
  has been removed. Instead, the constructors now accept a standard Map containing the properties.
* Moved several AMQP 1.0 specific constants from `org.eclipse.hono.util.Constants` to
  `org.eclipse.hono.client.amqp.connection.AmqpConstants` in the amqp-connection client module.
* Moved the code for injecting and extracting a tracing context to/from AMQP 1.0 messages to the amqp-connection
  client module.
* Moved `org.eclipse.hono.util.TelemetryExecutionContext` to adapter-base module.
* The *amqp-device* client module no longer supports including arbitrary properties in telemetry, event and command
  response messages because it was undefined how these properties would be processed by the AMQP protocol adapter.
* The *application* client module no longer supports including arbitrary properties in command messages
  because it was undefined how these properties would be processed by the Hono components.
* The order of the method parameters of the `org.eclipse.hono.application.client.CommandSender` interface have been changed
  so that mandatory parameters come first.
* The *hono-service-base-quarkus* module has been removed. Its content has been integrated in the *hono-service-base* module. 
* The *hono-adapter-base-quarkus* module has been removed. Its content has been integrated in the *hono-adapter-base* module. 
* The *amqp-device* client no longer requires supplying a tenant ID when creating a client or invoking any of its
  methods.
* The Hono components now export tracing data using the OpenTelemetry Protocol (OTLP). See the
  [Monitoring & Tracing guide]({{% doclink "/admin-guide/monitoring-tracing-config/#tracing" %}}) for details on how to
  configure the trace sampling and the OpenTelemetry Collector endpoint to send the traces to. The `jaeger` Maven build
  profile used for including the Jaeger client in the Hono images has been removed. In order to forward Hono traces to a
  Jaeger back-end, the OpenTelemetry Collector should be configured accordingly.
* The Hono components now support reading and writing trace context information from and to messages exchanged with
  other applications in the format defined by the [W3C Trace Context](https://www.w3.org/TR/trace-context/) and the
  [W3C Baggage](https://www.w3.org/TR/baggage/) specifications. The Jaeger native propagation format used in earlier Hono
  versions is not supported any more.
* The Hono command line client has been re-implemented using the Picocli framework. The synopsis of the client has been
  changed to provide for a richer user experience. The Getting Started guide has been updated to use the new command
  line client as well.
* The Hono protocol adapter, command router and device registry Maven modules and Docker images have been renamed,
  removing the *-vertx* and *-quarkus* parts from the names.
* The *hono-adapters-quarkus* and *hono-services-quarkus* Maven modules, used as parent modules for protocol adapter
  and service implementations, have been renamed to *hono-adapters-parent* and *hono-services-parent*, respectively.
* The *hono.messages* metrics have been renamed to *hono.telemetry* and *hono.messages.received* has been renamed to
  *hono.telemetry.processing.duration*.
* The *hono.commands* metrics have been renamed to *hono.command*.
* The *hono.downstream* metrics have been renamed to *hono.amqp*, *hono.downstream.sent* has been renamed to
  *hono.amqp.delivery.duration* and *hono.downstream.full* has been renamed to *hono.amqp.nocredit*.
* Hono’s service components and protocol adapters now use the Quarkus SmallRye Health extension to implement the health
  check and metrics endpoints. That means the health check server configuration is now done via the corresponding Quarkus
  configuration properties and not via *hono.healthCheck* properties any more. See the
  [Monitoring & Tracing guide]({{% doclink "/admin-guide/monitoring-tracing-config/#health-check-server-configuration" %}})
  for details on how to configure the health check server.

### Deprecations

* Support for implementing readiness and liveness checks based on Vert.x Health Checks has been deprecated and will be
  removed in a future version. New health checks should be implemented according to MicroProfile Health.

## 1.12.3

### Fixes & Enhancements

* The mechanism to delete obsolete `hono.command_internal.*` Kafka topics could have deleted still used topics in
  case the Kubernetes API server gave information about the running containers with a delay of several seconds. This has
  been fixed.
* The CoAP adapter did not properly consider the reduced minimum RAM requirements for starting up when running as a
  native executable on a SubstrateVM. This could have resulted in the adapter not starting up at all, if configured
  with less than ~150MB of RAM. This has been fixed.
* The HTTP protocol adapter and Device Registry now support a configuration property for explicitly setting the idle timeout.
  The timeout is configured with the property `idleTimeout`. This determines if a connection will timeout and be closed
  if no data is received or sent within the idle timeout period. The idle timeout is in seconds.
  A zero value means no timeout is used.
* The MQTT adapter skipped command or error (the first one) subscription if both are requested for the same device. This has been fixed.
* The native executable based Lora adapter container image failed to forward Lora meta information in messages being
  sent downstream. This has been fixed.
* Upgraded to JJWT 0.11.5 which contains additional security guards against an ECDSA bug in Java SE versions
  15-15.0.6, 17-17.0.2, and 18 ([CVE-2022-21449](https://nvd.nist.gov/vuln/detail/CVE-2022-21449)).
  Note: if your application does not use these JVM versions, you are not exposed to the JVM vulnerability.
  The CVE is not a bug within JJWT itself - it is a bug within the above listed JVM versions, and the JJWT 0.11.5 release
  adds additional precautions within JJWT in case an application team is not able to upgrade their JVM in a timely manner.
* The Auth server failed to create a token when configured with an ECC based private key that does not use the P-256 curve.
  This has been fixed.
* The CoAP protocol adapter now uses Eclipse Californium 2.7.3.

## 1.12.2

### Fixes & Enhancements

* The Quarkus variant of the MongoDB device registry did not accept HTTP endpoint credentials that worked with the
  Spring Boot based variant because password hashes were created in lower case instead of upper case. This has been fixed.
* In some cases, invalid HTTP requests to the HTTP adapter or the Device Registry caused a response
  with a 500 status code instead of the corresponding 4xx status code. This has been fixed.
* *HonoConnectionImpl* instances failed to release/close the underlying TCP/TLS connection when its *disconnect* or
  *shutdown* method had been invoked. This has been fixed.
* In the Quarkus variants of the MongoDB device registry and the Hono auth component, the provided metrics did not
  contain the default set of tags, as used in the other Hono components (e.g. *host* or *component-name*). This has been
  fixed.

## 1.12.1

### Fixes & Enhancements

* The Quarkus variant of the MongoDB based device registry failed to start up if the *hono.mongodb.dbName* property
  was not set. However, the DB name should not be required if a connection string is set using the
  *hono.mongodb.connectionString* property. This has been fixed.
* Using OpenSSL with the Quarkus based variant of Hono components did not work as described in the Secure Communication
  guide. This has been fixed.
* The connection pool configuration for the HotRod client in the Quarkus variant of the Command Router component
  didn't support using property names in camel-case. This has been fixed.

## 1.12.0

### New Features

* The Mongo DB based device registry now supports multiple tenants to use the same trust anchors for authenticating
  devices based on client certificates. The devices belonging to such tenants need to indicate the tenant identifier
  using the Server Name Indication extension during their TLS handshake with a protocol adapter. Please refer to the
  Device Registry Management API for details on how to configure tenants accordingly. Please refer to the protocol
  adapter user guides and the Device Identity concept page for details regarding device authentication based on client
  certificates.
* A Quarkus based variant of the MongoDB device registry has been added.

### Fixes & Enhancements

* The device registry containers might not have started up properly when used with Kafka as the messaging
  infrastructure. This has been fixed.
* The MongoDB device registry did not accept HTTP endpoint credentials that worked with Hono <= 1.10 because
  password hashes were created in lower case instead of upper case. This has been fixed.
* The native executable of the Command Router component did not start when configured with an embedded cache.
  This has been fixed.
* There was an issue trying to send connection events concerning unauthenticated MQTT/AMQP devices. This has been fixed.

### Deprecations

* The Spring Boot based variant of the MongoDB device registry has been deprecated and marked for removal in Hono 2.0.0.

### API Changes

* The MongoDB device registry now uses `PBKDF2` as default hash algorithm for the HTTP endpoint credential verification.
  Hono deployments updated from an earlier version with password hashes created using the previous SHA-512 default need
  to either create new password hashes or set `hono.registry.http.auth.hashAlgorithm` to `SHA512`. See the
  [MongoDB based registry configuration guide]({{% doclink "/admin-guide/mongodb-device-registry-config/#service-configuration" %}})
  for details.

## 1.11.3

### Fixes & Enhancements

* *HonoConnectionImpl* instances failed to release/close the underlying TCP/TLS connection when its *disconnect* or
  *shutdown* method had been invoked. This has been fixed.
* In the Quarkus variant of the Hono auth component, the provided metrics did not contain the default set of tags, as
  used in the other Hono components (e.g. *host* or *component-name*). This has been fixed.

## 1.11.2

### Fixes & Enhancements

* The MongoDB device registry did not accept HTTP endpoint credentials that worked with Hono <= 1.10 because
  password hashes were created in lower case instead of upper case. This has been fixed.
* The native executable of the Command Router component did not start when configured with an embedded cache.
  This has been fixed.
* There was an issue trying to send connection events concerning unauthenticated MQTT/AMQP devices. This has been fixed.

## 1.11.1

### Fixes & Enhancements

* The device registry containers might not have started up properly when used with Kafka as the messaging
  infrastructure. This has been fixed.

## 1.11.0

### New Features

* The Authentication Server now also reports the `hono.connections.attempts` metric which counts the number of authentication
  attempts made by clients.
* The JDBC DB based registry now also supports enforcement of registration limits configured at the tenant level.
  In particular, the maximum number of devices and the maximum number of credentials per device can be set in
  a tenant's registration-limits property. Please refer to the User Guide for details.
* Kafka clients used by a component can now be configured individually instead of all clients of a type using the same
  configuration. The prefixes that are prepended to the configuration properties of the native Kafka client have
  changed. For existing configuration properties prefixed with `hono.kafka.commonClientConfig` properties, no change is
  needed. Other configurations with specific consumer/producer/admin client properties have to be adapted. Please refer
  to the [Hono Kafka Client Configuration Guide]({{% doclink "/admin-guide/hono-kafka-client-configuration" %}}) for details.
* The protocol adapters now include a *ttl* header/property in every message being forwarded, regardless of message
  type. This allows a consumer of a message to easily determine if the message should be processed or considered
  *expired* already. The device registry supports the definition of default *ttl* values for the different types of
  messages at both the tenant and device level. Please refer to the Tenant API for details regarding the
  corresponding default property names to use.

### Fixes & Enhancements

* The number of credits that the Mongo DB based registry would flow to a newly connected client could not be set using
  the documented environment variable `HONO_CREDENTIALS_SVC_RECEIVERLINKCREDIT`. Instead, the initial link credit can be
  configured using environment variable `HONO_REGISTRY_AMQP_RECEIVERLINKCREDIT`. The Mongo DB based registry's admin guide
  has been updated accordingly.
* When using Kafka messaging, there could possibly be an exception during startup of the Command Router component, 
  meaning the component was potentially only available after a number of startup attempts. This has been fixed.
* The Quarkus based variant of the Lora protocol adapter did not start up unless the `HONO_LORA_COMMANDENABLEDTENANTS`
  environment variable had been set. This has been fixed by removing this (unused) variable from the Lora adapter
  altogether.
* All downstream messages that can be consumed via Hono's north bound APIs now include a `creation-time` header
  which indicates the point in time at which the message has been created.
* The error messages returned by protocol adapters when sending commands received via Kafka now include a reasonable
  error description.
* The tenant's configuration property `auto-provisioning-device-id-template` is now extended to support more
  subjectDN's attributes namely *Organizational Unit Name (OU)* and *Organization Name (O)*. For more information
  please refer to the [Device Registry Management API]({{% doclink "/api/management#/tenants/createTenant" %}}).
* The container images published by the Hono project are now built on top of Java 17 base images provided by the
  [Eclipse Temurin project](https://adoptium.net/).
* The Hono container images released with tag 1.10.0 failed to start up when not running as user `root` because the
  Java process was lacking authority to create a temporary directory in the file system's root folder (`/`).
  This has been fixed.
* Command response messages published via Kafka did not contain the `tenant_id` header. This has been fixed.
* Hono's components now support configuring the ciphers used in the TLS handshake when connecting to Infinispan servers.
  For the Command Router component [remote data grid configuration]({{% doclink "/admin-guide/command-router-config/#remote-cache" %}})
  this can be done by setting the `hono.commandRouter.cache.remote.sslCiphers` property.
* When using Kafka messaging, the Hono components will now retry creating the Kafka clients in case the Kafka bootstrap
  server URLs are not yet resolvable. This will prevent unnecessary restarts of the Hono components during initial
  deployment.
* The native image variant of the Command Router component failed to connect to an Infinispan server using SASL
  SCRAM. This has been fixed.
* The lora adapter supports unconfirmed uplink data for the firefly provider.
* The poll timeout used by the Kafka consumer clients in the Hono components can now be configured individually.
  Please refer to the [Hono Kafka Client Configuration Guide]({{% doclink "/admin-guide/hono-kafka-client-configuration/#consumer-configuration-properties" %}})
  for details.

## Deprecations

* The Spring Boot based variant of the protocol adapters has been deprecated and marked for removal in Hono 2.0.0.
* The Kura 3 protocol adapter has been deprecated and marked for removal in Hono 2.0.0. Support for Kura version 4 and
  later is still available by means of Hono's standard MQTT adapter.

## API Changes

* The `hono.kafka.defaultClientIdPrefix` configuration property needs to be removed from existing configurations.
  Configuring parts of the created Kafka client identifiers should usually not be needed any more. To still set a custom
  part, the `client.id` property value may be used instead. It is adopted as prefix for created client identifiers.
* A new API for notifications among Hono components has been introduced. For Hono deployments using an AMQP messaging
  network, it has to be made sure that addresses with the `notification/` prefix are configured to use multicast.
  See the `tests/src/test/resources/qpid/qdrouterd-with-broker.json` file for an example Qpid Dispatch Router
  configuration.

## 1.10.1

### Fixes & Enhancements

* The number of credits that the Mongo DB based registry would flow to a newly connected client could not be set using
  the documented environment variable `HONO_CREDENTIALS_SVC_RECEIVERLINKCREDIT`. Instead, the initial link credit can be
  configured using environment variable `HONO_REGISTRY_AMQP_RECEIVERLINKCREDIT`. The Mongo DB based registry's admin guide
  has been updated accordingly.
* When using Kafka messaging, there could possibly be an exception during startup of the Command Router component,
  meaning the component was potentially only available after a number of startup attempts. This has been fixed.
* The Quarkus based variant of the Lora protocol adapter did not start up unless the `HONO_LORA_COMMANDENABLEDTENANTS`
  environment variable had been set. This has been fixed by removing this (unused) variable from the Lora adapter
  altogether.
* The Hono container images released with tag 1.10.0 failed to start up when not running as user `root` because the
  Java process was lacking authority to create a temporary directory in the file system's root folder (`/`).
  This has been fixed.
* Command response messages published via Kafka did not contain the `tenant_id` header. This has been fixed.
* Hono's components now support configuring the ciphers used in the TLS handshake when connecting to Infinispan servers.
  For the Command Router component [remote data grid configuration]({{% doclink "/admin-guide/command-router-config/#remote-cache" %}})
  this can be done by setting the `hono.commandRouter.cache.remote.sslCiphers` property.
* When using Kafka messaging, protocol adapters may have shown a prolonged delay in processing command & control messages
  in certain situations in which one Kafka cluster node was shortly unavailable. This has been fixed.
* The native image variant of the Command Router component failed to connect to an Infinispan server using SASL
  SCRAM. This has been fixed.
* The lora adapter supports unconfirmed uplink data for the firefly provider.

## 1.10.0

### New Features

* The JDBC and Mongo DB based registry implementations now support configuration of a regular expression that should
  be used to validate authentication identifiers (user names) of hashed-password credentials. Please refer to the
  corresponding Admin Guides for details.
* The Command Router component is now able to determine the state of protocol adapter instances, preventing command
  & control messages to be sent to already terminated adapter instances. Hono Kubernetes deployments where not all
  protocol adapters are part of the same Kubernetes cluster and namespace that the Command Router component is in,
  need to disable this feature via the `hono.commandRouter.svc.kubernetesBasedAdapterInstanceStatusServiceEnabled`
  property. Please refer to the [Command Router Admin Guide]({{% doclink "/admin-guide/command-router-config/#service-configuration" %}})
  for details.
* The authentication provider used to guard access to the Mongo DB based registry implementation's HTTP endpoint
  can now be configured using environment variables. Please refer to the registry's Admin Guide for details.
* The Registry Management API has been extended with an operation to delete all devices (including credentials) of a
  tenant. Both the Mongo DB and the JDBC based registry implementations support this operation.
* The protocol adapters and the Command Router component now by default report a set of metrics concerning the clients
  used for sending and receiving messages via Kafka. Please refer to the 
  [Hono Kafka Client Configuration Guide]({{% doclink "/admin-guide/hono-kafka-client-configuration/#kafka-client-metrics-configuration" %}})
  for additional information.

### Fixes & Enhancements

* The Quarkus based variants of Hono's components now support configuring the Hot Rod client with a key and/or
  trust store in order to enable TLS secured connections to Infinispan servers and to authenticate using a
  client certificate.
* The MongoDB based DeviceManagementService erroneously removed the original device registration when trying to
  register a new device using the existing device's identifier. This has been fixed.
* The Mongo DB based registry implementation now uses a proper DB index to find credentials by type and authentication
  ID. This will speed up query execution significantly when there are a lot of devices registered for a tenant.
* The JDBC based device registry's *get Credentials* operation used by the protocol adapters now also supports
  matching credentials against a given *client context*.
* The device registry implementations did not return a JSON object in a response to a failed request as specified
  in the Device Registry Management API. This has been fixed.
* The MongoDB based registry erroneously rejected requests that would result in multiple tenants having an empty
  set of trusted CAs. This has been fixed.
* The _ttl_ header in downstream messages with Kafka had been set in seconds instead of milliseconds, as defined
  by the [API specification]({{% doclink "/api/telemetry-kafka" %}}). This has been fixed.

## 1.9.1

### Fixes & Enhancements

* The Quarkus based variants of Hono's components now support configuring the Hot Rod client with a key and/or
  trust store in order to enable TLS secured connections to Infinispan servers and to authenticate using a
  client certificate.
* The MongoDB based DeviceManagementService erroneously removed the original device registration when trying to
  register a new device using the existing device's identifier. This has been fixed.
* The Mongo DB based registry implementation now uses a proper DB index to find credentials by type and authentication
  ID. This will speed up query execution significantly when there are a lot of devices registered for a tenant.
* The JDBC based device registry's *get Credentials* operation used by the protocol adapters now also supports
  matching credentials against a given *client context*.
* The device registry implementations did not return a JSON object in a response to a failed request as specified
  in the Device Registry Management API. This has been fixed.
* The tracing output in error scenarios has been improved in the Mongo DB based device registry.
* The MongoDB based registry erroneously rejected requests that would result in multiple tenants having an empty
  set of trusted CAs. This has been fixed.

## 1.9.0

### New Features

* The Mongo DB based registry now supports enforcement of registration limits configured at the tenant level.
  In particular, the maximum number of devices and the maximum number of credentials per device can be set in
  a tenant's registration-limits property. Please refer to the Mongo DB User Guide for details.
* Hono now sends a device provisioning notification when a device or a gateway is successfully auto-provisioned.
* Northbound applications sending request/response Command & Control messages via Kafka will now receive
  a notification about a failed command delivery via a command response message. See the
  [Command &amp; Control API for Kafka]({{% doclink "/api/command-and-control-kafka/" %}}) for details.
* The Mongo DB based device registry implementation now supports transparent (symmetric) encryption of Pre-Shared Key
  secrets. Please refer to the user guide for details regarding configuration.

### Fixes & Enhancements

* The value of the properties `auto-provisioned` and `auto-provisioning-notification-sent` had always been *false* when
  retrieving device registration information using the MongoDB based registry implementation. This has been fixed.
* The LoRA protocol adapter will now skip registering itself for receiving commands for a given gateway device if
  that gateway device has no command endpoint defined. The adapter will now also free command consumer resources when
  the idle timeout of the corresponding tenant (configured via the `hono.lora.tenantIdleTimeout` property) has elapsed
  and the tenant is already removed or disabled.
* The device registry implementations have already supported the limitation of the body size of requests to the
  `/tenants` and `/credentials` resources of the HTTP based Device Registration Management API.
  However, the admin guides did not cover the corresponding `HONO_REGISTRY_HTTP_MAXPAYLOADSIZE` configuration variable
  yet. The `/devices` resources have been added to the scope of the feature and the admin guides have been amended
  accordingly.
* The protocol adapters now invoke the *set last known gateway* Command Router service operation a lot less frequently,
  reducing the load on the Command Router component if gateways send messages on behalf of devices at a high rate.
  The *last known gateway* entry for a device is now set with a delay of at most 400ms as part of a batch request.
* The `keyStorePassword` and `trustStorePassword` properties of the Hono clients now also support specifying a file
  to read the password from. If the property value starts with `file:` then the value after the prefix is interpreted as
  as the path to a file to read the password from.
* The registry implementations failed to reject a request to update a device's empty set of credentials, e.g. right
  after the device has been created, if the request contained a secret having an ID. In fact, the registry
  implementations would have accepted such a request even if the secrets did not contain a password/key/etc at all
  but only the identifier. This has been fixed.
* A potential issue processing Command & Control messages from a Kafka cluster while Command Router instances are
  getting stopped or started has been fixed.
* The rate at which the Command Router component handles Command & Control messages from a Kafka cluster is now
  limited to prevent potential memory issues and reduce the load on dependent services. The limit value is adopted from
  the configured `max.poll.records` Kafka consumer configuration value.
* The default properties of the Hono CLI tool have been updated to match typical Hono installations. It provides now 
  3 types of profiles that need to be combined: 1. select the "mode": `receiver` or `command`; 2. select the "target":
  `sandbox` or `local` (aims for deployment in Minikube but works for every deployment of the Helm chart); 3. select the 
  "messaging-type": `kafka` (if not set, it defaults to AMQP-based messaging). For details refer to the file 
  `application.yml` of the CLI module.

### Deprecations

* The file based device registry implementation has been deprecated and will be removed in a future version of Hono.
  Please use the Mongo DB or JDBC based registry implementations instead. The JDBC based registry can be configured
  to use an H2 database in either *embedded* or *in-memory* mode. The former can be used to persist data to the local
  file system while the latter keeps all data in memory only.
* The MongoDB based registry implementation no longer supports the configuration variables for disabling modification
  of existing data. In real life deployments this feature has no meaning because write access to data will need to
  be authorized more explicitly anyway, e.g. at the tenant level.

## API Changes

* The client classes used by the protocol adapters for accessing the device registry, the Command Router
  and the south bound C&C APIs have been reorganized into dedicated modules.
  In particular, the *adapter*, *adapter-amqp* and *adapter-kafka* modules have been resolved into modules
  *command*, *command-amqp*, *command-kafka*, *registry*, *registry-amqp*, *telemetry*, *telemetry-amqp* and
  *telemetry-kafka*. This allows Hono's components to define more specific dependencies on client classes
  that they require. This change should have no effect on application clients.
* The *set last known gateway* Command Router API operation has been extended to also support setting multiple
  values in one request.

## End of life

* The Maven profiles for compiling in support for exporting metrics to Graphite and InfluxDB have been removed.

## 1.8.3

### Fixes & Enhancements

* The Quarkus based variants of Hono's components now support configuring the Hot Rod client with a key and/or
  trust store in order to enable TLS secured connections to Infinispan servers and to authenticate using a
  client certificate.
* The Mongo DB based registry implementation now uses a proper DB index to find credentials by type and authentication
  ID. This will speed up query execution significantly when there are a lot of devices registered for a tenant.
* The MongoDB based registry erroneously rejected requests that would result in multiple tenants having an empty
  set of trusted CAs. This has been fixed.

## 1.8.2

### Fixes & Enhancements

* The MQTT adapter didn't close the CONNECT tracing span and didn't report metrics on failed connection attempts. This
  has been fixed.
* The registry implementations failed to reject a request to update a device's empty set of credentials, e.g. right
  after the device has been created, if the request contained a secret having an ID. In fact, the registry
  implementations would have accepted such a request even if the secrets did not contain a password/key/etc at all
  but only the identifier. This has been fixed.
* A potential issue processing Command & Control messages from a Kafka cluster while Command Router instances are
  getting stopped or started has been fixed.

## 1.8.1

### Fixes & Enhancements

* Letting the trace sampling settings of the Hono components be defined via sampling strategies served by the Jaeger
  Collector did not work for components using Quarkus native images. This has been fixed.
* Command messages with no payload could not be sent to an MQTT device. This has been fixed.
* The value of the properties `auto-provisioned` and `auto-provisioning-notification-sent` are always *false* while
  retrieving device registration information using the MongoDB based registry implementation. This has been fixed now.
* The Command Router service could have gotten into a state of very high CPU utilization when protocol
  adapters submitted non-existing tenant IDs for which command routing should be re-enabled. This has been fixed.
* The LoRA protocol adapter will now free command consumer resources when the idle timeout of the corresponding tenant
  (configured via the `hono.lora.tenantIdleTimeout` property) has elapsed and the tenant is already removed or disabled.
* The device registry implementations have already supported the limitation of the body size of requests to the
  `/tenants` and `/credentials` resources of the HTTP based Device Registration Management API.
  However, the admin guides did not cover the corresponding `HONO_REGISTRY_HTTP_MAXPAYLOADSIZE` configuration variable
  yet. The `/devices` resources have been added to the scope of the feature and the admin guides have been amended
  accordingly.

## 1.8.0

### New Features

* The CoAP adapter now supports authentication of client certificates using ECDSA based cipher suites.
* The JDBC-based device registry implementation now supports the automatic creation of the database schema, both for
  device registration and tenant data. This is especially useful for experimental setups where an embedded database,
  such [as H2 provides](http://www.h2database.com/html/features.html#embedded_databases), is sufficient. To enable
  automatic schema creation, activate the application profile `create-schema`.
* Hono's components now support configuration of supported TLS cipher suites. The cipher suites can be configured
  separately for both the endpoints exposed by the components as well as the clients used for accessing service
  endpoints exposed by other components. Please refer to the corresponding admin guides for details regarding the
  corresponding configuration variables.
* Hono now supports *auto-provisioning* of gateways. For more information please refer to the
  [Gateway Provisioning]({{% doclink "/concepts/device-provisioning/#automatic-gateway-provisioning" %}})
  concept and to the [Device Registry Management API]({{% doclink "/api/management#/tenants/createTenant" %}})
  on how to configure a tenant's trusted CA authority for that.
* Now the tenant configuration supports a new property namely *auto-provisioning-device-id-template* in it's trusted CA
  section. During auto-provisioning of devices and gateways, the device identifier is generated based on this template
  and used for the device registration. For more information please refer to the
  [Device Provisioning]({{% doclink "/concepts/device-provisioning/" %}}) concept and to the
  [Device Registry Management API]({{% doclink "/api/management#/tenants/createTenant" %}})
  on how to configure a tenant's trusted CA authority for that.
* The Hono CLI supports now Kafka as a messaging system. Please refer to the module's
  [README](https://github.com/eclipse-hono/hono/tree/master/cli) file for examples of using the CLI to receive events and
  telemetry data and send commands.
* The example business application supports now Kafka as a messaging system. Please refer to the
  [Developer Guide]({{% doclink "/dev-guide/java_client_consumer/" %}}) for details.

### Fixes & Enhancements

* The CoAP adapter did not correctly track the time it took to forward a command message to a device. This has been fixed.
* Sending requests using the Hono AMQP request-response client erroneously increased the `hono.downstream.timeout` metric.
  This has been fixed.
* Support for uplink messages from the Embedded LNS on MultiTech gateways.
* The *hono.connections.attempts* metric has been extended with a `cipher-suite` tag which contains the name of the
  cipher suite that is used in a device's attempt to establish a TLS based connection to an adapter.
* The Quarkus based Command Router native image failed to start an embedded cache that was configured to persist data
  to the local file system. This has been fixed.
* The delivery of a command message sent to an AMQP device potentially didn't get settled if the connection to the
  AMQP device got disconnected. This has been fixed.
* The Prometheus based resource limit checks' performance has been improved. This should result in considerably less
  load on the Prometheus server when failing over all of a crashed/stopped adapter instance's device connections.
* The Hono component container images now contain the *Gson* library which is required by the Jaeger client for
  processing sampling strategy configuration retrieved from the Jaeger Collector.
* The Kafka based implementation of the north bound application client
  `org.eclipse.hono.application.client.kafka.impl.KafkaApplicationClientImpl` now supports sending commands and
  receiving responses in a *request/response* fashion.

### Deprecations

* The `org.eclipse.hono.client.ApplicationClientFactory`, `org.eclipse.hono.client.AsyncCommandClient` and
  `org.eclipse.hono.client.CommandClient` classes have been deprecated. Client code should use
  `org.eclipse.hono.application.client.ApplicationClient` instead.

## 1.7.4

### Fixes & Enhancements

* Letting the trace sampling settings of the Hono components be defined via sampling strategies served by the Jaeger
  Collector did not work for components using Quarkus native images. This has been fixed.
* Command messages with no payload could not be sent to an MQTT device. This has been fixed.
* The value of the properties `auto-provisioned` and `auto-provisioning-notification-sent` are always *false* while
  retrieving device registration information using the MongoDB based registry implementation. This has been fixed now.
* The Command Router service could have gotten into a state of very high CPU utilization when protocol
  adapters submitted non-existing tenant IDs for which command routing should be re-enabled. This has been fixed.
* The LoRA protocol adapter will now free command consumer resources when the idle timeout of the corresponding tenant
  (configured via the `hono.lora.tenantIdleTimeout` property) has elapsed and the tenant is already removed or disabled.
* The device registry implementations have already supported the limitation of the body size of requests to the
  `/tenants` and `/credentials` resources of the HTTP based Device Registration Management API.
  However, the admin guides did not cover the corresponding `HONO_REGISTRY_HTTP_MAXPAYLOADSIZE` configuration variable
  yet. The `/devices` resources have been added to the scope of the feature and the admin guides have been amended
  accordingly.

## 1.7.3

### Fixes & Enhancements

* The Hono component container images now contain the *Gson* library which is required by the Jaeger client for
  processing sampling strategy configuration retrieved from the Jaeger Collector.

## 1.7.2

### Fixes & Enhancements

* The Quarkus based Command Router native image failed to start an embedded cache that was configured to persist data
  to the local file system. This has been fixed.
* The delivery of a command message sent to an AMQP device potentially didn't get settled if the connection to the
  AMQP device got disconnected. This has been fixed.

## 1.7.1

### Fixes & Enhancements

* The CoAP adapter did not correctly track the time it took to forward a command message to a device. This has been fixed.
* The downstream mapping endpoint was not correctly serialized which resulted in not being able to send commands using the downstream mapping endpoint. This has been fixed.
* When using Kafka as messaging system, the Command Router now creates the internal Command & Control topic with the
  replication factor defined in the broker `default.replication.factor` setting.
* Sending requests using the Hono AMQP request-response client erroneously increased the `hono.downstream.timeout` metric.
  This has been fixed.

## 1.7.0

### New Features

* Apache Kafka is now supported as a messaging system for command messages.
  This can be enabled by configuring protocol adapters to use Hono's new Kafka-based
  client. Please refer to [Hono Kafka Client Configuration]({{% doclink "/admin-guide/hono-kafka-client-configuration/" %}})
  for details. The new [Command &amp; Control API for Kafka]({{% doclink "/api/command-and-control-kafka/" %}}) defines
  how applications can use Apache Kafka to send command messages to devices.
  Note that support for Kafka based messaging is still considered an **experimental** feature.
* The Device Registry Management API has been extended now to support an optional unique identifier
  for trust anchors belonging to a tenant. The file, JDBC and MongoDB based device registries
  support this feature. Please refer to the
  [Device registry management API]({{% doclink "/api/management#/tenants/" %}}) for details.
* The LoraWAN protocol adapter has been extended with support for *The Things Stack* provider.
* The LoraWAN protocol adapter now supports command and control for providers *Chirpstack*, *Firefly* and *Loriot*.
* Added (experimental) Quarkus based variant of the Authentication service.
* Added (experimental) Quarkus based variant of the CoAP protocol adapter.
* Added (experimental) Quarkus based variant of the Command Router component.
* The container images for the (experimental) Quarkus based variant of Hono components are now being published on
  Docker Hub as well. The JVM based image names contain a `-quarkus` suffix whereas the native image names contain a
  `-quarkus-native` suffix. Note that the Quarkus based protocol adapter images do **not** support using the Device
  Connection service but require a connection to the Command Router service being configured instead.
* The MQTT adapter now lets devices subscribe on a new error topic to get informed about errors during the processing
  of telemetry, event or command response message. With such an error subscription in place, the default behaviour
  on such errors is now to keep the MQTT connection open. Please refer to the
  [MQTT Adapter User Guide]({{% doclink "/user-guide/mqtt-adapter/#error-reporting-via-error-topic" %}}) for details.
* The MQTT adapter now supports mapping the command payload through an external http service.
* The Command Router component has been promoted from *tech preview* to *fully supported*.
* The AMQP adapter now closes the network connection to the device if any terminal errors happen. Please refer to the
  [AMQP Adapter User Guide]({{% doclink "/user-guide/amqp-adapter/#error-handling" %}}) for details.

### Fixes & Enhancements

* The common configuration property for setting the vert.x instance's max-event-loop-execute-time had erroneously
  been documented as `HONO_VERTX_MAX_EVENT_LOOP_EXECUTE_TIME_MILLIS`, accepting an integer representing the duration as
  number of milliseconds. However, the correct property is named `HONO_VERTX_MAX_EVENT_LOOP_EXECUTE_TIME` and accepts an
  ISO-8601 Duration string instead of an integer.
* The MQTT adapter failed to handle a command response message if the corresponding tenant object wasn't available
  in the cache. This has been fixed.
* A failed connection attempt in the default `org.eclipse.hono.connection.ConnectionFactory` implementation could
  have led to the AMQP connection not getting closed, occupying connection resources. This has been fixed.
* Validation of MQTT topics containing property bags has been improved, preventing unhandled exceptions.
* The hugo themes for the Hono website and the documentation have been updated to the latest versions respectively.
  In order for the site module build to succeed with the `hugo` binary installed locally, the
  `site/homepage/themes/hugo-universal-theme` and `site/documentation/themes/hugo-theme-learn` folders need to be deleted.
  They will automatically be (re-)created as part of the build.
* The Infinispan client used by the protocol adapters, the Device Connection and Command Router services has been
  updated to version 11.0.9. The syntax of configuration file used for defining an embedded cache in the Device
  Connection and/or Command Router service has changed as documented
  [in the Infinispan version details](https://infinispan.org/docs/stable/titles/upgrading/upgrading.html#upgrading_from_10_1_to_11_0).
* The Command Router no longer uses the `embedded-cache` *Spring profile* in order to configure an embedded cache.
  Instead, the Command Router determines the type of cache (embedded or remote) by means of the
  `HONO_COMMANDROUTER_CACHE_REMOTE_SERVERLIST` configuration variable. Please refer to the
  [Command Router Admin Guide]({{% doclink "/admin-guide/command-router-config/#data-grid-connection-configuration" %}})
  for details.
* The Prometheus based resource limit checks can now be configured with a time out for establishing the TCP connection
  to the Prometheus server. This is useful to limit the time it takes to invoke the Prometheus Query API.
  Please refer to the
  [Protocol Adapter Common Configuration guide]({{% doclink "/admin-guide/common-config/#resource-limits-checker-configuration" %}})
  for details.

### API Changes

* The already deprecated classes `org.eclipse.hono.service.cache.SpringBasedExpiringValueCache` and
  `org.eclipse.hono.service.cache.SpringCacheProvider` have been removed.
* The `org.eclipse.hono.service.AbstractApplication` and `org.eclipse.hono.service.AbstractBaseApplication` classes
  have been moved to `org.eclipse.hono.service.spring.AbstractApplication` and `org.eclipse.hono.service.spring.AbstractApplication`
  in the newly added `service-base-spring` module respectively.
* The existing `mapper` configuration has been renamed to `downstream-message-mapper`.
* The Command Router component now requires the configuration of a tenant service client. Please refer to 
  [Tenant Service Connection Configuration]({{% doclink "/admin-guide/command-router-config/#tenant-service-connection-configuration" %}})
  for details.

### End of Life

* The `build-docker-image` build profile is no longer activated automatically if the *docker.host* Maven property
  is set. This has been changed in order to prevent the `build-docker-image` and `build-native-image` profiles being
  activated at the same time when running something like

  ```sh
  mvn clean install -Ddocker.host=tcp//host:port -Pbuild-native-image
  ```

  Activating the `build-docker-image` profile by default can easily be achieved by adding `-Pbuild-docker-image` to
  the *MAVEN_OPTS* environment variable instead.

## 1.6.2

### Fixes & Enhancements

* The CoAP adapter did not correctly track the time it took to forward a command message to a device. This has been fixed.
* Sending requests using the Hono AMQP request-response client erroneously increased the `hono.downstream.timeout` metric.
  This has been fixed.

## 1.6.1

### Fixes & Enhancements

* The common configuration property for setting the vert.x instance's max-event-loop-execute-time had erroneously
  been documented as `HONO_VERTX_MAX_EVENT_LOOP_EXECUTE_TIME_MILLIS`, accepting an integer representing the duration as
  number of milliseconds. However, the correct property is named `HONO_VERTX_MAX_EVENT_LOOP_EXECUTE_TIME` and accepts an
  ISO-8601 Duration string instead of an integer.
* The MQTT adapter failed to handle a command response message if the corresponding tenant object wasn't available
  in the cache. This has been fixed.
* A failed connection attempt in the default `org.eclipse.hono.connection.ConnectionFactory` implementation could
  have led to the AMQP connection not getting closed, occupying connection resources. This has been fixed.
* Validation of MQTT topics containing property bags has been improved, preventing unhandled exceptions.
* The protocol adapters might have run into a situation where devices connected to adapters did no longer receive
  commands when using the Command Router service. The problem occurred when a Command Router service instance had been
  restarted while one or more protocol adapters where connected to it. This has been fixed.

## 1.6.0

### New Features

* Apache Kafka is now supported as a messaging system for events and telemetry messages.
  This can be enabled by configuring protocol adapters to use Hono's new Kafka-based
  client. Please refer to [Hono Kafka Client Configuration]({{% doclink "/admin-guide/hono-kafka-client-configuration/" %}})
  for details.
* New APIs have been added for the Kafka-based messaging. Please refer to 
  [Telemetry API for Kafka]({{% doclink "/api/telemetry-kafka/" %}}) and 
  [Event API for Kafka]({{% doclink "/api/event-kafka/" %}}) for the specifications.
* The MQTT adapter now allows clients to indicate whether they want the target device's tenant and/or device IDs
  to be included in the topic used when publishing commands.
* The caching behavior of the protocol adapters' AMQP based registry clients has been changed. All adapter
  Verticle instances now share a single cache instance *per service*. In particular, there is a single cache for
  all responses returned by the Tenant, Device Registration and Credentials service respectively.
  In addition, each cache is now being used for all responses to requests regardless of the tenant. Consequently, the
  service client configurations' *responseCacheMinSize* and *responseCacheMaxSize* properties now determine
  the overall number of responses that can be cached *per service*. In previous versions the properties determined
  the number of entries *per client instance and tenant*. The new approach allows for better control over the maximum
  amount of memory being used by the cache and should also increase cache hits when deploying multiple adapter
  Verticle instances.
  The `org.eclipse.hono.adapter.client.registry.amqp.ProtonBasedTenantClient` now makes sure that only a single
  request to the Tenant service is issued when multiple (parallel) *get* method invocations run into a cache miss.
  This should reduce the load on the Tenant service significantly in scenarios where devices of the same
  tenant connect to an adapter at a high rate, e.g. in when re-connecting after one or more adapter pods have
  crashed.
* The Device Registry Management API's *update credentials* operation has been extended to allow specifying the
  *auth-id* and validity period implicitly by means of including a (Base64 encoded) client certificate in the new
  *cert* property. This can be used instead of specifying the client certificate's subject DN and public key's
  validity period explicitly in the *auth-id* and *secrets* properties. This should make setting the correct *auth-id*
  value much less error prone.
* Hono now supports *auto-provisioning* of devices that connect via gateway. For more information please refer to the
  [Device Provisioning]({{% doclink "/concepts/device-provisioning/#gateway-based-auto-provisioning" %}}) concept and to the
  [Device registry management API]({{% doclink "/api/management#/devices/createDeviceRegistration" %}}) on how to
  create a device registration for a gateway which is enabled for auto-provisioning.
* The Device Registry Management API has been extended now to support searching tenants with optional filters,
  paging and sorting options. Please refer to the
  [Device registry management API]({{% doclink "/api/management#/tenants/searchTenants" %}}) for details.
* The MongoDB based device registry now supports searching tenants with optional filters, paging and sorting options.

### Fixes & Enhancements

* The Mongo DB based Credentials service implementation failed to return the credentials matching the given
  *type* and *auth-id* if multiple credentials of the same *type* but with different *auth-id* values were
  registered for a device. This has been fixed.
* The Mongo DB based registry container would have failed to start if the connection to the Mongo DB could not
  be established quickly enough. This has been fixed by decoupling the creation of indices from the start up process.
* The protocol adapters erroneously indicated a client related error to devices if the downstream AMQP container
  rejected a message with an `amqp:resource-limit-exceeded` error condition. This has been fixed so that the adapters
  now correctly indicate a server related problem instead.
* The containers for the Device Registry implementations, the Authentication server, the Device Connection and
  Command Router services did not shut down gracefully upon receiving a SIGTERM signal. This has been fixed.
* The LoRA protocol adapter failed to start due to a missing bean declaration. This has been fixed.

### Deprecations

* The `org.eclipse.hono.service.cache.SpringCacheProvider` and `org.eclipse.hono.service.cache.SpringBasedExpiringValueCache`
  classes have been deprecated to further reduce the dependency on Spring Boot. Clients should use
  `org.eclipse.hono.service.cache.CaffeineCacheProvider` and `org.eclipse.hono.service.cache.CaffeineBasedExpiringValueCache`
  instead.
* Most of the public interfaces of the legacy `client` module have been deprecated. Client code should be
  adapted to use the corresponding interfaces and implementations from the `clients/adapter`, `clients/adapter-amqp`
  and `clients/application-amqp` modules instead.
* The Authentication server's `HONO_AUTH_SVC_PERMISSIONS_PATH` configuration property has been changed to no
  longer accept generic Spring resource URIs. The value is now required to be a file system path. The path
  may still contain a `file://` prefix in order to not break existing configurations. However, users are encouraged
  to remove the prefix as it will no longer be supported in versions starting with 2.0.0.
  The default permissions file has been removed from the Authentication server code base. Consequently,
  permissions can no longer be loaded from the class path using a `classpath://` URI. In practice this should
  have no impact because the Hono chart comes with a default permissions file which the Authentication
  server is pre-configured to load from the file system.

## 1.5.2

### Fixes & Enhancements

* The protocol adapters erroneously indicated a client related error to devices if the downstream AMQP container
  rejected a message with an `amqp:resource-limit-exceeded` error condition. This has been fixed so that the adapters
  now correctly indicate a server related problem instead.
* The containers for the Device Registry implementations, the Authentication server, the Device Connection and
  Command Router services did not shut down gracefully upon receiving a SIGTERM signal. This has been fixed.
* The LoRA protocol adapter failed to start due to a missing bean declaration. This has been fixed.

## 1.5.1

### Fixes & Enhancements

* The Mongo DB based Credentials service implementation failed to return the credentials matching the given
  *type* and *auth-id* if multiple credentials of the same *type* but with different *auth-id* values were
  registered for a device. This has been fixed.
* When using the Command Router or Device Connection service component with an embedded cache, a cache
  configuration file was required. Now, a default configuration will be used if no configuration file is
  given.
* The Mongo DB based registry container would have failed to start if the connection to the Mongo DB could not
  be established quickly enough. This has been fixed by decoupling the creation of indices from the start up process.
* The AMQP and MQTT adapters would erroneously forward commands to unauthorized gateway devices under certain
  conditions. This has been fixed.

## 1.5.0

### New Features

* The CoAP adapter has been promoted from *experimental* to *fully supported*.
* The Hono Client now supports several configuration properties that can be used
  to limit its resource usage. In particular the AMQP connection's *max-frame-size*,
  the AMQP session's incoming window size and the *max-message-size* of receiver
  links can be configured (and thus limited).
* A new way of routing Command & Control messages from the AMQP messaging network
  to the target protocol adapters has been introduced. For that, a new 
  [Command Router service]({{% doclink "/admin-guide/command-router-config" %}})
  component is used, receiving command messages and routing them to the appropriate
  protocol adapters. Protocol adapters supply routing information to the component
  by means of a new [Command Router API]({{% doclink "/api/command-router/" %}}).
  Protocol adapters can be configured to either use that new API or they can continue
  using the now deprecated [Device Connection API]({{% doclink "/api/device-connection/" %}})
  instead, meaning command routing will be done without using the Command Router.
* The pre-built Hono Docker images available from Docker Hub now include the Jaeger
  OpenTracing client.

### Fixes & Enhancements

* The file based as well as the MongoDB based registry implementations now remove the shared-key from
  PSK credentials returned in the response of the Management API's *get credentials* operation.
* The Device Registry Management API erroneously declared the *plaintext password* conveyed in the
  *update credentials* operation's request payload as a Base64 encoded byte array instead of a
  plain string. This has been fixed.
* The file based as well as the Mongo DB based registry implementations had failed to do both
  updating an existing secret (referred to by ID) and adding a new secret to the same credentials
  in a single request. This has been fixed.
* The property names for specifying the patterns for validating Tenant and Device IDs have been fixed
  in the admin guides for the file based and the Mongo DB based registry implementations.
* The registry implementations did not accept X.509 credentials in an update Credentials request.
  They also failed to remove existing credentials of a device if they were not included in an
  update Credentials request. This has been fixed.
* The file based device registry also now supports searching devices for a tenant with optional filters,
  paging and sorting options.
  Please refer to the [Device registry management API]({{% doclink "/api/management#/devices/searchDevicesForTenant" %}})
  for details.
* The wildcards `?` and `*` are now supported by the search devices operation in the MongoDB based device registry. 
  Please refer to the [Device registry management API]({{% doclink "/api/management#/devices/searchDevicesForTenant" %}})
  for details. 
* Command messages that have their payload in an AMQP body section whose type isn't supported in Hono
  now get rejected, instead of getting forwarded to the device with an empty payload.
* The authentication providers in Hono use `CredentialsObject.getCandidateSecrets` to retrieve valid secrets.
  The secrets are currently filtered based on their validity period regardless of whether the status is enabled
  or not. This has been fixed now, so that the disabled secrets are filtered out.
* The MQTT adapter now supports specifying the content-type of a telemetry/event message via a MQTT property bag.
* The MQTT adapter now sets the MQTT client identifier as *client-id* in the payload of a Credentials API *get*
  operation request also when authenticating a device using the username/password mechanism. Previously that was
  only done for the client certificate authentication mechanism.
* The HTTP adapter did not properly forward the QoS level for events when the *qos-level* header is not set 
  or set to AT_MOST_ONCE. This has been fixed.
* An HTTP device sending a command response request with no `Content-Type` header meant that the northbound
  application received a message with an empty content type. Now, the `application/octet-stream`
  content type is set, as it is also done for telemetry/event HTTP requests with no `Content-Type` header.
* There were errors sending Connection Events via the Events API if the corresponding tenant didn't
  have a non-default `max-ttl` resource limit configuration. This has been fixed.

### API Changes

* The deprecated configuration property `singleTenant` of the protocol adapters and the device registry has been removed.
* The default pattern for valid device identifiers used for the file based and the MongoDB based registry
  implementations now also contains a colon for compatibility with Eclipse Ditto.

### End of Life

* The *hono-jmeter* Maven module along with the JMeter plugin that is contained in it hasn't been updated for
  over a year and is also not used in any automated test runs.
  JMeter has turned out not to be the best choice for conducting load tests of Hono because it is difficult to
  generate the needed AMQP message load with its (blocking) thread based execution model.
  The JMeter plugin therefore has been removed.

## 1.4.5

### Fixes & Enhancements

* The tenant timeout mechanism doesn't close command consumer resources anymore if there are still active
  command subscriptions from AMQP or MQTT devices of the corresponding tenant.
* The Mongo DB based Credentials service implementation failed to return the credentials matching the given
  *type* and *auth-id* if multiple credentials of the same *type* but with different *auth-id* values were
  registered for a device. This has been fixed.
* The Mongo DB based registry container would have failed to start if the connection to the Mongo DB could not
  be established quickly enough. This has been fixed by decoupling the creation of indices from the start up process.
* The AMQP and MQTT adapters would erroneously forward commands to unauthorized gateway devices under certain
  conditions. This has been fixed.

## 1.4.4

### Fixes & Enhancements

* The HTTP adapter did not properly forward the QoS level for events when the *qos-level* header is not set 
  or set to AT_MOST_ONCE. This has been fixed.
* An HTTP device sending a command response request with no `Content-Type` header meant that the north bound
  application received a message with the content type set to an empty string. Now, the content type property
  isn't set in this case.
* There were errors sending Connection Events via the Events API if the corresponding tenant didn't
  have a non-default `max-ttl` resource limit configuration. This has been fixed.

## 1.4.3

### Fixes & Enhancements

* The file based as well as the Mongo DB based registry implementations now remove the shared-key from
  PSK credentials returned in the response of the Management API's *get credentials* operation.
* The Device Registry Management API erroneously declared the *plain text password* conveyed in the
  *update credentials* operation's request payload as a Base64 encoded byte array instead of a
  plain string. This has been fixed.
* The file based as well as the Mongo DB based registry implementations had failed to do both
  updating an existing secret (referred to by ID) and adding a new secret to the same credentials
  in a single request. This has been fixed.
* The property names for specifying the patterns for validating Tenant and Device IDs have been fixed
  in the admin guides for the file based and the Mongo DB based registry implementations.
* The registry implementations did not accept X.509 credentials in an update Credentials request.
  They also failed to remove existing credentials of a device if they were not included in an
  update Credentials request. This has been fixed.
* The AMQP protocol adapter did accept messages from clients (devices) exceeding the adapter's configured
  max-message-size. This has been fixed and the adapter now closes the link to the device in this case.

### API Changes

* The default pattern for valid device identifiers used for the file based and the MongoDB based registry
  implementations now also contains a colon (`:`) for compatibility with Eclipse Ditto.

## 1.4.0

### New Features

* The protocol adapters now report connection attempts made by devices
  in a new metric. In particular, the metric includes a tag reflecting the outcome
  of the attempt to establish a connection and the reason for failure.
  Please refer to the [Metrics API]({{% doclink "/api/metrics" %}}) for details.
* A new Quarkus based HTTP protocol adapter is now available. This adapter version provides better memory consumption and
  startup times comparing to the existing one.
* The LoraWAN protocol adapter has been extended with support for the *Actility Enterprise* provider.
* The LoraWAN protocol adapter has been extended with support for the *Orbiwise* provider.
* Added metrics for tracking the RTT between sending an AMQP message to receiving the disposition.
* The CoAP adapter now supports configuration of the *timeoutToAck* parameter at the tenant level
  in addition to the adapter level. If set, the tenant specific value is used for all devices of the
  particular tenant. If not set, the value defined at the adapter level is used.
* The Device Registry Management API has been extended now to support searching devices for a tenant
  with optional filters, paging and sorting options.
  Please refer to the [Device registry management API]({{% doclink "/api/management#/devices/searchDevicesForTenant" %}})
  for details. 
* The MongoDB based device registry now supports searching devices for a tenant with optional filters,
  paging and sorting options.

### Fixes & Enhancements

* The MongoDB based device registry now checks for tenant existence during device registration and credentials management operations.

### Deprecations
 
* The configuration property `singleTenant` of the protocol adapters and the device registry is now deprecated
  and planned to be removed in a future release. The use case of a system with just a single tenant should be
  realized by configuring just one tenant in the device registry.
* The `HONO_REGISTRY_REST_*` configuration properties of the file based device registry have been deprecated
  in favor of corresponding properties with the `HONO_REGISTRY_HTTP_` prefix.

## 1.3.2

### Fixes & Enhancements

* The HTTP adapter did not properly forward the QoS level for events when the *qos-level* header is not set 
  or set to AT_MOST_ONCE. This has been fixed.
* An HTTP device sending a command response request with no `Content-Type` header meant that the northbound
  application received a message with the content type set to an empty string. Now, the content type property
  isn't set in this case.
* There were errors sending Connection Events via the Events API if the corresponding tenant didn't
  have a non-default `max-ttl` resource limit configuration. This has been fixed.

## 1.3.1

### Fixes & Enhancements

* The file based as well as the Mongo DB based registry implementations now remove the shared-key from
  PSK credentials returned in the response of the Management API's *get credentials* operation.
* The Device Registry Management API erroneously declared the *plain text password* conveyed in the
  *update credentials* operation's request payload as a Base64 encoded byte array instead of a
  plain string. This has been fixed.
* The file based as well as the Mongo DB based registry implementations had failed to do both
  updating an existing secret (referred to by ID) and adding a new secret to the same credentials
  in a single request. This has been fixed.
* The property names for specifying the patterns for validating Tenant and Device IDs have been fixed
  in the admin guides for the file based and the Mongo DB based registry implementations.
* The registry implementations did not accept X.509 credentials in an update Credentials request.
  They also failed to remove existing credentials of a device if they were not included in an
  update Credentials request. This has been fixed.
* The AMQP protocol adapter did accept messages from clients (devices) exceeding the adapter's configured
  max-message-size. This has been fixed and the adapter now closes the link to the device in this case.

### API Changes

* The default pattern for valid device identifiers used for the file based and the MongoDB based registry
  implementations now also contains a colon (`:`) for compatibility with Eclipse Ditto.

## 1.3.0

### New Features

* The LoraWAN protocol adapter has been extended with support for the *ChirpStack* provider.
* Hono's integration tests can now be run with a Jaeger back end in order to collect tracing
  information.
* The AMQP 1.0 event message based Connection Event producer now sets a TTL on the event messages
  it produces. The TTL is the *max TTL* configured at the tenant level.
* A new Device Registry implementation based on MongoDB database is now available in Hono.
  Please refer to the [MongoDB Device Registry User Guide]({{% doclink "/user-guide/mongodb-based-device-registry" %}})
  for additional information.
* Hono protocol adapters can now be configured to use address rewriting when opening AMQP 1.0 links to the messaging network.
  This allows an easier deployment in multi-tenant messaging environments.
  Please refer to the [Hono Client Configuration Guide]({{% doclink "/admin-guide/hono-client-configuration/#address-rewriting" %}})
  for additional information.

### Fixes & Enhancements

* An admin guide for the CoAP adapter has been added.
* The Prometheus based resource limits checker now supports configuring a query timeout.
  Please refer to [Resource Limits Checker Configuration]({{% doclink "/admin-guide/common-config/#resource-limits-checker-configuration" %}})
  for additional information.
* When sending a command message to a device, the AMQP adapter now waits for a
  configurable period of time (default is 1 second) for the acknowledgement from the device.
  If none is received, the downstream command sender gets back a `released` outcome.
  Please refer to the `sendMessageToDeviceTimeout` property description in the
  AMQP Adapter admin guide for additional information.
* The client for storing device connection information to a data grid now supports configuring
  the name of the cache to store the data in.
* When the connection to a device is closed or lost, a protocol adapter instance will now
  stop listening for commands targeted at the device.
* The AMQP adapter before accepting any connections checks if the connection limit is exceeded 
  or not and if the adapter is enabled or not. These checks are currently done inside the
  `AmqpAdapterSaslAuthenticatorFactory`. Thereby, if any of these checks failed, the AMQP adapter
  reported authentication failure instead of the actual reason. This has been fixed now.
* The AMQP adapter did not consider the `HONO_CONNECTIONEVENTS_PRODUCER` configuration variable
  to set a Connection Event producer. This has been fixed.
* The *logging* Connection Event producer now supports configuring the level
  at which information should be logged. The producer type *none* has been added to explicitly
  turn off connection event reporting altogether.
  Please refer to the *Common Configuration* admin guide for details.
* The packages `org.eclipse.hono.service.credentials`, `org.eclipse.hono.service.management`,
  `org.eclipse.hono.service.management.credentials`, `org.eclipse.hono.service.management.device`,
  `org.eclipse.hono.service.management.tenant`, `org.eclipse.hono.service.registration` and
  `org.eclipse.hono.service.tenant` have been moved from the *service-base* to the
  *device-registry-base* module.
* The Device Connection service did return a 500 error code if no *last known gateway* could
  be found for a device ID. This has been fixed so that the service now returns a 404 in that
  case as specified by the Device Connection API.
* The cache based Device Connection service implementation now applies a lifespan of 28 days
  when setting/updating cache entries containing *last known gateway* information. This means
  no global expiration configuration is needed anymore for the cache.
* Protocol adapters failed to re-establish receiver links for commands to be routed after loss
  of connection to the AMQP Messaging Network. This has been fixed.
* The AMQP adapter reported an incorrect number of connections if resource limits had been
  defined and exceeded. This has been fixed.
* The device registry management HTTP endpoints now reject requests to register or update objects
  containing unknown properties. In the past such properties had been simply ignored.
* The Device Registry Management API has been amended with example messages and more thorough
  description of operations.
* The CoAP adapter has been enhanced to asynchronously look up a device's PSK during the
  DTLS handshake. This will allow the adapter to handle a lot more handshakes concurrently.
  The adapter also employs a more efficient message de-duplicator which reduces the adapter's
  memory footprint.

### API Changes

* The `getRemoteContainer` method in `org.eclipse.hono.client.HonoConnection` has been
  renamed to `getRemoteContainerId`.
* The `getName` method in `org.eclipse.hono.connection.ConnectionFactory` has been removed
  and an additional `connect` method has been added.
* The *set command-handling protocol adapter instance* operation of the *Device Connection* API
  has been extended to support an additional parameter which can be used to indicate the
  maximum amount of time that the given information is to be considered valid.
* The `hono-core` module no longer embeds any external classes by default.
  This includes the *OpenTracing API* and *Spring Crypto* classes. You can still
  embed the classes, as in Hono versions 1.2.x and before, by enabling the
  Maven profile `embed-dependencies` (e.g. using the command line switch
  `-Pembed-dependencies`). By default this profile is not active.
* The `org.eclipse.hono.client.DeviceRegistration` interface's *get* methods have been removed
  because the Device Registration API does not define a corresponding operation.
  Consequently, the C&C functionality of the Kerlink Lora provider which relied on the *get*
  method has been removed.
* Protocol adapters implementing the `org.eclipse.hono.service.AbstractAdapterConfig` class
  now need to implement the `getAdapterName` method. The `customizeDownstreamSenderFactoryConfig`
  method has been renamed to `getDownstreamSenderFactoryConfigDefaults`, while now returning
  properties instead of working on given ones. The new method name now more accurately conveys
  what the method is used for. The same change has been applied to the other `customize[*]Config`
  methods in the `AbstractAdapterConfig` class.
* The file based as well as the MongoDB based device registry implementations now disallow certain
  special characters in device and tenant identifiers. The configuration properties
  `HONO_REGISTRY_HTTP_TENANT_ID_PATTERN` and `HONO_REGISTRY_HTTP_DEVICE_ID_PATTERN` (and corresponding ones with
  prefix `HONO_REGISTRY_REST` for the file based registry) can be used to override the patterns for matching
  valid identifiers. Please refer to the 
  [file based registry]({{% doclink "/admin-guide/file-based-device-registry-config/#service-configuration" %}}) or
  [MongoDB based registry]({{% doclink "/admin-guide/mongodb-device-registry-config/#service-configuration" %}})
  configuration guide for details.
 
### Deprecations
 
 * The configuration property `HONO_MQTT_COMMAND_ACK_TIMEOUT` of the MQTT adapter is now deprecated
   and planned to be removed in a future release. Use `HONO_MQTT_SEND_MESSAGE_TO_DEVICE_TIMEOUT` instead.

## 1.2.4

### Fixes & Enhancements

* The `HonoConnection` implementation didn't use a delay before a reconnect attempt after
  a certain number of reconnect attempts (58 with the default configuration) had already
  failed. This has been fixed.
* An error when freeing Command & Control related resources of an idle tenant has been fixed.
* The Hotrod based DeviceConnectionClientFactory has been improved to prevent locking of
  objects in a clustered cache.
* The AMQP adapter reported an incorrect number of connections if resource limits had been
  defined and exceeded. This has been fixed.

## 1.2.3

### Fixes & Enhancements

* The Device Connection service did return a 500 error code if no *last known gateway* could
  be found for a device ID. This has been fixed so that the service now returns a 404
  in that case as specified by the Device Connection API.
* The cache based Device Connection service implementation now applies a lifespan of 28 days
  when setting/updating cache entries containing *last known gateway* information. This means
  no global expiration configuration is needed anymore for the cache.
* Protocol adapters failed to re-establish receiver links for commands to be routed after loss
  of connection to the AMQP Messaging Network. This has been fixed.

### API Changes

* The `org.eclipse.hono.client.DeviceRegistration` interface's *get* methods have been removed
  because the Device Registration API does not define a corresponding operation.
  Consequently, the C&C functionality of the Kerlink Lora provider which relied on the *get*
  method has been removed.

## 1.2.2

### Fixes & Enhancements

* Commands might not have been routed to the target device or gateway if multiple
  Verticle instances were deployed in a protocol adapter instance. This has been fixed.
* The example registry's admin guide has been amended with some missing configuration
  variables.
* The documentation of the underlying concepts of Hono's Command & Control functionality
  has been updated to reflect recent changes.
* Some dependencies have been updated to more recent versions fixing potential
  vulnerabilities.

## 1.2.1

### Fixes & Enhancements

* The AMQP adapter didn't report any metrics anymore. This has been fixed.
* The CoAP and HTTP adapters' user guide now correctly documents the 413 status code returned
  in response to a request that contains a payload exceeding the configured maximum size.

## 1.2.0

### New Features

* The protocol adapters can now be configured with a direct connection to a data grid
  for storing device connection information.
  This has the advantage of saving the network hop to the Device Connection service.
  Please refer to the protocol adapter Admin Guides for details.
* The Prometheus based resource limits check can now be configured with a trust store, key material
  and/or a username and password in order to be able to connect to a Prometheus server
  that requires TLS and/or client authentication.
* A Java client for the communication with the AMQP protocol adapter has been added.
  It can be used to implement devices, (protocol) gateways and for testing purposes.
  For more information refer to [AMQP Adapter Client for Java]({{% doclink "/dev-guide/amqp_adapter_client/" %}}).
* Devices can now be configured with *groups* of gateways that are allowed to act on behalf of the
  device. This makes it easier to support scenarios in which a device may *roam* between multiple
  gateways. The HTTP based management API has been adapted accordingly.
* The CoAP adapter now supports forwarding commands to devices in the response body of requests for
  uploading telemetry data and/or events, analogous to the HTTP adapter.
  Please note that the CoAP adapter is still considered *experimental* and its device facing API
  is therefore still subject to change.

### Fixes & Enhancements

* When a message arrived, the *message limit* checks failed to calculate the payload size 
  of the incoming message based on the configured *minimum message size*. Instead, it used the
  actual payload size to verify if the *message limit* has been exceeded or not. This has been
  fixed now.
* The Command & Control implementation in the protocol adapters has been optimized to use
  far fewer consumer links to the AMQP Messaging Network, saving up on resources.
  Before this change, the protocol adapters created a separate receiver link for each device that wanted
  to receive commands. Now each protocol adapter instance creates only a single receiver link over
  which all commands for all devices connected to the adapter instance are transmitted.
* The base classes for implementing a device registry have been moved into their own `device-registry-based`
  module.
* The example device registry's AMQP and HTTP endpoints do no longer exchange incoming request messages and outgoing
  response messages with the service implementation verticles over the Vert.x EventBus. This reduces complexity
  and should improve throughput as messages no longer need to be serialized anymore.
* A first version of the CoAP User Guide has been added.
* A concept page explaining the different ways devices can be connected to Hono's protocol adapters has been
  added. There is also example code illustrating how a protocol gateway which connects to the AMQP adapter can
  be implemented.
* If both AMQP and Kafka messaging systems are configured, then Command & Control messages will be received from
  both systems. A command response message from a device will by default be forwarded using the messaging system
  that the corresponding command message was received in.

### API Changes

* The device registry credentials endpoint will no longer hand out sensitive details for hashed-password secrets.
  `pwd-hash`, `salt` and `hash-function` are stored by the device registry but not returned to the user.
  Each secret is given an ID which is now returned, along with the other metadata (time validity and optional fields).
* The `tenant` and `devices` endpoints of the management HTTP API now accept creation requests without a body.
  As there is no mandatory field, having a mandatory body was confusing. 
* The methods of the service base classes `CredentialsManagementService`, `CredentialsService`,
  `DeviceConnectionService`, `DeviceManagementService`, `RegistrationService`, `TenantManagementService` and 
  `TenantService` have been refactored to now return a Vert.x Future instead of taking a Handler as an argument.
* The `CommandConsumerFactory` interface has been renamed to `ProtocolAdapterCommandConsumerFactory` and method
  signatures have been changed slightly. Also, a new `initialize` method has been added to be called on protocol
  adapter startup.
* The AMQP Messaging Network now has to be configured in such a way that protocol adapters can send and
  receive messages on the `command_internal/*` instead of the `control/*` address pattern.
* The Device Registry Management API has been extended to support the definition of *Gateway Groups* which
  can be referenced in a device's *viaGroups* property in order to authorize all gateways that are a member
  of any of the groups to act on behalf of the device.

## 1.1.2

### API Changes

* The `org.eclipse.hono.client.DeviceRegistration` interface's *get* methods have been removed
  because the Device Registration API does not define a corresponding operation.
  Consequently, the C&C functionality of the Kerlink Lora provider which relied on the *get*
  method has been removed.

## 1.1.1

### Fixes & Enhancements

* The *FireFly* LoRa adapter now supports mapping of the *mic* property.
* A bug preventing the lora and SigFox adapters to start up correctly has been fixed.
* The MQTT adapter failed to accept command response messages from authenticated gateways that had
  been published to the `command//${device-id}/res/${req-id}/${status}` topic. This has been fixed
  and the adapter now correctly uses the gateway's tenant in this case.
* When a north bound application sent a command to a device that is connected via a gateway, the
  AMQP adapter set the gateway's ID instead of the device's ID in the command response forwarded
  to the application. This has been fixed.

## 1.1.0

### New Features

* The Sigfox and LoRaWAN adapters now report metrics analogously to the other protocol
  adapters.
* With the release of [Eclipse Californium](https://www.eclipse.org/californium) 2.0.0,
  the CoAP adapter became an official part of the Hono release. The adapter also supports
  tracking of request processing using OpenTracing but is still considered *experimental*.
* A lorawan provider has been added for the *loriot* network provider.
* Hono's protocol adapters now support Server Name Indication in order
  to allow devices to establish a TLS connection with adapters using a tenant specific
  server certificate and host name. Please refer to the *Secure Communication* admin
  guide for details.
* Hono now supports *auto-provisioning* of devices that authenticate with X.509 client certificates. 
  For more information please refer to the [Device Provisioning]({{% doclink "/concepts/device-provisioning/" %}})
  concept and for details to the [Tenant API]({{% doclink "/api/tenant/#trusted-ca-format" %}})
  and the [Credentials API]({{% doclink "/api/credentials/#get-credentials" %}}).
* The Hono Auth Server and Device Registry components now support configuring the SASL
  mechanisms advertised to a client connecting to these components. This can be used to
  restrict the support to only one of the SASL PLAIN and EXTERNAL mechanisms instead of both. 
* A new metric namely *hono.connections.authenticated.duration* has been introduced to track the 
  connection duration of the authenticated devices. Please refer to the 
  [Metrics API]({{% doclink "/api/metrics/" %}}) for more details.
* The protocol adapters that maintain *connection state* can now be configured to verify the *connection 
  duration limit* for each tenant before accepting any new connection request from the devices. Please 
  refer to the [resource-limits]({{% doclink "/concepts/resource-limits/#connection-duration-limit" %}}) 
  section for more details.
* Hono's example device registry now supports configuring a time out for processing requests from clients.
  This is configured using the property `sendTimeOutInMs` in `org.eclipse.hono.config.ServiceConfigProperties`.
* Hono's Helm chart is now available from the
  [Eclipse IoT Packages chart repository](https://www.eclipse.org/packages/repository/).
  All future development of the chart will be done in the IoT Packages project only.
  The deployment guide has been adapted accordingly.

### Fixes & Enhancements

* Hono's Helm chart now supports configuring resource requests and limits of the container images.
* The MQTT adapter now includes a device's MQTT client ID in its request to retrieve the device's
  credentials. This additional information can be used by device registry implementations when determining
  the device identity.
* The domain name of the [Hono Sandbox]({{< relref "/sandbox.md" >}}) has been changed to
  `hono.eclipseprojects.io`.
  It will still be available at the old domain name for some time as well, though.
* Hono's OpenTracing instrumentation has been upgraded to Opentracing 0.33.0.
  The example deployment now uses the Jaeger Java client in version 0.35.2 and the
  Jaeger 1.16 agent and back end components.
* Some of the environment variable names documented in the user guides which can be used for configuring
  Hono components had not been recognized during start up of the components.
  In particular, the names for configuring the connection to the Device Connection service
  and for configuring a heath check server have been fixed.
* The AMQP adapter now correctly accepts command response messages from devices that do not
  contain any payload. Such responses are useful to only convey a status code in reply to
  a command.

### API Changes

* The already deprecated endpoints with the `control` prefix have been removed. The northbound and southbound
  Command & Control endpoints can now only be used with the `command` and `command_response` prefixes
  (or the respective shorthand version). Note that the AMQP Messaging Network still needs to be
  configured in such a way that protocol adapters can send and receive messages on the `control/*`
  address pattern. This is now used for internal communication between protocol adapters only.
* The `create` method in `org.eclipse.hono.client.HonoConnection` now requires its Vertx parameter to be not null.

## 1.0.4

### API Changes

* The `org.eclipse.hono.client.DeviceRegistration` interface's *get* methods have been removed
  because the Device Registration API does not define a corresponding operation.
  Consequently, the C&C functionality of the Kerlink Lora provider which relied on the *get*
  method has been removed.

## 1.0.3

### Fixes & Enhancements

* Hono's Helm Chart now correctly configures Prometheus based resource limit checks if
  a Prometheus server is being used for collecting metrics.
* Some OpenTracing spans used for tracking processing of messages haven't properly been finished.
  This has been fixed.
* Protocol adapters are now able to process messages while still trying to re-establish connections
  to other services after a connection loss. This might result in fewer messages being rejected
  in such situations.

## 1.0.2

### Fixes & Enhancements

* The example deployment now also works with Kubernetes 1.16 using Helm 2.15 and later.
* The Hono chart can now also be deployed using the recently released Helm 3. In fact,
  Helm 3 is now the recommended way for deploying Hono as it doesn't require installation
  of any Helm specific components to the Kubernetes cluster.
* The example data grid which can be deployed using the Hono Helm chart can now be scaled
  out to more than one node.
* Under rare circumstances an HTTP adapter instance might have gotten into a state where
  a device's requests to receive commands could no longer be processed successfully
  anymore until the adapter instance had been restarted. This has been fixed.
* A potential issue has been identified where some command messages might not get sent to
  the corresponding gateway. The scenario here involves the gateway sending event/telemetry
  messages via HTTP with a `hono-ttd` header in order to receive commands, and doing so
  with multiple concurrent requests for *different* devices. To resolve this issue, the 
  corresponding tenant can be configured with a `support-concurrent-gateway-device-command-requests`
  option set to `true` in the `ext` field of an `adapters` entry of type `hono-http`.
  Note that with this option it is not supported for the authenticated gateway to send
  _one_ message with a `hono-ttd` header and no device id to receive commands for *any*
  device that has last sent a telemetry or event message via this gateway.

## 1.0.1

### Fixes & Enhancements

* The AMQP protocol adapter now requests devices to send traffic periodically in order
  to prevent a time out of the connection. This way the adapter is able to detect and
  close stale connections which is important to reliably close the device's corresponding
  command consumer. The time period after which the adapter should consider a connection stale
  can be configured using an environment variable. Please refer to the AMQP adapter's
  admin guide for details.
* The example deployment using the Helm chart correctly creates the `DEFAULT_TENANT` again.

## 1.0.0

### New Features

* A tenant can now be configured with a *max-ttl* which is used as an upper boundary for default
  TTL values configured for devices/tenants. Please refer to the [Tenant API]({{% doclink "/api/tenant#resource-limits-configuration-format" %}})
  for details.
  The AMQP, HTTP, MQTT and Kura protocol adapters consider this property when setting a TTL on
  downstream event messages.
* A protocol adapter can now be configured with a timeout for idle tenants. When there has been no 
  communication between a protocol adapter instance and the devices of a tenant, the former one releases 
  allocated resources of the tenant. Currently this means that it closes AMQP links and stops reporting 
  metrics for this tenant. The timeout is configured with the property `tenantIdleTimeout` for a protocol 
  adapter. Please refer to the protocol adapter [configuration guides]({{% doclink "/admin-guide/" %}})
  for details.
* The accounting period for the *message limit* checks can now be configured as `monthly`.
  In this case the data usage for a tenant is calculated from the beginning till the end of the 
  (Gregorian) calendar month. Refer [resource limits]({{% doclink "/concepts/resource-limits/" %}})
  for more information.
* The devices can now indicate a *time-to-live* duration for event messages published using 
  the HTTP and MQTT adapters by setting the *hono-ttl* property in requests explicitly. Please refer to the
  [HTTP Adapter]({{% doclink "/user-guide/http-adapter/#publish-an-event-authenticated-device" %}})
  and [MQTT Adapter]({{% doclink "/user-guide/mqtt-adapter/#publishing-events" %}}) for details.
* The device registry HTTP management API now properly implements *cross-origin resource sharing (CORS)* support,
  by allowing the service to be exposed to configured domains (by default, it's exposed to all domains).
* The `org.eclipse.hono.util.MessageHelper` now provides convenience factory methods for creating
  new downstream messages from basic properties.
  `org.eclipse.hono.service.AbstractProtocolAdapterBase` has been adapted to delegate to these
  new factory methods.
* Hono's protocol adapters can now use multiple trusted certificate authorities per tenant to authenticate
  devices based on client certificates. The list of trusted certificate authorities can be managed at the
  tenant level using the Device Registry Management API.
* Authenticated gateway devices can now subscribe to commands for specific devices. Before, gateways
  could only subscribe to commands directed at any of the devices that the gateway has acted on behalf of.
  With the new feature of also being able to subscribe to commands for specific devices, northbound
  applications will get notified of such a subscription along with the specific device id.
* Now a *max-ttd* value, which is used as an upper boundary for the *hono-ttd* value specified by the devices,
  can be set as an *extension* property in the adapters section of the tenant configuration.  

### API Changes

* The already deprecated *legacy metrics* support has been removed.
* The already deprecated *legacy device registry* and the corresponding base classes, which had been deprecated 
 as well, have been removed.
* The topic filters used by MQTT devices to subscribe to commands has been changed slightly
  to better fit the addressing scheme used by the other protocol adapters.
  The existing topic filters have been deprecated but are still supported.
  Please refer to the [MQTT adapter user guide]({{% doclink "/user-guide/mqtt-adapter/#command-control" %}})
  for details.
* The interface `ResourceLimitChecks` and its implementation classes have been moved to
  package `org.eclipse.hono.service.resourcelimits` from `org.eclipse.hono.service.plan`.
  Also the configuration parameters for the resource limits were renamed from `hono.plan`
  to `hono.resourceLimits`. Please refer to the protocol adapter [configuration guides]({{% doclink "/admin-guide/" %}})
  for more information.
* The response payload of the *get Tenant* operation of the Tenant API has been changed to contain
  a list of trusted certificate authorities instead of just a single one. This way, protocol
  adapters can now authenticate devices based on client certificates signed by one of multiple
  different trusted root authorities defined for a tenant.
  All standard protocol adapters have been adapted to this change.
  The *Tenant* JSON schema object used in the tenant related resources of the Device Registry Management API
  has also been adapted to contain a list of trusted authorities instead of a single one.

### Deprecations

* The OpenShift specific source-to-image deployment model has been removed in
  favor of the Helm charts and the Eclipse IoT Packages project. You can still
  deploy Hono on OpenShift using the Helm charts.
* Defining password secrets with user provided password hash, function and salt is deprecated in the device registry management
  API and will be removed in the upcoming versions. You should use `pwd-plain` property only going forward.

## 1.0-M7

### New Features

* The Hono Helm chart now supports to choose if the example Device Registry, example AMQP Messaging Network
  and/or the example Jaeger back end should be deployed and used with the Hono components or not.
  In the latter case, the chart now also supports setting configuration properties for using an already
  existing AMQP Messaging Network, Device Registry and/or Jaeger back end.
* A data grid based implementation of the Device Connection API has been added to Hono. This implementation
  can be used in production environments using a highly scalable data grid for storing device connection
  information. The service can be used instead of the simple implementation provided by the example Device
  Registry by means of setting a configuration property when [deploying using the Helm chart]({{% doclink "/deployment/helm-based-deployment/#using-the-device-connection-service" %}}).
* A tenant can now be configured so that *all* OpenTracing spans created when processing messages for that
  specific tenant will be recorded in the tracing backend (overriding the default sampling strategy that
  might only record a certain percentage of traces). See 
  [Monitoring & Tracing]({{% doclink "/admin-guide/monitoring-tracing-config/#enforcing-the-recording-of-traces-for-a-tenant" %}})
  for more details.

### API Changes

* The obsolete method variants of `reportTelemetry` in `org.eclipse.hono.service.metric.Metrics` 
  have been removed. The new variants of this method accept an additional parameter of type `TenantObject`.
* The already deprecated `org.eclipse.hono.service.AbstractProtocolAdapterBase.getRegistrationAssertion`
  method has been removed. The alternate variant of the `getRegistrationAssertion` method which accepts an 
  additional OpenTracing span parameter should be used.
* The already deprecated `getRegistrationAssertion`, `getTenantConfiguration`, `sendConnectedTtdEvent`,
  `sendDisconnectedTtdEvent` and `sendTtdEvent` methods in `org.eclipse.hono.service.AbstractProtocolAdapterBase` 
  have been removed. The alternate variant of these methods which accepts an additional OpenTracing span parameter 
  should be used.  

### Deprecations

* The deprecated Kura adapter is no longer deployed by default by the Helm chart.
  However, it can still be deployed by means of [setting a configuration property]({{% doclink "/deployment/helm-based-deployment/#deploying-optional-adapters" %}}).

## 1.0-M6

### New Features

* Implementation of the new HTTP management API for tenants, devices and
  credentials.
* The health check endpoints of services can now be called securely via TLS. 
  Please refer to the protocol adapter [configuration guides]({{% doclink "/admin-guide/" %}}) 
  for the new parameters available.
* The [Tenant API]({{% doclink "/api/tenant/#resource-limits-configuration-format" %}}) now optionally allows 
  specifying *minimum message size*. If it is specified, then the payload size of the incoming telemetry, event 
  and command messages are calculated in accordance with the *minimum message size* by the AMQP, HTTP and MQTT 
  protocol adapters and then recorded in the metrics system.
  See [Metrics]({{% doclink "/api/metrics/#minimum-message-size" %}}) for more details.

### Fixes & Enhancements

* The automatic reconnect handling of the `HonoConnection` implementation has been
  improved and now applies an exponential back-off algorithm. The behavior can be
  configured using the `${PREFIX}_RECONNECT_*` configuration variables. Please
  refer to the [Hono Client Configuration guide]({{% doclink "/admin-guide/hono-client-configuration/" %}})
  for details regarding these new variables.
* The *message limit* checks is now extended to include command and control messages.
  Please refer to the [resource limits]({{% doclink "/concepts/resource-limits/" %}}) for details.
* The health check server endpoint will now bind to a default port value of 8088 if no values
  are set explicitly in the configuration. It is also possible to start both a secure and an insecure
  server (using different ports)
  Refer to the [Monitoring configuration guide]({{% doclink "/admin-guide/monitoring-tracing-config" %}})
  for details.

### API Changes

* With the implementation of the new HTTP management API for the device registry,
  the class hierarchy for implementing device registry was significantly
  refactored. This also includes the deprecation of a list of classes and tests.
  Also see [Device registry changes](#device-registry-changes) for more information.
* The *complete* interfaces, and the *base* and *complete* implementation
  classes for services got deprecated, and are planned to be removed in a future
  release. Also see [Device registry changes](#device-registry-changes) for
  more information.
* The example Device Registry that comes with Hono now implements the new
  HTTP management API. Consequently, the URI endpoints for managing the content
  of the registry have changed accordingly.
* The configuration parameters for the health check endpoint were moved from 
  `hono.app` to `hono.healthCheck` and renamed. `hono.app.healthCheckPort` is now
  `hono.healthCheck.insecurePort` and `hono.app.healthCheckBindAddress` is now 
  `hono.healthCheck.insecurePortBindAdress`. Please refer to the protocol adapter
  [configuration guides]({{% doclink "/admin-guide/" %}}) for additional
  information on the new naming.

### Device registry changes

The section summarizes changes made for 1.0-M5 in the device registry.

During the development of Hono 1.0, we defined a new HTTP API for managing
information stored in the device registry. This API replaces the current,
provisional API, which was originally intended for tests to manipulate the file
based device registry during system tests. The new HTTP based API is intended
to replace the existing HTTP API, as well as the management part of the AMQP
based API.

The first major change is, that all the *complete* classes got deprecated. As
they are based on the services for the protocol adapters. And those services
are no longer considered to be used for managing information. The replacement
for those classes are the new management APIs.

Each of the three APIs got a companion API for *management*, which are located
in the `org.eclipse.hono.service.management` base package.

The class hierarchy was decoupled in order to make it easier to implement those
services. The new design only requires to implement a service, which is not
based on any other interface. And while your concrete implement still can
implement the `Verticle` interface, this is no longer a requirement.

Also the *base* classes got deprecated. Instead of inheriting the common
functionality, to bind to the *event bus*, that functionality got moved into new
*event bus adapter* classes, which take the reference of a service, and bind
this service to the *event bus* instead of using inheritance. Those change
make it possible to  re-use much of the functionality, but do not impose the
requirement to inherit from either the *complete* or *base* classes. And
this finally allows your service implementation to be extend your specific
base class, and re-use common Hono functionality at the same time. This allows
service implementations to implement both the standard API, as well as the
management API in the same class. As Java does not allow inheritance
from multiple classes, this was not possible before.

The new default file based device registry, was modified to use the new class
hierarchy and support the new management model.

The device registry, which was provided in 1.0-M4 and before, is still part of
the source tree, but was moved to `services/device-registry-legacy`. It is
there to show the compatibility with the older class hierarchy, using the,
now deprecated, *base* and *complete* classes. Newer implementations should not
be build on this model.

Clients of the *base* variants of the services, like the protocol adapters,
do not need to make any changes.

Implementations of the device registry services, using the existing *base* and
*complete* can re-use that model, but are encouraged to migrate to the new model
as soon as possible, as the legacy model is planned to be removed. The only
impacting change is that the service interfaces no longer extend from
`Verticle` directly, but that has been moved to the *base* implementation.

Implementations of the services for protocol adapters (the non-management API),
can switch to the new class hierarchy by dropping inheritance to the *base*
class, and starting up a new instance of the corresponding *event bus adapter*,
providing a reference to the service. For the `RegistrationService` it is also
possible to inherit common functionality from `AbstractRegistrationService`.

Implementations of the services for management need to simply inherit from the
new management interfaces, and set up the *event bus adapters* the same way.

The module `device-registry-legacy` as well as all classes and interfaces,
which got deprecated, are planned to be dropped in 1.1.

## 1.0-M5

### New Features

* Hono's protocol adapters and the other components now support using ECC based server certificates.
  The protocol adapters also support authenticating devices which present an ECC based client certificate.
  The example configuration now uses ECC based certificates by default.
* Hono now specifies a [Device Connection API]({{% doclink "/api/device-connection/" %}}) and
  contains an exemplary implementation of this API included in the device registry component. The purpose of the API is
  to be able to set and retrieve information about the connections from devices or gateways to the protocol adapters.
* This version implements the new HTTP management API for tenants, devices, and credentials.

### Fixes & Enhancements

* The Hono Sandbox's protocol adapters support using the gateway mode again.
* The [Getting Started]({{% doclink "/getting-started/" %}}) guide has been rewritten
  to use the [Hono Sandbox]({{< relref "sandbox" >}}) or a local Minikube cluster
  instead of Docker Swarm.
* The MQTT adapter now closes the network connection to device on publish failures.

### API Changes

* The optional methods of the [Tenant API]({{% doclink "/api/tenant/" %}}) have been
  removed. Implementations of the Tenant API are encouraged to expose the *tenants* endpoint defined by
  [Hono's HTTP based management API]({{% doclink "/api/management" %}}) instead.
  Several of the formerly mandatory to include properties of the request and response messages have
  been made optional or removed altogether. Existing clients should not be affected by these changes, though.
* The optional methods of the 
  [Device Registration API]({{% doclink "/api/device-registration/" %}}) have been 
  removed. Implementations of the Device Registration API are encouraged to expose the *devices* endpoint defined by
  [Hono's HTTP based management API]({{% doclink "/api/management" %}}) instead.
  Several of the formerly mandatory to include properties of the request and response messages have
  been made optional or removed altogether. Existing clients should not be affected by these changes, though.
* The response message format of the *assert Device Registration* operation of the Device Registration API
  has been changed, replacing the optional `gw-supported` boolean field with an optional `via` field.
  The value of this field contains the list of gateway that may act on behalf of the device on which
  the operation is invoked.
* The methods for invoking the optional operations of the Device Registration API have been removed
  from `org.eclipse.hono.client.RegistrationClient` and `org.eclipse.hono.client.impl.RegistrationClientImpl`.
* The optional methods of the [Credentials API]({{% doclink "/api/credentials/" %}}) 
  have been removed. Implementations of the Credentials API are encouraged to expose the *credentials* endpoint defined
  by [Hono's HTTP based management API]({{% doclink "/api/management" %}}) instead.
  Several of the formerly mandatory to include properties of the request and response messages have
  been made optional or removed altogether. Existing clients should not be affected by these changes, though.
* The `control` prefix in the northbound and southbound Command & Control endpoints has been renamed to `command`. 
  The endpoint names with the `control` prefix are still supported but deprecated. The northbound endpoint for
  *business applications* to receive command responses has the `command_response` prefix now. The old `control` prefix
  for the receiver address is also still supported but deprecated.
* The `deviceId` parameter of the `getOrCreateCommandClient` and `getOrCreateAsyncCommandClient` methods of the 
  `org.eclipse.hono.client.ApplicationClientFactory` interface has been removed.
  This means that a `CommandClient` or `AsyncCommandClient` instance can be used to send commands to arbitrary
  devices of a tenant now. Accordingly, the `CommandClient.sendCommand` and `AsyncCommandClient.sendAsyncCommand`
  methods now require an additional `deviceId` parameter.
* The deprecated methods of `org.eclipse.hono.client.HonoConnection` have been removed.

## 1.0-M4

### New Features

* Default properties can now also be set at the tenant level, affecting all devices
  belonging to the tenant. Please refer to the 
  [protocol adapter user guides]({{% doclink "/user-guide/" %}}) for details.
* `CredentialsClientImpl` now supports caching of response data received from a Credentials service based on 
  *cache directives*. The protocol adapters are now equipped to cache the response from the Credentials Service.
  The protocol adapters support configuration variables to set the default cache timeout, the minimum 
  and maximum cache sizes for this service.
* The example device registry's Credentials service implementation now includes a *cache directive*
  in its response to the *get Credentials* operation which allows clients to cache credentials of
  type *hashed-password* and *x509-cert* for a configurable amount of time. Please refer to the
  [Device Registry Admin Guide]({{% doclink "/admin-guide/device-registry-config/" %}}) 
  for details regarding the configuration properties to use.
* There is now an official specification of an HTTP API for managing the content of a device registry.
  The [HTTP Management API]({{% doclink "/api/management" %}}) is defined using by 
  means of OpenAPI v3. Note, that the API is not yet implemented by the example device registry that comes with Hono.
* The Command & Control feature now supports gateway agnostic addressing of devices. This means that applications are
  able to send commands to devices without knowing the particular gateway they may be connected to.
* The concept and implementation of *message limit* have been added. The protocol adapters can be now
  enabled to verify this *message limit* for each tenant before accepting any telemetry/event messages.
  Please refer to the [resource limits]({{% doclink "/concepts/resource-limits/" %}}) for details.
* A basic Sigfox protocol adapter, for use with the Sigfox backend. Please read
  the [Sigfox protocol adapter]({{% doclink "/user-guide/sigfox-adapter/" %}})
  documentation to learn more about pre-requisites and limitations.

### Fixes & Enhancements

* vert.x has been updated to version 3.7.0.

### API Changes

* The `org.eclipse.hono.util.RegistrationConstants.FIELD_DEFAULTS` constant
  has been renamed to `org.eclipse.hono.util.RegistrationConstants.FIELD_PAYLOAD_DEFAULTS`.
* The `org.eclipse.hono.service.AbstractProtocolAdapterBase.newMessage` and
  `org.eclipse.hono.service.AbstractProtocolAdapterBase.addProperties` methods have
  been changed to accept an additional parameter of type `TenantObject` which may contain default
  properties defined for the tenant to be included in downstream messages.
* The *get Registration Information* operation of the Device Registration API is not optional anymore, 
  it is now mandatory to implement. For device registry implementations based on the
  `CompleteRegistrationService` interface, there is no change needed as the operation is already
  defined there.
* The response message of the *assert Device Registration* operation does not contain an assertion
  token anymore. The `org.eclipse.hono.service.registration.BaseRegistrationService` class
  has been adapted accordingly.
* The already deprecated `org.eclipse.hono.client.CommandConsumerFactory.closeCommandConsumer`
  method has been removed.
* The response message format of the *assert Device Registration* operation of the Device Registration API
  has been changed to include an optional `gw-supported` boolean field. The value of this field refers to 
  whether the device on which the operation is invoked allows one or more gateways to act on its behalf.
* The AMQP sender link address to be used by *business applications* to send commands to devices has been 
  changed from `control/${tenant_id}/${device_id}` to `control/${tenant_id}` with command messages requiring 
  the `to` property to be set to `control/${tenant_id}/${device_id}`. Using `control/${tenant_id}/${device_id}`
  as sender link address is still possible but gateway agnostic addressing of devices is not supported for 
  such command messages.

### Deprecations

* Instructions for script based deployment to Kubernetes have been removed from the deployment guide.
  Using Helm is now the only supported way of deploying Hono to Kubernetes.

## 1.0-M3

### Fixes & Enhancements

* The protocol adapters add tracing information about invocations of the Device Registry
  services again.
* The example deployment now uses Qpid Dispatch Router 1.6.0.

## 1.0-M2

### New Features

* A new *experimental* LoRa protocol adapter has been added which (so far) supports the reception
  of telemetry data and events from devices connected to LoRa network providers and/or
  LoRa gateways. Note that this adapter is *not* considered production ready yet.
  Any help in improving and enhancing the adapter is more than welcome.
* The concept and implementation of *resource limits* have been added. Now a connection limit to define 
  the maximum number of device connections to be allowed per tenant can be configured. The MQTT and AMQP 
  adapters can be enabled to verify this connection limit before accepting any new connections. Please 
  refer to the [resource limits]({{% doclink "/concepts/resource-limits/" %}}) for details.
 
### Fixes & Enhancements

* The base classes for implementing the AMQP and HTTP endpoints for the Credentials, Tenant
  and Device Registration APIs now create an OpenTracing Span for tracking the
  processing of requests at a high level.
* The `hono-client` and `hono-core` artifacts use Java 8 level again so that they
  can be used in applications using Java 8.
* The protocol adapters now always specify values for the *ttd* and *qos* tags when
  reporting telemetry messages using meter name *hono.messages.received*. This fixes
  an issue when using the Prometheus back end where the HTTP adapter failed to report
  messages that contained a TTD value and others that didn't.
* The Helm based deployment of the device registry has been fixed by adding the secret
  and deployment entries for the `device-identities.json`.
* Before uploading command responses, the MQTT and AMQP adapters now check whether the device is 
  registered and also the adapter is enabled for the tenant.

### API Changes

* The `hono-client` module has undergone several major and incompatible changes. The most
  important change affects the `HonoClient` interface which no longer serves as a factory
  for the arbitrary clients to the Hono service endpoints.
  It has been renamed to `HonoConnection` and now only represents the underlying
  AMQP connection to a peer and provides methods for managing the connection state
  and registering listeners for arbitrary life-cycle events of the connection.
  In addition to this, several factory interfaces have been added which can be used
  to create specific clients to Hono's arbitrary services. All of the former `HonoClient`
  interface's factory methods have been distributed accordingly to:
  * `org.eclipse.hono.client.ApplicationClientFactory` for creating clients to
    Hono's north bound Telemetry, Event and Command &amp; Control API.
  * `org.eclipse.hono.client.DownstreamSenderFactory` for creating clients to
    Hono's south bound Telemetry and Event APIs.
  * `org.eclipse.hono.client.CommandConsumerFactory` for creating clients to
    Hono's south bound Command &amp; Control API.
  * `org.eclipse.hono.client.TenantClientFactory` for creating clients to
    Hono's Tenant API.
  * `org.eclipse.hono.client.RegistrationClientFactory` for creating clients to
    Hono's Device Registration API.
  * `org.eclipse.hono.client.CredentialsClientFactory` for creating clients to
    Hono's Credentials API.
* In this context the `org.eclipse.hono.client.MessageSender` interface has been changed as follows:
  * The *send* methods have been changed to no longer accept a *registration assertion token*
    which became obsolete with the removal of the *Hono Messaging* component.
  * The *isRegistrationAssertionRequired* method has been removed from the interface.
  * All *send* method variants which accept specific message parameters have been moved into
    the new `org.eclipse.hono.client.DownstreamSender` interface which extends the existing
    `MessageSender`.
* Several changes have been made to the `org.eclipse.hono.service.AbstractProtocolAdapterBase`
  class:
  * The *newMessage* and *addProperties* methods no longer require a boolean parameter indicating
    whether to include the assertion token in the message being created/amended.
    Custom protocol adapters should simply omit the corresponding parameter.
  * The base class now uses `org.eclipse.hono.client.CommandConsumerFactory` instead of
    `org.eclipse.hono.client.CommandConnection` for creating
    `org.eclipse.hono.client.CommandConsumer` instances.
    The *setCommandConnection* and *getCommandConnection* methods have been
    renamed to *setCommandConsumerFactory* and *getCommandConsumerFactory*
    correspondingly.
  * The base class now uses `org.eclipse.hono.client.TenantClientFactory` instead of
    `org.eclipse.hono.client.HonoClient` for creating `org.eclipse.hono.client.TenantClient`
    instances.
    The *setTenantServiceClient* and *getTenantServiceClient* methods have been
    renamed to *setTenantClientFactory* and *getTenantClientFactory* correspondingly.
  * The base class now uses `org.eclipse.hono.client.RegistrationClientFactory` instead of
    `org.eclipse.hono.client.HonoClient` for creating
    `org.eclipse.hono.client.RegistrationClient` instances.
    The *setRegistrationServiceClient* and *getRegistrationServiceClient* methods have been
    renamed to *setRegistrationClientFactory* and *getRegistrationClientFactory* correspondingly.
  * The base class now uses `org.eclipse.hono.client.CredentialsClientFactory` instead of
    `org.eclipse.hono.client.HonoClient` for creating
    `org.eclipse.hono.client.CredentialsClient` instances.
    The *setCredentialsServiceClient* and *getCredentialsServiceClient* methods have been
    renamed to *setCredentialsClientFactory* and *getCredentialsClientFactory* correspondingly.
  * The base class now uses `org.eclipse.hono.client.DownstreamSendertFactory` instead of
    `org.eclipse.hono.client.HonoClient` for creating
    `org.eclipse.hono.client.DownstreamSender` instances.
    The *setHonoMessagingClient* and *getHonoMessagingClient* methods have been
    renamed to *setDownstreamSenderFactory* and *getDownstreamSenderFactory* correspondingly.
* The `org.eclipse.hono.service.auth.device.UsernamePasswordAuthProvider` and the
  `org.eclipse.hono.service.auth.device.X509AuthProvider` now accept a
  `org.eclipse.hono.client.CredentialsClientFactory` instead of a
  `org.eclipse.hono.client.HonoClient` in their constructors.
* The `org.eclipse.hono.adapter.http.HonoBasicAuthHandler` and
  `org.eclipse.hono.adapter.http.X509AuthHandler` classes have been moved to
  package `org.eclipse.hono.service.http` in the *service-base* module for
  consistency reasons as all other reusable classes for implementing HTTP services/
  adapters are located in that package already.
* The `org.eclipse.hono.client.HonoClient` class has been renamed to
  `org.eclipse.hono.client.HonoConnection` to better reflect its sole responsibility
  for establishing (and maintaining) the connection to a Hono service endpoint.

### Deprecations

* The optional operations defined by the Tenant, Device Registration and Credentials API
  have been deprecated. They will be removed from Hono 1.0 altogether.
  A new HTTP based API will be defined instead which can then be used to *manage* the content
  of a device registry.
* `org.eclipse.hono.client.HonoConnection`'s *connect* method variants accepting
  a disconnect handler have been deprecated and will be removed in Hono 1.0.
  Client code should use one of the other *connect* methods instead and register a
  `org.eclipse.hono.client.DisconnectListener` and/or a
  `org.eclipse.hono.client.ReconnectListener` to get notified about life-cycle
  events of the underlying AMQP connection.

## 1.0-M1

### New Features

* The AMQP adapter now supports limiting the number of concurrent connections in order
  to prevent Out of Memory errors. Please refer to
  [AMQP Adapter Configuration]({{% doclink "/admin-guide/amqp-adapter-config/" %}}) for details.
* The `org.eclipse.hono.client.AsyncCommandClient` has been added to support the sending of
  commands to devices and the receiving of responses in an asynchronous way. This can be used
  to decouple the sender and receiver from each other. 
* The `org.eclipse.hono.service.tenant.CompleteBaseTenantService` class now rejects malformed
  encodings of public keys/certificates included in a request to add a trust anchor to a tenant.

### Deprecations

* The `HonoClient.closeCommandConsumer()` method will be removed in Hono 1.0.
  The `CommandConsumer.close()` method should be used instead.

## 0.9

### New Features

* The MQTT adapter now supports commands to be published using QoS 1. Please refer to
  [MQTT adapter User Guide]({{% doclink "/user-guide/mqtt-adapter/" %}}) for details.
* The MQTT adapter now supports limiting the number of concurrent connections in order
  to prevent running out of resources. Please refer to
  [MQTT Adapter Configuration]({{% doclink "/admin-guide/mqtt-adapter-config/" %}}) for details.
* The new *Helm deployment* for Kubernetes has been added. Please refer to
  [Helm based deployment guide]({{% doclink "/deployment/helm-based-deployment/" %}}) for details.

### Fixes & Enhancements

* `org.eclipse.hono.util.RequestResponseResult` now provides access to AMQP application-properties conveyed in the
  response message.
* The `org.eclipse.hono.service.registration.BaseRegistrationService` class now supports authorization of gateways
  (acting on behalf of a device) against a list of gateway identifiers instead of a single identifier only. For that purpose
  the `via` property of the device's registration information may contain either a single string or a JSON array containing
  multiple strings. Based on this, a device can now be configured to connect via arbitrary gateways instead of just a single
  one.

### API Changes

* The layout and structure of the metrics reported by Hono have been changed substantially. Many of the existing meters and tags
  have been changed or replaced in order to provide a more consistent set of metrics and increase the value of the information
  being reported. The legacy metrics still remain unchanged, though.
  Please refer to the [Metrics definition]({{% doclink "/api/metrics/" %}}) for details.
* In case of a failed connection attempt, `HonoClientImpl` will now determine based on the error whether it will re-try
  to connect to the peer. Before, reconnect attempts were done unconditionally, by default infinitely or up to the
  number of times defined in the *reconnectAttempts* property in the `ClientConfigProperties`. Now, when the outcome
  of the SASL handshake received from the peer during connection establishment indicates that invalid credentials were
  provided or that the server encountered a permanent error when handling the request, no further reconnect attempts
  will be done.
* The deprecated methods have been removed from `org.eclipse.hono.client.MessageSender` and
  its implementations.
* The Command &amp; Control API's *send a request/response command* operation has been changed. The response
  message to a command now must include the device and tenant identifiers of the device. Including
  these two properties should make it much easier to implement competing command response consumers
  in business applications.
  As a consequence, the `org.eclipse.hono.client.CommandResponse`'s factory methods have been changed to
  accept the tenant and device IDs as parameters.
* The *connectTimeout* configuration variable for the `HonoClient` now defines the time to wait not only for the TCP/TLS
  connection establishment but also for the SASL handshake and the exchange of the AMQP *open* frame.
* The (already deprecated) Hono Messaging component has been removed from Hono.

### Deprecations

* The script based deployment to Kubernetes has been deprecated and will be removed in the next version.
  The Helm based deployment should be used instead.
* The *sendCommandResponse(String, String, Buffer, Map, int, SpanContext)* of the
  `org.eclipse.hono.client.CommandResponseSender` interface has been deprecated and
  will be removed in the next version. Custom protocol adapters should use
  *sendCommandResponse(CommandResponse, SpanContext)* instead.

## 0.9-M2

### New Features

* The MQTT protocol adapter now supports authentication of devices using X.509 client certificates. Please refer to
  the [MQTT adapter user guide]({{% doclink "/user-guide/mqtt-adapter/" %}}) for details regarding configuration.

### Fixes & Enhancements

* The OpenShift *source-to-image* (S2I) deployment is now the default
  OpenShift / OKD deployment. The plain OpenShift deployment, which had been deprecated
  in Hono 0.8, has been removed.
* The protocol adapters can now be configured with a custom *DNS timeout* value, limiting the time that the adapter
  will wait for the response to a DNS query. By default, a DNS query will time out after 5 seconds.
  Please refer to the protocol adapter admin guides for details regarding the new configuration variable.
* The following configuration variables have been added to `HonoClient`:

  * *connectTimeout*: Sets a limit on the time that the client will wait for a TCP/TLS connection with the peer
    to be established. By default, a connection attempt will time out after 5 seconds.
  * *idleTimeout*: The idle timeout defines the amount of time after which a connection will be closed when no frames
    have been received from the remote peer. The default value is 16 seconds.
  * *sendMessageTimeout*: Limits the time to wait for a downstream consumer's acknowledgement of
    an event or command response message received from a device. The default value is 1 second.

    Please refer to the [Hono Client Configuration guide]({{% doclink "/admin-guide/hono-client-configuration/" %}})
    for details regarding the new configuration variables.

### API Changes

* Some of the *tags* used by Hono's components when reporting metrics have been changed. The common tag *component*
  has been renamed to *component-type*. The *protocol* tag formerly used by adapters to indicate the transport protocol
  that a message has been received over, has been replaced by the generic *component-name* tag which indicates the name
  of the component that a metric has been reported by. Please refer to the [Metrics API]({{% doclink "/api/metrics/" %}})
  for details. Note that these changes do not affect the legacy Graphite based metrics back end.

### Deprecations

* The Hono Messaging component is now  deprecated and will be removed from Hono in version 0.9 altogether.
  The example deployment has not been using Hono Messaging since 0.6 and there is no practical reason for
  using it anymore.

## 0.9-M1

### New Features

* The default Micrometer back end is now Prometheus, the Grafana dash boards have been updated
  to retrieve data from Prometheus instead of the old InfluxDB.
  The Graphite based legacy metrics format can still be used but requires building Hono from source and activating
  the `metrics-graphite` Maven build profile.
  Please refer to the [Monitoring admin guide]({{% doclink "/admin-guide/monitoring-tracing-config/" %}}) for details.
* The `org.eclipse.hono.service.credentials.CompleteBaseCredentialsService` class now supports the transparent
  *on-the-fly* hashing of clear text passwords contained in *hashed-password* credentials. Please refer to the
  [Device Registry user guide]({{% doclink "/user-guide/device-registry/#managing-credentials" %}}) for details.

### Fixes & Enhancements

* The base classes for implementing the Device Registration and Tenant APIs have been instrumented
  with OpenTracing. New variants of the `RegistrationService.assertRegistration`, `TenantService.get` and `CredentialsService.get`
  methods have been added which also accept an OpenTracing span as a parameter.
  The default implementations of these methods still default to the previously existing methods.
  In `RegistrationService` implementations based on `BaseRegistrationService` an OpenTracing span will be created,
  passed on to the `assertRegistration` method and finished eventually. The same applies to `TenantService` implementations
  based on `BaseTenantService` concerning the `get` method and to `CredentialsService` implementations based on
  `BaseCredentialsService` concerning the `get` method.

### API Changes

* The `org.eclipse.hono.service.credentials.CompleteBaseCredentialsService` class now requires an
  `org.eclipse.hono.auth.HonoPasswordEncoder` to be passed into its constructor.
  The `org.eclipse.hono.auth.SpringBasedHonoPasswordEncoder` has been added as a default implementation for
  this purpose.
* The [Tenant API]({{% doclink "/api/tenant/#trusted-ca-format" %}}) now optionally allows specifying an
  X.509 certificate instead of a public key when defining a trusted CA.


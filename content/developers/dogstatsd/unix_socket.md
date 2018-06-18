---
title: DogStatsD over Unix Domain Socket
kind: documentation
description: Usage documentation for DogStatsD over Unix Domain Sockets.
further_reading:
- link: "developers/dogstatsd"
  tag: "Documentation"
  text: "Introduction to DogStatsD"
- link: "developers/libraries"
  tag: "Documentation"
  text: "Official and Community-contributed API and DogStatsD client libraries"
- link: "https://github.com/DataDog/datadog-agent/tree/master/pkg/dogstatsd"
  tag: "Github"
  text: "DogStatsD source code"
---

Starting with version 6.0, the Datadog Agent is able to ingest metrics via a Unix Domain Socket (UDS), as an alternative to UDP, when running on Linux systems.

While UDP works great on `localhost`, it can be a challenge to setup in containerized environments. Unix Domain Sockets allow to easily establish the connection via a socket file, regardless of the IP of the Datadog Agent container. It also enables the following benefits:

- bypassing the networking stack brings a significant performance improvement for high traffic
- while UDP has no error handling, UDS allows to detect dropped packets and connection errors, while still allowing a non-blocking use
- DogStatsD is be able to detect the origin container of metrics and tag them accordingly

## How it works

Instead of using an `IP:port` pair to establish connections, Unix Domain Sockets use a placeholder socket file. Once the connection is open, data is transmitted in the same [datagram format][2] as UDP.

When the agent restarts, the existing socket is deleted and replaced by a new one. Client libraries detect this change and connect seamlessly to the new one.

**Note:** by design, UDS traffic is local to the host, which means the Datadog Agent must run on every host you will be sending metrics from. 

## Setup

### Agent

Edit your `datadog.yaml` file to set the `dogstatsd_socket` option to the path where DogStatsD should create its listening socket:

```
dogstatsd_socket: /var/run/datadog/dsd.socket
```

Then [restart your Agent][1]. You can also set the socket path via the `DD_DOGSTATSD_SOCKET` environment variable.

### Client

The following DogStatsD client libraries support UDS traffic:

- Golang: [DataDog/datadog-go][3]
- Java: [DataDog/java-dogstatsd-client][4]
- Python: [DataDog/datadogpy][5]
- Ruby: [DataDog/dogstatsd-ruby][6]

Refer to the library's documentation on how to enable UDS traffic.

**Note:** as with UDP, enabling client-side buffering is highly recommended to improve performance on heavy traffic. Refer to your client library's documentation for instructions.

### Accessing the socket across containers

When running in a containerized environment, the socket file needs to be accessible to the client containers. To achieve this, we recommend mounting a host directory on both sides (read-only in your client containers, read-write in the Agent container).

Mounting the parent folder instead of the individual socket enables socket communication to persist across DogStatsD restarts.

#### Docker: bind mount

- Start the Agent container with `-v /var/run/datadog:/var/run/datadog`
- Start your containers with `-v /var/run/datadog:/var/run/datadog:ro`

#### Kubernetes: `hostPath` volume

Mount the folder in your `datadog-agent` container:

```
volumeMounts:
  - name: dsdsocket
    mountPath: /var/run/datadog
...
volumes:
- hostPath:
    path: /var/run/datadog/
  name: dsdsocket
```

Expose the same folder in your client containers:

```
volumeMounts:
  - name: dsdsocket
    mountPath: /var/run/datadog
    readOnly: true
...
volumes:
- hostPath:
    path: /var/run/datadog/    
  name: dsdsocket
```

## Using Origin Detection for container tagging

Origin Detection allows DogStatsD to detect the container metrics come from, and tag metrics automatically. When this mode is enabled, all metrics received via UDS will be tagged by the same container tags as Autodiscovery metrics. **Note:** `container_id`, `container_name` and `pod_name` tags will not be added, to avoid creating too many custom metric contexts.

To enable this, enable the `dogstatsd_origin_detection` option in your `datadog.yaml`, or set the environment variable `DD_DOGSTATSD_ORIGIN_DETECTION=true`, and [restart your Agent][1].

When running inside a container, DogStatsd needs to run in the host PID namespace for origin detection to work reliably. You can enable this via the docker `--pid=host` flag.

## Client library implementation guidelines

Adding UDS support to existing libraries is usually pretty simple as the protocol is very close to UDP. Implementation guidelines and a testing checklist are available in the [datadog-agent wiki](https://github.com/DataDog/datadog-agent/wiki/Unix-Domain-Sockets-support)

## Further reading

{{< partial name="whats-next/whats-next.html" >}}

[1]: /agent/faq/agent-commands
[2]: /developers/dogstatsd/data_types
[3]: https://github.com/DataDog/datadog-go
[4]: https://github.com/DataDog/java-dogstatsd-client
[5]: https://github.com/DataDog/datadogpy
[6]: https://github.com/DataDog/dogstatsd-ruby


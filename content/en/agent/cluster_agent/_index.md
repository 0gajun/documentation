---
title: Datadog Cluster Agent for Kubernetes
kind: documentation
aliases:
- /agent/kubernetes/cluster/
further_reading:
- link: "https://www.datadoghq.com/blog/datadog-cluster-agent/"
  tag: "Blog"
  text: "Introducing the Datadog Cluster Agent"
- link: "https://www.datadoghq.com/blog/autoscale-kubernetes-datadog/"
  tag: "Blog"
  text: "Autoscale your Kubernetes workloads with any Datadog metric"
- link: "/agent/autodiscovery/clusterchecks"
  tag: "Documentation"
  text: "Running Cluster Checks with Autodiscovery"
- link: "agent/kubernetes/daemonset_setup"
  tag: "Documentation"
  text: "Kubernetes DaemonSet Setup"
- link: "agent/kubernetes/integrations"
  tag: "Documentation"
  text: "Custom Integrations"
- link: "https://github.com/DataDog/datadog-agent/blob/master/docs/cluster-agent/GETTING_STARTED.md#troubleshooting"
  tag: "Github"
  text: "Troubleshooting the Datadog Cluster Agent"
---

## Overview

The Datadog Cluster Agent provides a streamlined, centralized approach to collecting cluster-level monitoring data. By acting as a proxy between the API server and node-based Agents, the Cluster Agent helps to alleviate server load. It also relays cluster-level metadata to node-based Agents, allowing them to enrich the metadata of locally collected metrics.

Using the Datadog Cluster Agent allows you to:

* Alleviate the impact of Agents on your infrastructure.
* Isolate node-based Agents to their respective nodes, reducing RBAC rules to solely read metrics and metadata from the kubelet.
* Provide cluster level metadata that can only be found in the API server to the Node Agents for them to enrich the metadata of the locally collected metrics.
* Enable the collection of cluster level data such as the monitoring of services or SPOF and events.
* Leverage horizontal pod autoscaling with custom Kubernetes metrics. Refer to [the dedicated guide][1] for more details about this feature.

## Limitations

The Datadog Cluster Agent implements a Go HTTP server (from `http/net`) to expose its API. This implementations is [largely sufficient][2] as the Datadog Cluster Agent should only be receiving calls from up to 5K nodes that are made every minute by default. Load testing the Datadog Cluster Agent, there were no problems handling 200 rq/s for an extended period of time.

**Datadog recommends running 3 replicas of the Datadog Cluster Agent for infrastructures beyond a thousand nodes with the Agent.**

**Note**: To leverage all features from the Datadog Cluster Agent, you must run Kubernetes v1.10+.

## Further Reading

{{< partial name="whats-next/whats-next.html" >}}

[1]: https://github.com/DataDog/datadog-agent/blob/master/docs/cluster-agent/CUSTOM_METRICS_SERVER.md
[2]: https://github.com/valyala/fasthttp#http-server-performance-comparison-with-nethttp

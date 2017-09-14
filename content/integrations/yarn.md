---
title: Datadog-Hadoop YARN Integration
integration_title: Hadoop YARN
kind: integration
git_integration_title: yarn
newhlevel: true
description: "{{< get-desc-from-git >}}"
---
{{< img src="integrations/yarn/yarndashboard.png" alt="Hadoop Yarn" >}}

## Overview

Capture Yarn metrics to:

* Visualize cluster health, performance, and utilization.
* Analyze and inspect individual application performance.

## Setup
### Configuration

*Install Datadog Agent on the ResourceManager*

1.  Configure the agent to connect to the ResourceManager: `Edit conf.d/yarn.yaml`:
{{< highlight yaml>}}
init_config:

instances:
    -   resourcemanager_address: localhost
        resourcemanager_port: 8088
{{< /highlight >}}

2.  Restart the Agent

{{< insert-example-links conf="yarn" check="yarn" >}}

### Validation

Execute the info command and verify that the integration check has passed. The output of the command should contain a section similar to the following:
{{< highlight shell>}}
Checks
======

  [...]

  yarn
  ----
      - instance #0 [OK]
      - Collected 8 metrics & 0 events
{{< /highlight >}}

## Data Collected
### Metrics

{{< get-metrics-from-git >}}



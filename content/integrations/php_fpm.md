---
title: Datadog-PHP-FPM Integration
integration_title: PHP-FPM
kind: integration
doclevel: basic
newhlevel: true
git_integration_title: php_fpm
alias:
  - integrations/phpfpm
description: "{{< get-desc-from-git >}}"
---

{{< img src="integrations/phpfpm/phpfpmoverview.png" style="max-width: 600px;" alt="PHP overview" >}}

## Overview

Enable the PHP-FPM check to monitor the state of your FPM pool and track requests performance.

## Setup
### Installation

No installation steps are required for this integration.

### Configuration

1.  Configure the Agent to connect to your FPM status endpoint (look at your pools definition). Edit `conf.d/php_fpm.yaml`:
{{< highlight yaml>}}
init_config:

instances:
  - status_url: http://localhost/status
    ping_url: http://localhost/ping
    ping_reply: pong
    tags:
      - instance:foo
{{< /highlight >}}

2.  Restart the Agent

#### Configuration Options

* `status_url` (Required) - URL for the PHP FPM status page defined in the fpm pool config file (pm.status_path)
* `ping_url` (Required) - URL for the PHP FPM ping page defined in the fpm pool config file (ping.path)
* `ping_reply` (Required) - Reply from the ping_url. Unless you define a reply, it is `pong`
* `user` (Optional) - Used if you have set basic authentication on the status and ping pages
* `password` (Optional) - Used if you have set basic authentication on the status and ping pages
* `http_host` (Optional) - If your FPM pool is only accessible via a specific HTTP vhost, specify it here

### Validation

To validate your installation and configuration, restart the agent and execute the info command. The output should contain a section similar to the following:
{{< highlight shell>}}
Checks
======

  [...]

  php_fpm
  -------
      - instance #0 [OK]
      - Collected 7 metrics & 0 events & 2 service checks
{{< /highlight >}}

## Data Collected
### Metrics
| Metric name | Definition |
|---|----|
| **php_fpm.listen_queue.size**<br/>(gauge)| Size of the socket queue of pending connections|
| **php_fpm.processes.active**<br/>(gauge)| Total number of active processes|
| **php_fpm.processes.idle**<br/>(gauge)| Total number of idle processes |
| **php_fpm.processes.total**<br/>(gauge)|Total number of processes|

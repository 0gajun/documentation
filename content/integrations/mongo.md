---
title: Datadog-MongoDB Integration
integration_title: MongoDB
kind: integration
git_integration_title: mongo
newhlevel: true
aliases:
  - /integrations/mongodb
description: "{{< get-desc-from-git >}}"
---
## Overview

Connect MongoDB to Datadog in order to:

* Visualize key MongoDB metrics.
* Correlate MongoDB performance with the rest of your applications.

## Setup
### Installation

1.  To capture MongoDB metrics you need to install the Datadog Agent.
2.  Create a read-only user for Datadog, with the appropriate [MongoDB roles](https://docs.mongodb.com/manual/reference/built-in-roles/) to collect complete server statistics. In the mongo shell, run:
{{< highlight shell>}}
# Authenticate as the admin user.
use admin
db.auth("admin", "admin-password")

# On MongoDB 2.x, use the addUser command.
db.addUser("datadog", "<UNIQUEPASSWORD>", true)

# On MongoDB 3.x or higher, use the createUser command.
db.createUser({
"user":"datadog",
  "pwd": "<UNIQUEPASSWORD>",
  "roles" : [
    {role: 'read', db: 'admin' },
    {role: 'clusterMonitor', db: 'admin'},
    {role: 'read', db: 'local' }
  ]
})
{{< /highlight >}}

### Configuration

1.  Edit your `conf.d/mongo.yaml` file as follows:
{{< highlight yaml>}}
init_config:

instances:
  # The format for the server entry below is:
  # server: mongodb://username:password@host:port/database where database will default to admin
  - server: mongodb://admin:datadog@localhost:27017/admin
    tags:
      - mytag1
      - mytag2
    additional_metrics:
      - durability
      - locks
      - top
{{< /highlight >}}

2.  Restart the agent

{{< insert-example-links conf="mongo" check="mongo" >}}

### Validation

To validate that the integration is working, run ```/etc/init.d/datadog-agent info```. You should see results similar to the following:

{{< highlight shell >}}
Checks
======

  mongo
  -----
    - instance #0 [OK]
    - Collected 89 metrics, 0 events & 2 service checks
    - Dependencies:
        - pymongo: 2.8
{{< /highlight>}}

## Data Collected
### Metrics

{{< get-metrics-from-git "mongo" >}}

<div class="alert alert-info">
Many of these metrics are described in the <a href="https://docs.mongodb.org/manual/reference/command/dbStats/">MongoDB Manual 3.0</a>
</div>

[1]: https://github.com/DataDog/integrations-core/blob/master/mongo/conf.yaml.example
[2]: https://github.com/DataDog/integrations-core/blob/master/mongo/check.py

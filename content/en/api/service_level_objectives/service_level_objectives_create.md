---
title: Create a service level objective
type: apicontent
order: 29.01
external_redirect: /api/#create-a-service-level-objective
---

## Create a service level objective

If you manage and deploy SLOs programmatically, it's easier to define the SLO in the Datadog UI and [export its valid JSON][1].

**ARGUMENTS**:

* **`type`** [*required*]:
    The [type of the SLO][2], chosen from:

| SLO Type     | type attribute value             |
|:-------------|:---------------------------------|
| metric       | `metric`                         |
| monitor      | `monitor`                        |

* **`name`** [*required*, *default* = **dynamic, based on query**]:
    The name of the SLO.
* **`description`** [*optional*, *default* = **empty**]:
    A description of the SLO.
* **`tags`** [*optional*, *default* = **empty list**]:
    A list of tags to associate with your SLO.
* **`thresholds`** [*required*]:
    A list of Target thresholds, requires at least 1.
    
    * **`timeframe`** [*required*]: 
        The timeframe to apply to the target value. Valid options are `7d`, `30d`, `90d`.
    * **`target`** [*required*]:
        The target value to associate with the SLI that defines the SLO.
    * **`target_displauy`** [*optional*, *default* = **dynamic, based on query**]:
        The target display value that includes the requires level of precision.
    * **`warning`** [*optional*, *default* = **none**]:
        A warning target value to indicate when the SLI is close to breaching the `target`.
    * **`warning_display`** [*optional*, *default* = **dynamic, based on query**]:
        A warning target display value that includes the requires level of precision.

##### Monitor Based SLO

* **`monitor_ids`** [*required*, *default* = **empty list**]:
    Specify up to 20 monitor IDs directly for a monitor-based SLO. You can optionally on-create-dynamically select
    monitor IDs using the following option instead:
* **`monitor_search`** [*optional*]:
    Optional way to specify monitor IDs on create is to use a monitor search. On first create or edit, it will dynamically
    search for the provided parameters and select up to the first 50 monitors matching the query.
* **`groups`** [*optional*, *default* = **empty list**]:
    **Note: Only valid on single monitor SLOs** Specify the selected groups as a sub query of the selected monitor.

##### Metric Based SLO    
* **`query`** [*required*]:
    The query defines the metric-based SLO query. It requires two arguments:
    
    * **`numerator`** [*required*]:
        Defines the sum of the `good` events
    * **`denominator`** [*required*]:
        Defines the sum of the `total` events. **Note: this should always be >= `good` events**


[1]: /service_level_objectives/#export-your-service-level-objective
[2]: /service_level_objectives/slo_types

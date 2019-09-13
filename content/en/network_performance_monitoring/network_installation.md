---
title: Network Performance Monitoring Installation
kind: documentation
disable_toc: true
description: Collect your Network Data with the Agent.
further_reading:
    - link: "https://www.datadoghq.com/blog/network-performance-monitoring"
      tag: "Blog"
      text: "Network Performance Monitoring"
    - link: "https://www.datadoghq.com/blog/monitoring-101-alerting/"
      tag: "Blog"
      text: "Monitoring 101: Alerting on what matters"
    - link: "/api/#monitors"
      tag: "Documentation"
      text: "Datadog Monitors API"
---

<div class="alert alert-warning">
This feature is currently in beta. Request access by filling out the <a href="https://app.datadoghq.com/network/2019signup">Datadog Network Performance Monitoring Beta Request form</a>.
</div>

Network performance monitoring requires Datadog Agent v6.13+. To enable network performance monitoring, configure it in your [Agent main configuration file][1] based on your system setup:

{{< tabs >}}
{{% tab "Agent" %}}

To enable network performance monitoring with the Datadog Agent, use the following configurations:

1. Copy the system-probe example configuration:<br>
`sudo -u dd-agent cp /etc/datadog-agent/system-probe.yaml.example /etc/datadog-agent/system-probe.yaml`
2. Modify the system-probe configuration file to set the enable flag to `true`:<br>

3. Optionally uncomment the `system_probe_config` parameter to add a custom object:
    ```
    ## @param system_probe_config - custom object - optional
    ## (...)
    #
    system_probe_config:
    ```

4. Enter specific configurations for your System Probe data collection:
    ```
    system_probe_config:
        ## @param enabled - boolean - optional - default: false
        ## Set to true to enable the System Probe.
        #
        enabled: true
    ```

5. Start the system-probe: `sudo service datadog-agent-sysprobe start`
6. [Restart the Agent][1].

[1]: https://docs.datadoghq.com/agent/guide/agent-commands/?tab=agentv6#restart-the-agent
{{% /tab %}}
{{% tab "Kubernetes" %}}

To enable network performance monitoring with Kubernetes, use the following configuration:

```yaml
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
  name: datadog-agent
  namespace: default
spec:
  template:
    metadata:
      labels:
        app: datadog-agent
      name: datadog-agent
      annotations:
        container.apparmor.security.beta.kubernetes.io/datadog-agent: unconfined
    spec:
      serviceAccountName: datadog-agent
      containers:
      - image: datadog/agent:6.12.0
        imagePullPolicy: Always
        name: datadog-agent
        securityContext:
          capabilities:
            add: ["SYS_ADMIN", "SYS_RESOURCE", "SYS_PTRACE", "NET_ADMIN"]
        ports:
          - {containerPort: 8125, name: dogstatsdport, protocol: UDP}
          - {containerPort: 8126, name: traceport, protocol: TCP}
        env:
          - {name: DD_API_KEY, value: <DATADOG_API_KEY>}
          - {name: KUBERNETES, value: "true"}
          - {name: DD_HEALTH_PORT, value: "5555"}
          - {name: DD_PROCESS_AGENT_ENABLED, value: "true"}
          - {name: DD_SYSTEM_PROBE_ENABLED, value: "true"}
          - {name: DD_SYSPROBE_SOCKET, value: "/var/run/s6/sysprobe.sock"}
          - name: DD_KUBERNETES_KUBELET_HOST
            valueFrom:
              fieldRef:
                fieldPath: status.hostIP
        resources:
          requests:
            memory: "256Mi"
            cpu: "200m"
          limits:
            memory: "256Mi"
            cpu: "200m"
        volumeMounts:
          - {name: dockersocket, mountPath: /var/run/docker.sock}
          - {name: procdir, mountPath: /host/proc, readOnly: true}
          - {name: cgroups, mountPath: /host/sys/fs/cgroup, readOnly: true}
          - {name: debugfs, mountPath: /sys/kernel/debug}
          - {name: s6-run, mountPath: /var/run/s6}
        livenessProbe:
          httpGet:
            path: /health
            port: 5555
          initialDelaySeconds: 15
          periodSeconds: 15
          timeoutSeconds: 5
          successThreshold: 1
          failureThreshold: 3
      volumes:
        - {name: dockersocket, hostPath: {path: /var/run/docker.sock}}
        - {name: procdir, hostPath: {path: /proc}}
        - {name: cgroups, hostPath: {path: /sys/fs/cgroup}}
        - {name: s6-run, emptyDir: {}}
        - {name: debugfs, hostPath: {path: /sys/kernel/debug}}
```

Don't forget to replace `<DATADOG_API_KEY>` with the [API key associated to your Datadog Account][1].

[1]: https://app.datadoghq.com/account/settings#api
{{% /tab %}}
{{% tab "Docker" %}}

To enable Network Performance monitoring in Docker, use the following configuration when starting the container Agent:

```
$ docker run -e DD_API_KEY="<DATADOG_API_KEY>" \
	-e DD_SYSTEM_PROBE_ENABLED=true \
	-e DD_PROCESS_AGENT_ENABLED=true \
        -v /var/run/docker.sock:/var/run/docker.sock:ro \
        -v /proc/:/host/proc/:ro \
        -v /sys/fs/cgroup/:/host/sys/fs/cgroup:ro \
	-v /sys/kernel/debug:/sys/kernel/debug \
	--security-opt apparmor:unconfined \
	--cap-add=SYS_ADMIN \
	--cap-add=SYS_RESOURCE \
	--cap-add=SYS_PTRACE \
	--cap-add=NET_ADMIN \
	datadog/agent:latest
  ```

Don't forget to replace `<DATADOG_API_KEY>` with the [API key associated to your Datadog Account][1].

[1]: https://app.datadoghq.com/account/settings#api
{{% /tab %}}
{{< /tabs >}}

## Further Reading

{{< partial name="whats-next/whats-next.html" >}}
[1]: /agent/guide/agent-configuration-files/?tab=agentv6#agent-main-configuration-file

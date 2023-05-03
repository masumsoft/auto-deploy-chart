# Auto-deploy Helm Chart for Gitlab CI/CD

## Requirements

- Helm `2.9.0` and above is required in order support `"helm.sh/hook-delete-policy": before-hook-creation` for migrations

## Configuration

| Parameter                     | Description | Default                            |
| ---                           | ---         | ---                                |
| replicaCount                  |             | `1`                                |
| strategyType                  | Pod deployment [strategy](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#strategy) | `nil` |
| serviceAccountName(**DEPRECATED**)            | Pod service account name override  | `nil` |
| serviceAccount.name           | Name of service account to use for running the pods | `nil` |
| serviceAccount.createNew      | If set to `true`, a new service account will be created with the details specified in the other fields under `serviceAccount`. If set to `false`, the service account specified in `serviceAccount.name` is expected to already exist. | `false` |
| serviceAccount.annotations    | Annotations for the service account to be created | `nil` |
| image.repository              |             | `gitlab.example.com/group/project` |
| image.tag                     |             | `stable`                           |
| image.pullPolicy              |             | `Always`                           |
| image.secrets                 |             | `[name: gitlab-registry]`          |
| extraLabels                   | Allow labelling resources with custom key/value pairs | `{}` |
| lifecycle                     | [Container lifecycle hooks](https://kubernetes.io/docs/concepts/containers/container-lifecycle-hooks/) | `{}` |
| podAnnotations                | Pod annotations | `{}`                           |
| dnsPolicy                     | Pod DNS policy  | `{}`                           |
| dnsConfig                     | [Pod DNS config](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-dns-config)  | `{}` |
| nodeSelector                  | Node labels for pod assignment | `{}`           |
| tolerations                   | List of node taints to tolerate | `[]`          |
| priorityClassName             | Assign node [priority](https://kubernetes.io/docs/concepts/scheduling-eviction/pod-priority-preemption/) from priorityClass | `""`
| terminationGracePeriodSeconds | The amount of time in seconds a pod is given to terminate | [See the Kubernetes API for reference](https://kubernetes.io/docs/reference/kubernetes-api/workload-resources/pod-v1/#lifecycle)          |
| hostAliases                   | If present, this will set static hosts to the pod configuration | [See the Kubernetes API for reference](https://kubernetes.io/docs/reference/kubernetes-api/workload-resources/pod-v1/#hostname-and-name-resolution) |
| initContainers                | Containers that are run before the app containers are started. | `[]`          |
| topologySpreadConstraints     | [Pod Topology Spread Constraints](https://kubernetes.io/docs/concepts/workloads/pods/pod-topology-spread-constraints/) | `[]`          |
| affinity                      | Node affinity for pod assignment | `{}`          |
| application.track             |             | `stable`                           |
| application.tier              |             | `web`                              |
| application.migrateCommand    | If present, this variable will run as a shell command within an application Container as a Helm pre-upgrade Hook. Intended to run migration commands. | `nil` |
| application.initializeCommand | If present, this variable will run as shell command within an application Container as a Helm post-install Hook. Intended to run database initialization commands. When set, the Deployment and Cronjob resources will be skipped.| `nil` |
| application.secretName        | Pass in the name of a Secret which the deployment will [load all key-value pairs from the Secret as environment variables](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/#configure-all-key-value-pairs-in-a-configmap-as-container-environment-variables) in the application container. | `nil` |
| application.secretChecksum    | Pass in the checksum of the secrets referenced by `application.secretName`. | `nil` |
| application.database_url      | If present, sets the `DATABASE_URL` environment variable. If postgres is enabled this will be autogenerated. | `nil` |
| application.command           | If present, overrides docker image `ENTRYPOINT`. Needs to be an array. | `nil` |
| application.args              | If present, overrides docker image `CMD`. Needs to be an array. | `nil` |
| hpa.enabled                   | If true, enables horizontal pod autoscaler. A resource request is also required to be set, such as `resources.requests.cpu: 200m`.| `false` |
| hpa.minReplicas               |             | `1`                                |
| hpa.maxReplicas               |             | `5`                                |
| hpa.targetCPUUtilizationPercentage | `autoscaling/v1` - Percentage threshold for when HPA begins scaling out pods. Ignored if `hpa.metrics` is present. | `nil` |
| hpa.metrics                   | `autoscaling/v2beta2`  [metrics](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/) definitions for when HPA begins scaling out pods.  | `nil` |
| gitlab.app                    | GitLab project slug. | `nil` |
| gitlab.env                    | GitLab environment slug. | `nil` |
| gitlab.envName                | GitLab environment name. | `nil` |
| gitlab.envURL                 | GitLab environment URL.  | `nil` |
| gitlab.projectID              | Gitlab project ID.       | `nil` |
| service.enabled               |             | `true`                             |
| service.annotations           | Service annotations | `{}`                       |
| service.name                  |             | `web`                              |
| service.type                  |             | `ClusterIP`                        |
| service.nodePort              | Used to set NodePort number if service.type == 'NodePort' | `0` (random)                  |
| service.url                   |             | `http://my.host.com/`              |
| service.additionalHosts       | If present, this list will add additional hostnames to the server configuration. | `nil` |
| service.commonName            | If present, this will define the ssl certificate common name to be used by CertManager. `service.url` and `service.additionalHosts` will be added as Subject Alternative Names (SANs) | `nil` |
| service.externalPort          |             | `5000`                             |
| service.internalPort          |             | `5000`                             |
| service.extraPorts        | List of additional service port definitions | `[]` |
| service.extraPorts.port   | Integer external port number | `nil` |
| service.extraPorts.targetPort | Integer container port number | `nil` |
| service.extraPorts.protocol | Protocol of the service port definition | `nil` |
| service.extraPorts.name | Name of the service port definition | `nil` |
| ingress.enabled               | If true, enables ingress | `true`                |
| ingress.className             | The name of the ingress class to use. When present, sets `ingressClassName` and `kubernetes.io/ingress.class` as appropriate. | `nil`                |
| ingress.path                  | Default path for the ingress | `/` |
| ingress.tls.enabled           | If true, enables SSL | `true`                    |
| ingress.tls.acme              | Controls `kubernetes.io/tls-acme` annotation | `true` |
| ingress.tls.secretName        | Name of the secret used to terminate SSL traffic | `""` |
| ingress.tls.useDefaultSecret  | If set to `true`, the `secretName` is not used, which makes Ingress fall back to the default secret (certificate). This requires [configuration of the default secret](https://kubernetes.github.io/ingress-nginx/user-guide/tls/#default-ssl-certificate). | `false` |
| ingress.modSecurity.enabled | Enable custom configuration for modsecurity, defaulting to [the Core Rule Set](https://coreruleset.org) | `false` |
| ingress.modSecurity.secRuleEngine | Configuration for [ModSecurity's rule engine](https://github.com/SpiderLabs/ModSecurity/wiki/Reference-Manual-(v2.x)#SecRuleEngine) | `DetectionOnly` |
| ingress.modSecurity.secRules | Configuration for custom [ModSecurity's rules](https://github.com/SpiderLabs/ModSecurity/wiki/Reference-Manual-(v2.x)#secrule) | `nil` |
| ingress.annotations           | Ingress annotations | See [`_ingress-annotations.yaml`](./templates/_ingress-annotations.yaml) |
| livenessProbe.path            | Path to access on the HTTP server on periodic probe of container liveness. | `/`                                |
| livenessProbe.scheme          | Scheme to access the HTTP server (HTTP or HTTPS). | `HTTP`                                |
| livenessProbe.httpHeaders       | List of additional custom headers to send on the server | `[]`                                |
| livenessProbe.httpHeaders.name  | Name of the custom header | `nil`                                |
| livenessProbe.httpHeaders.value | Value of the header | `nil`                                |
| livenessProbe.initialDelaySeconds | # of seconds after the container has started before liveness probes are initiated. | `15`                               |
| livenessProbe.timeoutSeconds  | # of seconds after which the liveness probe times out. | `15`                               |
| livenessProbe.probeType       | Type of [liveness probe](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes) to use. | `httpGet`
| livenessProbe.command         | Commands for use with probe type 'exec'. | `{}`
| readinessProbe.path           | Path to access on the HTTP server on periodic probe of container readiness. | `/`                                |
| readinessProbe.scheme         | Scheme to access the HTTP server (HTTP or HTTPS). | `HTTP`                                |
| readinessProbe.httpHeaders      | List of additional custom headers to send on the server | `[]`                                |
| readinessProbe.httpHeaders.name | Name of the custom header | `nil`                                |
| readinessProbe.httpHeaders.value | Value of the header | `nil`                                |
| readinessProbe.initialDelaySeconds | # of seconds after the container has started before readiness probes are initiated. | `5`                                |
| readinessProbe.timeoutSeconds | # of seconds after which the readiness probe times out. | `3`                                |
| readinessProbe.probeType     | Type of [readiness probe](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes) to use. | `httpGet`
| readinessProbe.command       | Commands for use with probe type 'exec'. | `{}`
| startupProbe.enabled         | If true, enables startup probe. | `/`                                |
| startupProbe.path            | Path to access on the HTTP server on periodic probe of container startup. | `/`                                |
| startupProbe.scheme          | Scheme to access the HTTP server (HTTP or HTTPS). | `HTTP`                                |
| startUpProbe.httpHeaders      | List of additional custom headers to send on the server | `[]`                                |
| startUpProbe.httpHeaders.name | Name of the custom header | `nil`                                |
| startUpProbe.httpHeaders.value | Value of the header | `nil`                                |
| startupProbe.initialDelaySeconds | # of seconds after the container has started before startup probes are initiated. | `5`                                |
| startupProbe.timeoutSeconds  | # of seconds after which the startup probe times out. | `3`                                |
| startupProbe.failureThreshold | # of times, Kubernetes will retry failed probes before giving up. | `30`                                |
| startupProbe.periodSeconds  | How often (in seconds) to perform the probe. | `10`                                |
| startupProbe.probeType      | Type of [startup probe](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes) to use. | `httpGet`
| startupProbe.command        | Commands for use with probe type 'exec'. | `{}`
| postgresql.managed            | If true, this will provision a managed Postgres instance via crossplane.            | `false`                             |
| postgresql.managedClassSelector            | This will allow provisioning a Postgres instance based on label selectors via Crossplane, eg: `managedClassSelector.matchLabels.stack: gitlab`. The `postgresql.managed` value should be true as well for this to be honoured. [Crossplane Configuration](https://docs.gitlab.com/ee/user/clusters/applications.html#crossplane)            | `{}`                             |
| podDisruptionBudget.enabled   |             | `false`                            |
| podDisruptionBudget.maxUnavailable |             | `1`                            |
| podDisruptionBudget.minAvailable | If present, this variable will configure minAvailable in the PodDisruptionBudget. :warning: if you have `replicaCount: 1` and `podDisruptionBudget.minAvailable: 1` `kubectl drain` will be blocked.              | `nil`                            |
| prometheus.metrics            | Annotates the service for prometheus auto-discovery. Also denies access to the `/metrics` endpoint from external addresses with Ingress. | `false` |
| networkPolicy.enabled(**DEPRECATED**)         | Enable container network policy | `false` |
| networkPolicy.spec(**DEPRECATED**)            | [Network policy](https://kubernetes.io/docs/concepts/services-networking/network-policies/) definition | `{ podSelector: { matchLabels: {} }, ingress: [{ from: [{ podSelector: { matchLabels: {} } }, { namespaceSelector: { matchLabels: { app.gitlab.com/managed_by: gitlab } } }] }] }` |
| persistence.enabled           | Allow a [persistent volume claim](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims) (PVC) to be mounted as a volume. <br/> **Warning:** Auto-created PVCs are deleted any time `persistence.enabled` is set to `false`. | `false` |
| persistence.volumes[].name         | The name of the volume. | `data` |
| persistence.volumes[].mount.path         | The mount path in the deployment containers. | `/pvc-mount` |
| persistence.volumes[].mount.subPath      | (Optional) The [subPath](https://kubernetes.io/docs/concepts/storage/volumes/#using-subpath) of the path. | |
| persistence.volumes[].claim.accessMode   | The access mode of the PVC. | `ReadWriteOnce` |
| persistence.volumes[].claim.size         | The storage size of the PVC. | `8Gi` |
| persistence.volumes[].claim.storageClass | (Optional) The storage class of the PVC. If not specified, it falls back to the default storage class from the provider. | `nil` |
| extraVolumes | This field allows to add extra volumes to your Deployment. | `[]` |
| extraVolumeMounts | This field allows to add extra volume mounts to your Deployment. | `[]` |
| cronjobs                            | Define your jobs in this section, an example of the definition can be found in values.yaml | `nil` |
| cronjob.job.failedJobsHistoryLimit          | This field specify how many failed jobs are kept | `1` |
| cronjob.job.startingDeadlineSeconds         | If a CronJob controller cannot start a job run on its schedule, it will keep retrying until the value (In seconds) is reached. | `300` |
| cronjob.job.successfulJobsHistoryLimit      | This field specify how many completed jobs are kept | `1` |
| cronjob.job.concurrencyPolicy               | If `cronjob.concurrencyPolicy` is set to Forbid and a CronJob was attempted to be scheduled when there was a previous schedule still running, then it would count as missed. | `Forbid` |
| cronjob.job.restartPolicy                   | Possible values: `Always`, `OnFailure` and `Never` | `OnFailure` |
| cronjob.job.extraVolumes | This field allows to add extra volumes to CronJob Pods. | `[]` |
| cronjob.job.extraVolumeMounts | This field allows to add extra volume mounts to CronJob Pods. | `[]` |
| cronjob.job.livenessProbe           | If defined, enables livenessProbe in the cronjob. If not defined, it uses top-level `livenessProbe` setting to the job. (To see details about the default probes check values.yaml) | |
| cronjob.job.readinessProbe           | If defined, enables readinessProbe in the cronjob. If not defined, it uses top-level `readinessProbe` setting to the job. (To see details about the default probes check values.yaml) | |
| cronjob.activeDeadlineSeconds           | Alternative to terminate a Job: Once a Job reaches `activeDeadlineSeconds` value, all of its running Pods are terminated and the Job status will become `type: Failed` with `reason: DeadlineExceeded` | `nil` |


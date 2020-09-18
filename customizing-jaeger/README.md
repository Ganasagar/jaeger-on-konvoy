
## Modifying Jaeger Deployment in Konvoy.

Jaeger in Konvoy is installed as a deployment that's based on an all-in-one jaeger image which is validated by Istio-Community. Unlike standalone installation of Jaeger, Jaeger in Konvoy leverages Istio's envoy proxy as the agent which is responsible for capturing the traces. The all-in-one Jaeger deployment which serves the Jaeger Query, Jaeger UI is deployed in `istio namespace` . Jaeger in Konvoy currently leverages ephemeral storage but we expect this to change to persistent using `elastic cluster` which is shipped with Konvoy

Jaeger is deployed as a part of the istio operator which leverages helm charts for reconciling cluster state. Jaeger implementation is divided into two major sections, the agent and the collector. The agent's role is taken over by envoy proxy hence any agent related changes would need to be made to the envoy which is a part of the `istioOperator`. All the modifications go are made to the addons section in `cluster.yaml` which will be applied to the underlying charts at runtime. Review the `istioOperator-values.yaml` file in this repo for all the available options

Any changes to the collector will need modifications to the `tracing` charts. All the modifications are made to the addons section in `cluster.yaml` which will be applied to the underlying charts at runtime. Review the `tracing-values.yaml` file in this repo for all the available options for configuring 



### Changing the sampling rate in Jager. 

Modifying sampling rate is one of the most common use-cases while using Jaeger. Since 


#### Changing sampling rate for Jaeger 
Jaeger agent responsibilities are taken up by Envoy proxy which runs a sidecar proxy in a service mesh. In order to be able to tweak the trace-sampling for Istio we would need to pass the value to pilot in Istio's control plane. The pilot then configures all the proxies to modify the sampling rate. Below you can see an example implementing the same change 


Open up the `cluster.yaml` and add the paramters for the configuring sampling rate as listed below. 
```bash
- name: istio
  enabled: true
  values: |
    istioOperator:
      values:
        global:
          pilot:
            traceSampling: 5.0
```


### Changing just resource specifications for Jaeger Deployment.
Jaeger in Istio is renamed to be used as **tracing**. Any modifications to the deployment itself would need to go under the tracing chart. Below is an example depicting the same.
```bash
- name: istio
  enabled: true
  values: |
    istioOperator:
      values:
        global:
          tracing:
            jaeger:
              resources:
                limits:
                  cpu: 1000m
                  memory: 2048Mi
                requests:
                  cpu: 150m
                  memory: 900Mi
```


### Chaging both Jaeger resource & sampling rate
```bash
- name: istio
  enabled: true
  values: |
    istioOperator:
      values:
        global:
          pilot:
            traceSampling: 3.0
          tracing:
            jaeger:
              resources:
                limits:
                  cpu: 1000m
                  memory: 2048Mi
                requests:
                  cpu: 150m
                  memory: 900Mi
          
  
``` 

#### Apply changes in Istio

Once you are done with modifying the `cluster.yaml` with the options you desire like above. redeploy the addons. 
```bash
konvoy deploy addons --yes
```
Output:
```
konvoyconfig                                                           [OK]
reloader                                                               [OK]
dashboard                                                              [OK]
external-dns                                                           [OK]
cert-manager                                                           [OK]
defaultstorageclass-protection                                         [OK]
opsportal                                                              [OK]
gatekeeper                                                             [OK]
traefik                                                                [OK]
awsebscsiprovisioner                                                   [OK]
istio                                                                  [OK]
dex                                                                    [OK]
kube-oidc-proxy                                                        [OK]
traefik-forward-auth                                                   [OK]
dex-k8s-authenticator                                                  [OK]
prometheus                                                             [OK]
velero                                                                 [OK]
prometheusadapter                                                      [OK]
elasticsearch                                                          [OK]
elasticsearch-curator                                                  [OK]
elasticsearchexporter                                                  [OK]
fluentbit                                                              [OK]
kibana                                                                 [OK]
kommander                                                              [OK]

Kubernetes cluster and addons deployed successfully!
```

# Jaeger Distributed Tracing on Konvoy

When moving from a Monolithic to Microservices architecture, the application is usually structured as a set of loosely coupled and interdependent services. All the capabilities of the monolith are accomplished by these sets of microservices communicating with each other using synchronous protocols like REST/HTTP or asynchronous protocols like AMQP. This decoupling enables the microservices to use languages frameworks that suit them best. 

When a request is initiated by a specific service it might traverse between several microservices before a response is provided. The request could hop between other microservices which may or may not make queries to several databases for processing the request. As the total number of microservices increases the complexity of the request/response cycle increases as well. When running a microservices based architecture like this in production with heavy load keeping track of failures, errors, delays and anomalies becomes exponentially difficult. Jaeger which is based on OpenTracing initiative traces the request and responses across all the micro-services as a single context and provides better insights into the behaviour of application as a whole. 

Konvoy ships Jaeger as part of the Istio which is deployed by the Istio Operator. The architecture consists of Envoy proxies which act as the clients that capture traces and there is a stand alone tracing deployment which acts as the collector and serves Jaeger UI. Jaeger uses the elastic stack which is deployed as part of Istio as the persistent data store for stored trace information.      

## Jaeger testdrive on Konvoy  

Jaeger is enabled in Konvy out of the box and does not need any additional configuration to get started with Jaeger. Once you have the Konvoy cluster up and running. Deploy the bookinfo application. We will be using the Bookinfo application to demo Jaeger. 

Install Istio in your Konvoy cluster but changing the default value of false → true for Istio in the cluster.yaml file
Once done, Trigger the addons built on the cluster and ensure that Istio is installed on the cluster

```bash
Konvoy deploy addons --yes 
```
Output:
```
STAGE [Deploying Enabled Addons]
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


Go to the Istio release page to download the installation file for your OS:


curl -L https://istio.io/downloadIstio | sh -






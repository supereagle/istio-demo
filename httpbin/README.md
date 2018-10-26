# Httpbin 

## Pre-requirements

* Istio 1.0
* Kubernetes 1.10

### Status of environment

```shell
kubectl -n istio-system get svc
NAME                       TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                                                                                                     AGE
grafana                    ClusterIP   10.254.154.239   <none>        3000/TCP                                                                                                    5d
istio-citadel              ClusterIP   10.254.238.4     <none>        8060/TCP,9093/TCP                                                                                           5d
istio-egressgateway        ClusterIP   10.254.205.168   <none>        80/TCP,443/TCP                                                                                              5d
istio-galley               ClusterIP   10.254.218.135   <none>        443/TCP,9093/TCP                                                                                            5d
istio-ingressgateway       NodePort    10.254.94.15     <none>        80:31380/TCP,443:31390/TCP,31400:31400/TCP,15011:32067/TCP,8060:30846/TCP,15030:31721/TCP,15031:31277/TCP   5d
istio-pilot                ClusterIP   10.254.72.121    <none>        15010/TCP,15011/TCP,8080/TCP,9093/TCP                                                                       5d
istio-policy               ClusterIP   10.254.184.92    <none>        9091/TCP,15004/TCP,9093/TCP                                                                                 5d
istio-sidecar-injector     ClusterIP   10.254.4.39      <none>        443/TCP                                                                                                     5d
istio-statsd-prom-bridge   ClusterIP   10.254.114.180   <none>        9102/TCP,9125/UDP                                                                                           5d
istio-telemetry            ClusterIP   10.254.158.75    <none>        9091/TCP,15004/TCP,9093/TCP,42422/TCP                                                                       5d
jaeger-agent               ClusterIP   None             <none>        5775/UDP,6831/UDP,6832/UDP                                                                                  5d
jaeger-collector           ClusterIP   10.254.28.89     <none>        14267/TCP,14268/TCP                                                                                         5d
jaeger-query               NodePort    10.254.141.176   <none>        16686:31000/TCP                                                                                             5d
prometheus                 ClusterIP   10.254.180.168   <none>        9090/TCP                                                                                                    5d
servicegraph               ClusterIP   10.254.213.19    <none>        8088/TCP                                                                                                    5d
tracing                    ClusterIP   10.254.49.171    <none>        80/TCP                                                                                                      5d
zipkin                     ClusterIP   10.254.135.132   <none>        9411/TCP                                                                                                    5d
```

> Use node port to export necessary services, such as istio-ingressgateway and jaeger-query.

## Setup

* Create namespace

```shell
$ kubectl create ns httpbin
```

* Create deployment with sidecar

```shell
kubectl -n httpbin apply -f <(istioctl kube-inject -f httpbin.yaml)
```

* Create gateway

```shell
kubectl -n httpbin apply -f gateway.yaml
```

* Create virtual service

```shell
kubectl -n httpbin apply -f virtualservice.yaml
```

## Test

```shell
$ curl -I -HHost:httpbin.example.com http://httpbin.example.com:31380/status/200
HTTP/1.1 200 OK
server: envoy
date: Fri, 26 Oct 2018 09:15:00 GMT
content-type: text/html; charset=utf-8
access-control-allow-origin: *
access-control-allow-credentials: true
content-length: 0
x-envoy-upstream-service-time: 16

$ curl -I -HHost:httpbin.example.com http://httpbin.example.com:31380/headers
HTTP/1.1 404 Not Found
date: Fri, 26 Oct 2018 09:20:20 GMT
server: envoy
transfer-encoding: chunked
```

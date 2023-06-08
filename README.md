## Local K3d DUBBD development
 

#### Setup K3d cluster

Run shell script to create cluster, add MetalLB, and `zarf init` the cluster
`./mp-k3d-dev.sh`

#### Build and install DUBBD
Create and deploy the [DUBBD package](https://github.com/defenseunicorns/zarf-package-big-bang/tree/main/k3d)
`zarf package create --confirm`
`zarf package deploy <package name> --confirm`


#### Build and deploy Podinfo
Palassis created a podinfo example that exercises two Istio ingressGateways.
* The public gateway performs TLS termination at the edge as expected.
* The passthrough gateway allows the request to be routed to the appropriate backend where the application performs TLS termination 



> Before deploying this package you will need to delete the kyverno clusterpolicies that prevents unauthorized registries
```
k delete clusterpolicies restrict-image-registries
```


Create and deploy the [Podinfo package](https://github.com/mxnxPx/zarf-podinfos/)
`zarf package create --confirm`
`zarf package deploy <package name> --confirm`



Update `/etc/hosts` with the external ip addresses for ingress loadbalancers
```
NAMESPACE↑         NAME                             TYPE               CLUSTER-IP          EXTERNAL-IP  
istio-system       passthrough-ingressgateway       LoadBalancer       10.43.80.151        172.20.1.233 
istio-system       public-ingressgateway            LoadBalancer       10.43.103.167       172.20.1.234 
```

```
172.20.1.233 podinfo-too.bigbang.dev    # passthrough-ingressgateway
172.20.1.234 podinfo.bigbang.dev        # public-ingressgateway
```


Test 

`curl -v -k https://podinfo-too.bigbang.dev:443`    
`curl -v -k https://podinfo.bigbang.dev:443`

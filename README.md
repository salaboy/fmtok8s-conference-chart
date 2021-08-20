# FMTOK8S-APP
From Monolith to K8s :: Application Parent Helm Chart 

This version can be configured to work with Knative Services by using the following install command which overrides the values.yaml file:

```
cat <<EOF | helm install app fmtok8s/fmtok8s-app --values=-                   
fmtok8s-api-gateway:
  knativeDeploy: true
  env:
    KNATIVE_ENABLED: "true"
    AGENDA_SERVICE: http://fmtok8s-agenda.default.svc.cluster.local
    C4P_SERVICE: http://fmtok8s-c4p.default.svc.cluster.local
    EMAIL_SERVICE: http://fmtok8s-email.default.svc.cluster.local

fmtok8s-agenda-rest:
  knativeDeploy: true
fmtok8s-c4p-rest:
  knativeDeploy: true
  env:
    AGENDA_SERVICE: http://fmtok8s-agenda.default.svc.cluster.local
    EMAIL_SERVICE: http://fmtok8s-email.default.svc.cluster.local
fmtok8s-email-rest:
  knativeDeploy: true
EOF
```

For more information check: https://github.com/salaboy/from-monolith-to-k8s

## Release

```
helm dependency build
helm package .
```

Then copy to http://github.com/salaboy/helm and run `release.sh` then push


## Demo
Install Knative first: 

Serving: 
https://knative.dev/docs/install/install-serving-with-yaml/

And don't forget to patch!

```
kubectl patch cm config-features -n knative-serving -p '{"data":{"tag-header-based-routing":"Enabled", "kubernetes.podspec-fieldref": "Enabled"}}'

```
Install: 

```
cat <<EOF | helm install app fmtok8s/fmtok8s-app --values=-                   
fmtok8s-api-gateway:
  knativeDeploy: true
  env:
    KNATIVE_ENABLED: "true"
    AGENDA_SERVICE: http://fmtok8s-agenda.default.svc.cluster.local
    C4P_SERVICE: http://fmtok8s-c4p.default.svc.cluster.local
    EMAIL_SERVICE: http://fmtok8s-email.default.svc.cluster.local

fmtok8s-agenda-rest:
  knativeDeploy: true
fmtok8s-c4p-rest:
  knativeDeploy: true
  env:
    AGENDA_SERVICE: http://fmtok8s-agenda.default.svc.cluster.local
    EMAIL_SERVICE: http://fmtok8s-email.default.svc.cluster.local
fmtok8s-email-rest:
  knativeDeploy: true
EOF
```

### For Knative traffic routing 

Change FrontEnd for Color Image: 
```
image: salaboy/fmtok8s-api-gateway:0.1.0-color
```

And add the following traffic spliting routes:

```
 traffic:
  - latestRevision: true
    percent: 50
  - latestRevision: false
    percent: 50
    revisionName: fmtok8s-api-gateway-00002

```


Change FrontEnd for Debug Image: 

```
image: salaboy/fmtok8s-api-gateway:0.1.0-debug
```

Also increase the number of replicas
```
autoscaling.knative.dev/minScale: "1"
```

And the following tag based routing rules:

```
 traffic:
  - latestRevision: false
    percent: 100
    revisionName: fmtok8s-api-gateway-00001
    tag: current
  - latestRevision: false
    percent: 0
    revisionName: fmtok8s-api-gateway-00002
    tag: debug
  - latestRevision: true
    percent: 0
    tag: latest
```

## For Eventing

Install Eventing:

https://knative.dev/docs/admin/install/eventing/install-eventing-with-yaml/

```
kubectl create -f - <<EOF
apiVersion: eventing.knative.dev/v1
kind: broker
metadata:
 name: default
 namespace: default
EOF
```

Install SockEye:

```
kubectl apply -f https://github.com/n3wscott/sockeye/releases/download/v0.7.0/release.yaml

```

Update KSVC with the following ENV Variables:

```
 - name: EVENTS_ENABLED
          value: "true"
        - name: K_SINK
          value: http://broker-ingress.knative-eventing.svc.cluster.local/default/default 
```

Create Trigger for Sockeye

```
kubectl create -f - <<EOF
apiVersion: eventing.knative.dev/v1
kind: Trigger
metadata:
  name: app-trigger
  namespace: default
spec:
  broker: default
  subscriber:
    uri: http://sockeye.default.svc.cluster.local
EOF
```
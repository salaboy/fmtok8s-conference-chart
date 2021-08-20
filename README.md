# FMTOK8S-APP
From Monolith to K8s :: Application Parent Helm Chart 

This version can be configured to work with Knative Services by using the following install command which overrides the values.yaml file:

```
cat <<EOF | helm install app fmtok8s/fmtok8s-app --values=-
fmtok8s-api-gateway:
  knativeDeploy: true
  env:
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

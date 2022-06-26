# FMTOK8S :: Conference Platform Parent Helm Chart 

To install this chart run:

```
helm repo add fmtok8s https://github.com/salaboy/helm
helm repo update
helm install conference fmtok8s/fmtok8s-conference-chart
```

For a step-by-step tutorial of running this application on KinD visit: [[https://github.com/salaboy/from-monolith-to-k8s/tree/main/knative](https://github.com/salaboy/from-monolith-to-k8s/tree/main/kind)]([https://github.com/salaboy/from-monolith-to-k8s/tree/main/knative](https://github.com/salaboy/from-monolith-to-k8s/tree/main/kind))

This version can be configured to work with Knative Services by using the following install command which overrides the values.yaml file:

```
cat <<EOF | helm install conference-knative fmtok8s/fmtok8s-conference-chart --values=-
fmtok8s-frontend:
  knative:
    enabled: true
  prometheus:
    enabled: true
  env:
    KNATIVE_ENABLED: "true"
    FRONTEND_AGENDA_SERVICE: http://fmtok8s-agenda.default.svc.cluster.local
    FRONTEND_C4P_SERVICE: http://fmtok8s-c4p.default.svc.cluster.local
    FRONTEND_EMAIL_SERVICE: http://fmtok8s-email.default.svc.cluster.local  
fmtok8s-email-service:
  knative:
    enabled: true
  prometheus:
    enabled: true
fmtok8s-c4p-service:
  knative:
    enabled: true
  prometheus:
    enabled: true
  env:
    C4P_AGENDA_SERVICE: http://fmtok8s-agenda.default.svc.cluster.local
    C4P_EMAIL_SERVICE: http://fmtok8s-email.default.svc.cluster.local  
fmtok8s-agenda-service:
  knative:
    enabled: true
  prometheus:
    enabled: true 
EOF
```

For a step-by-step tutorial with Knative Serving and Eventing visit: [https://github.com/salaboy/from-monolith-to-k8s/tree/main/knative](https://github.com/salaboy/from-monolith-to-k8s/tree/main/knative)

For more information check: [https://github.com/salaboy/from-monolith-to-k8s](https://github.com/salaboy/from-monolith-to-k8s)





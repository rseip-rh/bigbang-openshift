# Istio

See [here](https://istio.io/latest/docs/setup/platform-setup/openshift/).

## Description

Configuring, enabling, and validating the Platform One Istio on OpenShift.

## Configure and Enable Big Bang Istio

In demo_values.yaml:

```
istio:
  enabled: true
  values:
    domain: apps.bigbang1.redhatgov.io

...

istiooperator:
  enabled: true
```


```
helm upgrade --install bigbang $HOME/bigbang/chart   --values https://repo1.dso.mil/platform-one/big-bang/bigbang/-/raw/master/chart/ingress-certs.yaml   --values $HOME/bigbang/ib_creds.yaml   --values $HOME/bigbang/demo_values.yaml   --namespace=bigbang --create-namespace
watch oc get hr -n bigbang
oc -n istio-system expose svc/public-ingressgateway --port=http2
```

Once charts are reconciled, patch the daemonset.

```
kubectl get daemonset istio-cni-node -n kube-system -o json | jq '.spec.template.spec.containers[] += {"securityContext":{"privileged":true}}' | kubectl replace -f -
```

Note that SCC/PodSecurity transition may result in the following warning message.
```
Warning: would violate PodSecurity "restricted:latest": privileged (container "install-cni" must not set securityContext.privileged=true), allowPrivilegeEscalation != false (container "install-cni" must set securityContext.allowPrivilegeEscalation=false), unrestricted capabilities (container "install-cni" must set securityContext.capabilities.drop=["ALL"]), restricted volume types (volumes "cni-bin-dir", "cni-net-dir", "cni-log-dir" use restricted volume type "hostPath"), runAsNonRoot != true (pod or container "install-cni" must set securityContext.runAsNonRoot=true), seccompProfile (pod or container "install-cni" must set securityContext.seccompProfile.type to "RuntimeDefault" or "Localhost")
daemonset.apps/istio-cni-node replaced
```

## Validate Big Bang Istio

TBD

## Team Contacts

- Chris Mays, @chmays, chmays@redhat.com
- Arian Sanchez, @arisanch, arisanch@redhat.com
- Roger Seip, @rseip, rseip@redhat.com

## Roadmap

TBD

## Contributing

TBD

## License

TBD - what is the appropriate thing here for contributing back to P1?

## Project status

TBD


# Kiali

Kiali provides service mesh observability functionality.

[Kiali is a core package provided by Big Bang](https://repo1.dso.mil/platform-one/big-bang/apps/core/kiali/) .

[Kiali is a component of Red Hat OpenShift Service Mesh 2.x](https://docs.openshift.com/container-platform/4.11/service_mesh/v2x/ossm-architecture.html#understanding-kiali) .

## Description

Installing, configuring, and validating Kiali functionality when Big Bang is installed on OpenShift.

## Configure and Enable Big Bang Kiali

In demo_values.yaml:

```
kiali:
  enabled: true
  values:
    domain: apps.bigbang1.redhatgov.io
    istio:
      enabled: true
    cr:
      spec:
        istio_component_namespaces:
          grafana: my-grafana
          prometheus: openshift-monitoring
          tracing: jaeger
        external_services:
          prometheus:
            url: http://monitoring-monitoring-kube-prometheus.monitoring.svc.cluster.local:9090
          grafana:
            #in_cluster_url: "http://monitoring-monitoring-grafana.monitoring.svc.cluster.local:80"
            in_cluster_url: "http://grafana-service.my-grafana.svc.cluster.local:3000"
            url: "https://grafana-route-my-grafana.apps.bigbang1.redhatgov.io/"
          tracing:
            url: "https://tracing.apps.bigbang1.redhatgov.io"
```

Is there a kialioperator namespace to be addressed?

## Validate Big Bang Kiali

TODO confirm servicemonitor
TODO confirm Prometheus URL
TODO confirm Grafana URL and dashboards
TODO confirm GUI endpoint

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


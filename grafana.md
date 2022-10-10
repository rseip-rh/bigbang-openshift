# Grafana

> Grafana is an open source interactive data-visualization platform, developed by Grafana Labs, which allows users to see their data via charts and graphs that are unified into one dashboard (or multiple dashboards!) for easier interpretation and understanding.

[What is Grafana?](https://www.redhat.com/en/topics/data-services/what-is-grafana)

## Description

Big Bang installs upstream Grafana via the [monitoring package](https://repo1.dso.mil/platform-one/big-bang/apps/core/monitoring).

> kube-prometheus-stack collects Kubernetes manifests, Grafana dashboards, and Prometheus rules combined with documentation and scripts to provide easy to operate end-to-end Kubernetes cluster monitoring with Prometheus using the Prometheus Operator.

Red Hat provides a Kubernetes Operator based on the Operator SDK for creating and managing Grafana instances. The Red Hat team has elected to use the operator for this effort.

## Configure and Enable the Grafana operator

Follow instructions [here](https://www.redhat.com/en/blog/custom-grafana-dashboards-red-hat-openshift-container-platform-4).

Custom dashboard example [here](https://github.com/cmays20/hello-world/tree/master/Grafana)

## Validate Grafana Functionality and Big Bang Interoperability

Operator created route at [https://grafana-route-my-grafana.apps.bigbang1.redhatgov.io/](https://grafana-route-my-grafana.apps.bigbang1.redhatgov.io/).

From console admin view, select Observe -> Dashboards

From console admin view, select Operators -> Installed Operators -> Grafana Operator -> Grafana Dashboard -> 

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


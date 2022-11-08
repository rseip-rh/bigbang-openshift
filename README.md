# Documenting Platform One Big Bang on Red Hat OpenShift

## Description

Red Hat effort to validate Big Bang on OpenShift 4.11.

## Install OpenShift in AWS

Workstation or bastion host prerequisites:
```
sudo dnf install wget git perl-Digest-SHA jq
wget https://mirror.openshift.com/pub/openshift-v4/x86_64/clients/ocp/stable/openshift-install-linux.tar.gz
wget https://mirror.openshift.com/pub/openshift-v4/x86_64/clients/ocp/stable/openshift-client-linux.tar.gz
mkdir ~/bin
tar xzvf openshift-client-linux.tar.gz
mv oc ~/bin/
mv kubectl ~/bin/
tar xzvf openshift-install-linux.tar.gz
mv openshift-install ~/bin/
chmod +x ~/bin/*
```

IPI includes all necessary IaC.

```
mkdir $HOME/cluster
cd $HOME/cluster
openshift-install create install-config
```

Modify install-config.yaml as necessary.

```
openshift-install create cluster
chmod g-r $HOME/cluster/auth/kubeconfig
```

## Configure OpenShift for Big Bang

```
curl -s "https://raw.githubusercontent.com/kubernetes-sigs/kustomize/master/hack/install_kustomize.sh"  | bash
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
curl -s https://fluxcd.io/install.sh | sudo bash
cd $HOME
git clone https://repo1.dso.mil/platform-one/big-bang/bigbang.git
cd ~/bigbang
git checkout tags/$(grep 'tag:' base/gitrepository.yaml | awk '{print $2}')
oc login
```

### Install and Configure Flux
```
oc adm policy add-scc-to-group nonroot-v2 system:serviceaccounts:flux-system
export REGISTRY1_USERNAME=xxxx
export REGISTRY1_PASSWORD=yyyy
env | grep REGISTRY1
cd ~/bigbang
$HOME/bigbang/scripts/install_flux.sh -u $REGISTRY1_USERNAME -p $REGISTRY1_PASSWORD
```

### Security Context Constraints for Big Bang Packages

OCP 4.11

```
oc adm policy add-scc-to-group nonroot-v2 system:serviceaccounts:gatekeeper-system
oc adm policy add-scc-to-group nonroot-v2 system:serviceaccounts:cluster-auditor
oc adm policy add-scc-to-group nonroot-v2 system:serviceaccounts:istio-operator
oc adm policy add-scc-to-group nonroot-v2 system:serviceaccounts:istio-system
oc adm policy add-scc-to-group nonroot-v2 system:serviceaccounts:eck-operator
oc adm policy add-scc-to-group nonroot-v2 system:serviceaccounts:logging
oc adm policy add-scc-to-group nonroot-v2 system:serviceaccounts:jaeger
oc adm policy add-scc-to-group nonroot-v2 system:serviceaccounts:kiali
oc adm policy add-scc-to-group nonroot-v2 system:serviceaccounts:monitoring
oc adm policy add-scc-to-user node-exporter -z monitoring-monitoring-prometheus-node-exporter -n monitoring
```

## Install Big Bang

Modify [demo_values.yaml](demo_values.yaml) as necessary.

Refer to [https://repo1.dso.mil/platform-one/big-bang/bigbang/-/blob/master/chart/values.yaml](https://repo1.dso.mil/platform-one/big-bang/bigbang/-/blob/master/chart/values.yaml) for the Big Bang-documented default values that are and can be overridden.

### Create Iron Bank Credentials File
```
cat << EOF > $HOME/bigbang/ib_creds.yaml
registryCredentials:
  registry: registry1.dso.mil
  username: "$REGISTRY1_USERNAME"
  password: "$REGISTRY1_PASSWORD"
EOF
```

### Install Big Bang Umbrella Helm Chart
```
helm upgrade --install bigbang $HOME/cluster/bigbang/chart \
  --values https://repo1.dso.mil/platform-one/big-bang/bigbang/-/raw/master/chart/ingress-certs.yaml \
  --values $HOME/bigbang/ib_creds.yaml \
  --values $HOME/demo_values.yaml \
  --namespace=bigbang --create-namespace
watch oc get hr -n bigbang
```

### Finalize Big Bang Install

```
oc -n istio-system expose svc/public-ingressgateway --port=http2
cat <<\EOF >> $HOME/bigbang/NetworkAttachmentDefinition.yaml
apiVersion: "k8s.cni.cncf.io/v1"
kind: NetworkAttachmentDefinition
metadata:
  name: istio-cni
EOF
oc -n logging create -f ~/bigbang/NetworkAttachmentDefinition.yaml
oc -n eck-operator create -f ~/bigbang/NetworkAttachmentDefinition.yaml
oc -n kiali create -f ~/bigbang/NetworkAttachmentDefinition.yaml
oc -n jaeger create -f ~/bigbang/NetworkAttachmentDefinition.yaml
oc -n monitoring create -f NetworkAttachmentDefinition.yaml
oc -n cluster-auditor create -f ~/bigbang/NetworkAttachmentDefinition.yaml
```

## Validate Big Bang on OpenShift

Refer to https://repo1.dso.mil/platform-one/big-bang/customers/bigbang/-/blob/master/docs/release/README.md#3-ui-tests

### gatekeeper

### eck-operator

### elasticsearch-kibana

See [here](elasticsearch-kibana.md).
### fluentbit

See [here](fluentbit.md).
### grafana

See [here](grafana.md).
### istio

See [here](istio.md).
### jaeger

See [here](jaeger.md).
### kiali

See [here](kiali.md)
### prometheus

See [here](prometheus.md).

## Team Contacts

- Chris Mays, @chmays, chmays@redhat.com
- Arian Sanchez, @arisanch, arisanch@redhat.com
- Roger Seip, @rseip, rseip@redhat.com

## Roadmap
If you have ideas for releases in the future, it is a good idea to list them in the README.

## Contributing
State if you are open to contributions and what your requirements are for accepting them.

For people who want to make changes to your project, it's helpful to have some documentation on how to get started. Perhaps there is a script that they should run or some environment variables that they need to set. Make these steps explicit. These instructions could also be useful to your future self.

You can also document commands to lint the code or run tests. These steps help to ensure high code quality and reduce the likelihood that the changes inadvertently break something. Having instructions for running tests is especially helpful if it requires external setup, such as starting a Selenium server for testing in a browser.

## License

TBD - what is the appropriate thing here for contributing back to P1?

## Project status
If you have run out of energy or time for your project, put a note at the top of the README saying that development has slowed down or stopped completely. Someone may choose to fork your project or volunteer to step in as a maintainer or owner, allowing your project to keep going. You can also make an explicit request for maintainers.


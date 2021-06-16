[![CircleCI](https://circleci.com/gh/giantswarm/harbor-app.svg?style=shield)](https://circleci.com/gh/giantswarm/harbor-app)

# harbor chart

Giant Swarm offers a Harbor App which can be installed in workload clusters.
Here we define the Harbor chart with its templates and default configuration.

**What is this app?**

It is a chart and container repository that runs upon Kubernetes and provide some functionality like vulnerability scanning or signing artifacts.

**Why did we add it?**

Some of our customers like to have the artifacts controlled by them.

**Who can use it?**

It can be used in all type of installations across providers.

## Installing

There are 3 ways to install this app onto a workload cluster.

1. [Using our web interface](https://docs.giantswarm.io/ui-api/web/app-platform/#installing-an-app)
2. [Using our API](https://docs.giantswarm.io/api/#operation/createClusterAppV5)
3. Directly creating the [App custom resource](https://docs.giantswarm.io/ui-api/management-api/crd/apps.application.giantswarm.io/) on the management cluster.

## Configuring

### values.yaml
**This is an example of a values file you could upload using our web interface.**
```
# values.yaml
expose:
  tls:
    enabled: false
  ingress:
    hosts:
      core: harbor.example.com
```

### Sample App CR and ConfigMap for the management cluster
If you have access to the Kubernetes API on the management cluster, you could create
the App CR and ConfigMap directly.

Here is an example that would install the app to
workload cluster `abc12`:

```
# appCR.yaml
apiVersion: application.giantswarm.io/v1alpha1
kind: App
metadata:
  labels:
    app-operator.giantswarm.io/version: 1.0.0
  name: harbor
  namespace: abc12
spec:
  catalog: giantswarm-playground
  config:
    configMap:
      name: abc12-cluster-values
      namespace: abc12
  kubeConfig:
    context:
      name: abc12
    inCluster: false
    secret:
      name: abc12-kubeconfig
      namespace: abc12
  name: harbor
  namespace: harbor
  userConfig:
    configMap:
      name: harbor-user-values
      namespace: abc12
  version: 0.1.0

```

And here is an example of configuration where we disable the TLS at the same time we add a fake domain to the core service. This is discouraged in a production environment but it helps for a quick installation, especially in on premises setups, where you can add a `hosts` entry to your local pointing the Load Balancer or proxy in front your installation.

__Note__: Make sure you have an ingress controller deployment running to route the requests over different services.

```
# user-values-configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: harbor-user-values
  namespace: abc12
data:
  values: |
    expose:
      tls:
        enabled: false
      ingress:
        hosts:
          core: harbor.example.com
```

See our [full reference page on how to configure applications](https://docs.giantswarm.io/app-platform/app-configuration/) for more details.

## Compatibility

This app has been tested to work with the following workload cluster release versions:

* AWS, Azure and KVM 14.x.x versions.

## Limitations

Some apps have restrictions on how they can be deployed.
Not following these limitations will most likely result in a broken deployment.

* It requires to run an ingress controller to allow the portal to be accessible.

## Credit

* [Official repository](https://github.com/goharbor/harbor-helm)

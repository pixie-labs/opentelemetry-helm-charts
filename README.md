# Fluentbit, Otel, Prometheus, Pixie Demo

- [Setup a k8s cluster](#setup-a-k8s-cluster)
  - [Minikube Quickstart (Optional)](#minikube-quickstart--optional-)
- [Install demo apps](#install-demo-apps)

## Setup a k8s cluster

You may pick any k8s cluster to use, as long as it sized large enough to meet the minimum requirements for the various tools we are demoing.

> :warning: **If you are using minikube ensure that you are using a driver supported by Pixie** (see [Pixie Requirements](https://docs.px.dev/installing-pixie/requirements/#kubernetes-local-development-environments))

### Minikube Quickstart (Optional)

1. We recommend the podman driver since it works on Linux machines that lack KVM support, Apple Silicon based macs, and Intel silicon based macs

```console
brew install podman
brew install minikube
```

2. Check installed versions. This demo has been tested with podman `v4` and minikube `v1.30`

```console
podman version
minikube version
```

3. Start a minikube cluster

```console
podman machine init --cpus 4 --memory 8192 --rootful
podman machine start
minikube start --driver=podman --cpus=4 --memory=7680
```

## Install demo app, Fluentbit, Otel, and Prometheus

We use [helm](https://helm.sh/) to install the demo app, Fluentbit, Otel, and Prometheus. This demo has been tested with helm `v3.11.2`.

```console
brew install helm
```

```console
helm repo add otel-demo https://pixie-labs.github.io/opentelemetry-helm-charts
helm repo update
```

```console
helm install --create-namespace --namespace otel-demo otel-demo otel-demo/opentelemetry-demo
```

## Install Pixie

Get the Pixie CLI:

```console
bash -c "$(curl -fsSL https://withpixie.ai/install.sh)"
```

The CLI installer should prompt you to authenticate with Pixie but if it doesn't, you can auth manually by running the following:

```console
px auth login
```

We can now use helm to install Pixie.

```console
helm repo add pixie-operator https://pixie-operator-charts.storage.googleapis.com
helm repo update
```

```console
PIXIE_DEPLOY_KEY=$(px deploy-key create --short)

helm install --namespace pl --create-namespace pixie pixie-operator/pixie-operator-chart --set deployKey="${PIXIE_DEPLOY_KEY}" --set clusterName="${USER}_otel_demo"
```

## Cleanup

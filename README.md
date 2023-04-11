# Fluentbit, Otel, Prometheus, Pixie Demo

- [Setup a k8s cluster](#setup-a-k8s-cluster)
  - [Minikube Quickstart (Optional)](#minikube-quickstart--optional-)
- [Install demo apps](#install-demo-apps)
- [Cleanup](#cleanup)

## Setup a k8s cluster

You may pick any k8s cluster to use, as long as it sized large enough to meet the minimum requirements for the various tools we are demoing.

> :warning: **If you are using minikube ensure that you are using a driver supported by Pixie** (see [Pixie Requirements](https://docs.px.dev/installing-pixie/requirements/#kubernetes-local-development-environments))

### Minikube Quickstart (Optional)

1. We recommend the podman driver since it works on Linux machines that lack KVM support, Apple Silicon based macs, and Intel silicon based macs.

1. For MacOS, we recommend homebrew to install podman and minikube. On Linux, follow your distro specific installers.

    ```console
    brew install podman
    brew install minikube
    ```

1. Check installed versions. This demo has been tested with podman `v4` and minikube `v1.30`

    ```console
    podman version
    minikube version
    ```

1. Start a minikube cluster

    ```console
    podman machine init --cpus 4 --memory 8192 --rootful
    podman machine start
    minikube start --driver=podman --cpus=4 --memory=7680
    ```

## Install demo apps

1. We use helm to install the demo apps

1. Add the otel demo repo

    ```console
    helm repo add otel-demo https://pixie-labs.github.io/opentelemetry-helm-charts
    helm repo update
    ```

1. Install the otel demos

    ```console
    helm install --create-namespace --namespace otel-demo otel-demo otel-demo/opentelemetry-demo
    ```

## Install Pixie

1. Get the Pixie CLI:

    ```console
    bash -c "$(curl -fsSL https://withpixie.ai/install.sh)"
    ```

1. (optional) The CLI installer should prompt you to authenticate with Pixie but if it doesn't, you can auth manually by running the following:

    ```console
    px auth login
    ```

1. We can now use helm to install Pixie. Add the pixie repo

    ```console
    helm repo add pixie-operator https://pixie-operator-charts.storage.googleapis.com
    helm repo update
    ```

1. Install pixie on the cluster

    ```console
    PIXIE_DEPLOY_KEY=$(px deploy-key create --short 2>&1 | grep px-dep)

    helm install --namespace pl --create-namespace pixie pixie-operator/pixie-operator-chart --set deployKey="${PIXIE_DEPLOY_KEY}" --set clusterName="${USER}_otel_demo"
    ```

## Cleanup

```console
minikube delete 
podman machine rm -f
```

# k8s Cluster Setup

- [Cloud based k8s provider](#cloud-based-k8s-provider)
- [Local minikube cluster](#local-minikube-cluster)
  - [Linux](#linux)
  - [MacOS (Intel Based)](#macos--intel-based-)
  - [MacOS (Apple Silicon)](#macos--apple-silicon-)

## Cloud based k8s provider

Please follow the reccomendations and for the cloud based k8s provider of your choice. Note that Pixie doesn't work in all environments, reference the [docs](https://docs.px.dev/installing-pixie/requirements/#kubernetes-production-environments) to ensure that Pixie works
in your environment of choice.

## Local minikube cluster

### Linux

1. We recommend minikube with the kvm2 driver. Consult the docs for your system to install kvm and libvirt properly. Minikube docs link to system specific instructions [here](https://minikube.sigs.k8s.io/docs/drivers/kvm2/#installing-prerequisites)

1. validate that libvirt reports no errors

    ```console
    virt-host-validate
    ```

1. Start a minikube cluster

    ```console
    minikube start --driver=kvm2 --cpus=4 --memory=7680
    ```

### MacOS (Intel Based)

1. We recommend minikube with the hyperkit driver

1. If you use the homebrew package manager, you can use it to install hyperkit

    ```console
    brew install hyperkit
    ```

1. Start a minikube cluster

    ```console
    minikube start --driver=hyperkit --cpus=4 --memory=7680
    ```

### MacOS (Apple Silicon)

1. We recommend minikube with the qemu driver

1. If you use the homebrew package manager, you can use it to install qemu

    ```console
    brew install qemu
    ```

1. Start a minikube cluster

    ```console
    minikube start --driver=qemu --cpus=4 --memory=7680
    ```

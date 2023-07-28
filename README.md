# ARC Configuration
Repository for testing GitHub ARC

## Install ARC Controller

```sh
helm install arc \
    --namespace arc-controller \
    --create-namespace \
    -f controller/values.yml \
    oci://ghcr.io/actions/actions-runner-controller-charts/gha-runner-scale-set-controller
```

## Create a secret

```sh
kubectl create secret generic arc-runners \
    --namespace arc-runners \
    --from-literal=github_app_id=<GITHUB_APP_ID> \
    --from-literal=github_app_installation_id=<GITHUB_APP_INSTALLATION_ID> \
    --from-file=github_app_private_key=<*.private-key.pem> \
    --dry-run=client -o yaml | kubectl apply -f -
```

## Install ARC Scale Set

```sh
helm install arc \
    --namespace arc-runners \
    --create-namespace \
    -f scale-set-1/values.yml \
    oci://ghcr.io/actions/actions-runner-controller-charts/gha-runner-scale-set
```

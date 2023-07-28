# ARC Configuration
Repository for testing GitHub ARC

1. Create a GitHub Codespace

2. Log in to the codespace instance using `gh codespace ssh` :leftwards_arrow_with_hook: , then select the codespace to login.

3. Create Minikube Cluster
    ```sh 
    minikube start -p arc --cpus=2 --memory=4096 --mount-string="/run/udev:/run/udev" --mount
    ```

4. Install ARC Controller
    ```sh
    helm install arc \
        --namespace arc-controller \
        --create-namespace \
        -f helm/controller/values.yml \
        oci://ghcr.io/actions/actions-runner-controller-charts/gha-runner-scale-set-controller
    ```

6. Create a namespace
    ```sh
    kubectl create ns arc-runners
    ```

7. Create a secret
    ```sh
    kubectl create secret generic arc-runners \
        --namespace arc-runners \
        --from-literal=github_app_id=<GITHUB_APP_ID> \
        --from-literal=github_app_installation_id=<GITHUB_APP_INSTALLATION_ID> \
        --from-file=github_app_private_key=<*.private-key.pem> \
        --dry-run=client -o yaml | kubectl apply -f -
    ```

8. Install ARC Scale Set
    ```sh
    helm install arc \
        --namespace arc-runners \
        --create-namespace \
        -f helm/scale-set-1/values.yml \
        oci://ghcr.io/actions/actions-runner-controller-charts/gha-runner-scale-set
    ```

9. Create a workflow under `.github/workflows` with `runs-on` value set to a Scale Set name defined in `helm/scale-set-1/values.yml`

10. For Kubernetes mode, install `openebs` using a Helm chart.
    ```sh
    helm repo add openebs https://openebs.github.io/charts
    helm repo update
    helm install openebs openebs/openebs \
        --namespace openebs \
        --create-namespace
    ```
11. Create a namespace
    ```sh
    kubectl create ns arc-runners-k8
    ``````

12. 7. Create a secret
    ```sh
    kubectl create secret generic arc-runners \
        --namespace arc-runners-k8 \
        --from-literal=github_app_id=<GITHUB_APP_ID> \
        --from-literal=github_app_installation_id=<GITHUB_APP_INSTALLATION_ID> \
        --from-file=github_app_private_key=<*.private-key.pem> \
        --dry-run=client -o yaml | kubectl apply -f -
    ```

12. Install ARC Scale Set
     ```sh
    helm install arc \
        --namespace arc-runners-k8 \
        --create-namespace \
        -f helm/scale-set-2/values.yml \
        oci://ghcr.io/actions/actions-runner-controller-charts/gha-runner-scale-set
    ```
13. Create a workflow under `.github/workflows` with `runs-on` value set to a Scale Set name defined in `helm/scale-set-2/values.yml`
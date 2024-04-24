# *Inprogress*
### Deploy traefik and cert manager onto the cluster
this is a continuation of setting up the kubernetes cluster that techno tim lays out you can find the documentation of him setting up traefik and cert manager [here](https://technotim.live/posts/kube-traefik-cert-manager-le/) and the his youtube video [here](https://www.youtube.com/watch?v=G4CmbYL9UPg)

### Install helm onto the cluster
run these commands to install helm onto the cluster
```
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
```
```
chmod 700 get_helm.sh
```
```
./get_helm.sh
```
Next is to verify if helm is installed
```
helm version
```
you should see an output similar to this
```
version.BuildInfo{Version:"v3.8.0", GitCommit:"d14138609b01886f544b2025f5000351c9eb092e", GitTreeState:"clean", GoVersion:"go1.17.5"}
```

### deploying traefik
First step is adding the traefik repo and update the helm repo list
```
helm repo add traefik https://helm.traefik.io/traefik
```
```
helm repo update
```
next creat the traefik namespace and verify if its created
```
kubectl create namespace traefik
```
```
kubectl get namespaces
```
Next we can install traefik
```
helm install --namespace=traefik traefik traefik/traefik --values=4.Deploy_traefik+Cert_Manager/traefik/values.yaml
```
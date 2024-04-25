# *Inprogress*
## Deploy traefik and cert manager onto the cluster
### this is a continuation of setting up the kubernetes cluster that techno tim lays out you can find the documentation of him setting up traefik and cert manager [here](https://technotim.live/posts/kube-traefik-cert-manager-le/) and the his youtube video [here](https://www.youtube.com/watch?v=G4CmbYL9UPg)

## Install helm onto the cluster
### run these commands to install helm onto the cluster
```
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
```
```
chmod 700 get_helm.sh
```
```
./get_helm.sh
```
### Next is to verify if helm is installed
```
helm version
```
#### you should see an output similar to this
```
version.BuildInfo{Version:"v3.8.0", GitCommit:"d14138609b01886f544b2025f5000351c9eb092e", GitTreeState:"clean", GoVersion:"go1.17.5"}
```

## deploying traefik
### First step is adding the traefik repo and update the helm repo list
```
helm repo add traefik https://helm.traefik.io/traefik
```
```
helm repo update
```
### next creat the traefik namespace and verify if its created
```
kubectl create namespace traefik
```
```
kubectl get namespaces
```
### next we have to edit the ```values.yaml``` for traefik 
```
nano traefikcertmanager/traefik/values.yaml
```
#### options in the ```values.yaml``` you can change
```
loadBalancerIP: 192.168.30.80 //should be changed to a ip in the range you set for metallb 
```
### Next we can install traefik
```
helm install --namespace=traefik traefik traefik/traefik --values=traefikcertmanager/traefik/values.yaml
```
### next we can see if traefik was deployed and if a external ip was given to the traefik service
```
kubectl get svc --all-namespaces -o wide
```
### next we need to install the default headers middleware for traefik 
```
kubectl apply -f traefikcertmanager/traefik/default-headers.yaml
```
#### to verify if middleware was applied to the cluster
```
kubectl get middleware
```
#### should get an output like this
```
NAME              AGE
default-headers   25s
```

## setting up traefik dashboard
### First we need to install htpassword to get a encrypted user token that will give us the login for the dashboard page. this is from the apache2-utils apt.
```
sudo apt-get update
```
```
sudo apt-get install apache2-utils
```
#### to generate the encrypted creds you would run this command replacing username and password with the ones you would like to be the dashboard login
```
htpasswd -nb username password | openssl base64
```
### once you get your token copy and paste into a safe space. then next we would need to place this token into the ```secret-dashboard.yaml```
```
nano traefikcertmanager/traefik/dashboard/secret-dashboard.yaml
```
```
data:
  users: *Paste token here*
```
### after the token is pasted and saved into the yaml we can send this secret to the cluster
```
kubectl apply -f traefikcertmanager/traefik/dashboard/secret-dashboard.yaml
```
#### and to verify the secret was applied to the cluster
```
kubectl get secrets --namespace traefik
```
### next we need to deploy the middleware that will be the auth into accessing the traefik dashboard to the cluster
```
kubectl apply -f traefikcertmanager/traefik/dashboard/middleware.yaml
```

## deploying traefik dashboard ingress
### Now to deploy the traefik dashboard ingress we need to setup a dns name and point it to the correct ip address. If you have a domain name and for this example it would be zacarrie.com since that is the domain i have. We need to go to the ```ingress.yaml``` file and change the hostname to a domain you would like to use.
```
nano traefikcertmanager/traefik/dashboard/ingress.yaml
```
This is what the host should look like by default in ```ingress.yaml```
```
spec:
  entryPoints:
    - websecure
  routes:
    - match: Host(`traefik.local.example.com`) <--- dns name 
```
#### i would like to change this from the domain example.com to zacarrie.com and should look like this afterwords
```
spec:
  entryPoints:
    - websecure
  routes:
    - match: Host(`traefik.local.zacarrie.com`) <--- dns name 
```
### next now we need to point "traefik.local.zacarrie.com" to the loadbalancer ip address we put traefik on in ```values.yaml``` inside of your dns server of choice
```
service:
  enabled: true
  type: LoadBalancer
  annotations: {}
  labels: {}
  spec:
    loadBalancerIP: 192.168.30.80 <--- loadbalancer ip we put traefik on
  loadBalancerSourceRanges: []
  externalIPs: []
```
### now we can apply the ingress setting for traefik
```
kubectl apply -f traefikcertmanager/traefik/dashboard/ingress.yaml
```
### now if in a browser i go to traefik.local.zacarrie.com it should prompt me for the username and password i set in ```htpasswd -nb username password | openssl base64``` and takes me to the traefik dashboard.

## Installing Cert Manager
### First we need to add the helm repo for cert manager to kubernetes
```
helm repo add jetstack https://charts.jetstack.io
```
```
helm repo update
```
### then we need to creat the namespace for cert manager
```
kubectl create namespace cert-manager
```
#### to verify if the name space was created
```
kubectl get namespaces
```

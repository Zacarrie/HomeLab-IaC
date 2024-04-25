# *Inprogress*

# Setting up FluxCD
## This is the steps for getting FluxCD up and running on the k3s cluster we just deployed
### You can find more inforomation about deploying flux from [Technotim's documentation](https://technotim.live/posts/flux-devops-gitops/), [Technotim's youtube video](https://youtu.be/PFLimPh5-wo?si=cGkVMkltf0NTvVuZ), [FluxCD getting started page](https://fluxcd.io/flux/get-started/), and [FluxCD example deployment repo](https://github.com/fluxcd/flux2-kustomize-helm-example).

## First step - Installing Flux cli to host machine
### First thing we need to do is install flux to the host machine and you can do that by running this command
```
curl -s https://fluxcd.io/install.sh | sudo bash
```

## Second step - Github api token
### flux is able to make the github repo to be used for deploying infrustructor and workloads to the cluster. You can go to user token options in github [here](https://github.com/settings/tokens) and generate a classic token that has the repo option selected and all the recursive options for repo enabled. You can find more information on how to get a api token for github [here](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens).

## Third step - Deploy flux to cluster
### After we have the api token saved for github we can deploy flux to the cluster.
### you can deploy with this code, there will be some options you would have to change. you would have to change the owner option to your github username and its case sensitive.
```
flux bootstrap github \
  --components-extra=image-reflector-controller,image-automation-controller \
  --owner=YourGitHUbUserName \
  --repository=flux \
  --branch=main \
  --path=clusters/home \
  --personal \
  --token-auth
```
### Once you run this command it will ask for your token for github and you would go ahead and paste it in even though it doesnt show it pasted it should be typed in and you can press enter and see it deploy flux to your cluster and create a repo for you.
### The newly made repo from flux for your cluster should have a directory like this.
```
└── clusters
    └── home
        └── flux-systems
            ├── gotk-components.yaml
            ├── gotk-sync.yaml
            └── kustomization.yaml
```
### once the repo is created I like to copy the files from this repo directory to the newely created repo from flux. And the new directory should look like this
```
├── apps
├── infrastructure
│   ├── configs
│   └── controllers
└── clusters
    └── home
        └── flux-systems
        │   ├── gotk-components.yaml
        │   ├── gotk-sync.yaml
        │   └── kustomization.yaml
        ├── apps.yaml
        └── infrastructure.yaml
```
### once these files are copied over to the new repo when we need to deploy an workload and needs some additional setup Flux will follow this paths of folders when deploying ```infrastructure/config``` > ```infrastructure/controllers``` > ```apps``` 
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
## Fourth step - Clone flux repo to host computer
### once the repo is created we would need to clone the repo to the root folder of this repo we are working with. Since its a private repo we have to change the git clone command to have the new repo cloned. We need to add ```{github-api-token}@``` before github.com in the git clone command. replacing ```{github-api-token}``` 
### so for my example it would go from this
```
git clone https://github.com/Zacarrie/flux.git
```
### to this
```
git clone https://{github-api-token}@github.com/Zacarrie/flux.git
```
### now if i ```ls``` i should see a ```flux``` folder listed

## Fifth step - Edit structure of the new repo
### I like to setup the new repo to something simillar in the FluxCD example repo you can find [here](https://github.com/fluxcd/flux2-kustomize-helm-example/tree/main). I already have the folder structure I like to use here in this repo and we can go ahead and copy it over with these commands.
```
cp -a fluxcd/. {name-of-flux-repo}
```
### so for my example the command i would run is ```cp -a fluxcd flux``` after the files are copied over we need to commit the changes to the repo so that flux knows the new layout
### before we can push the changes we have to cd into the directory of the flux repo
```
cd {name-of-flux-repo}
```
### now we can add all of the new files to the commit
```
git add .
```
### commit the changes with a message. It might ask you to setup your git config user.email & user.name. It will show you how to set that up and afterwords you can run this command again
```
git commit -m "Change repo structure from guide"
```
### push the changes to the main branch
```
git push origin main
```
### Then go back to the original directory
```
cd ..
```
### after that the repo should be setup and the new structure of your repo should look like this
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
### once your flux repo looks like this when we need to deploy an workload that needs some additional setup Flux will follow this order of operations of folders when deploying ```infrastructure/config``` > ```infrastructure/controllers``` > ```apps``` 
### so now when you want to deploy an app that needs a helm chart repo and namespace you can put that in the ```infrastructure/config``` and it will deploy them before your manifest in the apps folder. In the apps folder i like to seperate the apps into their own namespace folders

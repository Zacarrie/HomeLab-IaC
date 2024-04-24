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
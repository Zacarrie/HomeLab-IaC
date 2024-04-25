# *Inprogress*
## HomeLab-k3s-gitops
### This is currently a work in progress as im currently setting up my homelab k3s Cluster

## getting ready
### On your host machine you are wanting to orchastrate your cluster from follow these commands if you are on linux.
```
git clone https://github.com/Zacarrie/HomeLab-k3s-gitops.git
```
```
cd HomeLab-k3s-gitops
```
### After you are in the root of the cloned repo all commands posted can be ran from this directory
### All the steps have their own readme in their directories

## [First step - VM's or baremetal nodes](https://github.com/Zacarrie/HomeLab-k3s-gitops/tree/main/autoinstall_iso)
### The Firest step is setting up VM's or baremetal cluster machines you can set these up how ever you like but i will be running my cluster on ubuntu server vm's and you can find how to make auto isntall iso's for easy deployment [here](https://github.com/Zacarrie/HomeLab-k3s-gitops/tree/main/autoinstall_iso)

## [Second step - Installing ansible](https://github.com/Zacarrie/HomeLab-k3s-gitops/tree/main/ansible)
### The second step is setting up ansible on your machine you are wanting to deploy kubernetes from not the machine you are wanting kubernetes installed to and you can find the setup [here](https://github.com/Zacarrie/HomeLab-k3s-gitops/tree/main/ansible)

## [Third step - Deploying k3s](https://github.com/Zacarrie/HomeLab-k3s-gitops/tree/main/k3s)
### The third step is deploying k3s to the vm's or machines you are wanting to duploy kubernetes onto and you can find the steps [here](https://github.com/Zacarrie/HomeLab-k3s-gitops/tree/main/k3s)

## [Fourth step - Deploying FluxCD to k3s cluster](https://github.com/Zacarrie/HomeLab-k3s-gitops/tree/main/fluxcd)
### The fourth step is installing FluxCD and deploying it to the k3s cluster and editing the repo structre to have it to where you can deploy services directly from your github repo [here](https://github.com/Zacarrie/HomeLab-k3s-gitops/tree/main/fluxcd)
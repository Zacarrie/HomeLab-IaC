# Install k3s on VM's with Ansible 
This folder is how you install k3s on your nodes that you have setup. I did not make this I actually got this from [techno-tim's github](https://github.com/techno-tim/k3s-ansible) after watching his [YouTube video](https://www.youtube.com/watch?v=CbkEWcUZ7zM) on how to install k3s with ansible.
## After you have setup ansible on the host machine these are the next steps

### Change Varibles in inventory folder
The first steps is to change the settings of your cluster install with the files in the Inventory folder. There is a sample folder in the directory that you can copy over to the my-cluster folder then change the settings to work with your setup. To copy the sample folder over you can run this command.
```
cp -R k3s/inventory/sample k3s/inventory/my-cluster
```
In the ```k3s/inventory/my-cluster/group_vars/all.yml``` file this are the varibles that you are going to want to change to work in your setup.
```
nano k3s/inventory/my-cluster/group_vars/all.yml
```
```
ansible_user: ansibleuser //change to a user on your cluster

ansible_become_password: ansibleuser_password //optional to auto type password to connect to cluster nodes

system_timezone: "Your/Timezone" //change this to your timezone

flannel_iface: "eth0" //change this to your network interface find with 'ip address' command

apiserver_endpoint: "192.168.30.222" //change this to a ip you want your kubernetes api address as

k3s_token: "some-SUPER-DEDEUPER-secret-password" //change this to a secret token for your cluster

metal_lb_ip_range: "192.168.30.80-192.168.30.90" //change this to a ip range you want your services in your cluster exposed on.
```
Then in the ```k3s/inventory/my-cluster/hosts.ini``` file this is where you want to add your ip addresses of your nodes and make sure to put the right addresses under the bracketed roles. for example.
```
nano k3s/inventory/my-cluster/hosts.ini
```
```
[master]
192.168.30.38 ansible_ssh_user=ansible_usernmame ansible_ssh_pass=ansible_password
192.168.30.39
192.168.30.40

[node]
192.168.30.41
192.168.30.42

[k3s_cluster:children]
master
node
```

### After inventory folder is done you are set to install the cluster on the nodes
On your host machine while you are in this folder run this command and ansible will install k3s, kube-vip, and MetalLB on your cluster.
```
ansible-playbook k3s/site.yml -i k3s/inventory/my-cluster/hosts.ini
```

### How to control your kubernetes cluster from your host machine
First make a file on the host machine to copy the config over to
```
mkdir ~/.kube
```
```
touch ~/.kube/config
```
To copy your `kube config` locally so that you can access your **Kubernetes** cluster run:
```
scp {ansible_user}@{ip-of-a-master-node}:~/.kube/config ~/.kube/config
```
To see if you are able to control your cluster run this command to get a list of your nodes
```
kubectl get nodes
```
you should get a output similar to
```
NAME           STATUS   ROLES                  AGE   VERSION
kubernetesvm   Ready    control-plane,master   14m   v1.29.2+k3s1
```

### How to remove k3s from the cluster
```
ansible-playbook k3s/reset.yml -i k3s/inventory/my-cluster/hosts.ini
```




# Automated build of HA k3s Cluster with `kube-vip` and MetalLB (ORIGINAL README)

![Fully Automated K3S etcd High Availability Install](https://img.youtube.com/vi/CbkEWcUZ7zM/0.jpg)

This playbook will build an HA Kubernetes cluster with `k3s`, `kube-vip` and MetalLB via `ansible`.

This is based on the work from [this fork](https://github.com/212850a/k3s-ansible) which is based on the work from [k3s-io/k3s-ansible](https://github.com/k3s-io/k3s-ansible). It uses [kube-vip](https://kube-vip.io/) to create a load balancer for control plane, and [metal-lb](https://metallb.universe.tf/installation/) for its service `LoadBalancer`.

If you want more context on how this works, see:

📄 [Documentation](https://technotim.live/posts/k3s-etcd-ansible/) (including example commands)

📺 [Watch the Video](https://www.youtube.com/watch?v=CbkEWcUZ7zM)

## 📖 k3s Ansible Playbook

Build a Kubernetes cluster using Ansible with k3s. The goal is easily install a HA Kubernetes cluster on machines running:

- [x] Debian (tested on version 11)
- [x] Ubuntu (tested on version 22.04)
- [x] Rocky (tested on version 9)

on processor architecture:

- [X] x64
- [X] arm64
- [X] armhf

## ✅ System requirements

- Control Node (the machine you are running `ansible` commands) must have Ansible 2.11+ If you need a quick primer on Ansible [you can check out my docs and setting up Ansible](https://technotim.live/posts/ansible-automation/).

- You will also need to install collections that this playbook uses by running `ansible-galaxy collection install -r ./collections/requirements.yml` (important❗)

- [`netaddr` package](https://pypi.org/project/netaddr/) must be available to Ansible. If you have installed Ansible via apt, this is already taken care of. If you have installed Ansible via `pip`, make sure to install `netaddr` into the respective virtual environment.

- `server` and `agent` nodes should have passwordless SSH access, if not you can supply arguments to provide credentials `--ask-pass --ask-become-pass` to each command.

## 🚀 Getting Started

### 🍴 Preparation

First create a new directory based on the `sample` directory within the `inventory` directory:

```bash
cp -R inventory/sample inventory/my-cluster
```

Second, edit `inventory/my-cluster/hosts.ini` to match the system information gathered above

For example:

```ini
[master]
192.168.30.38
192.168.30.39
192.168.30.40

[node]
192.168.30.41
192.168.30.42

[k3s_cluster:children]
master
node
```

If multiple hosts are in the master group, the playbook will automatically set up k3s in [HA mode with etcd](https://rancher.com/docs/k3s/latest/en/installation/ha-embedded/).

Finally, copy `ansible.example.cfg` to `ansible.cfg` and adapt the inventory path to match the files that you just created.

This requires at least k3s version `1.19.1` however the version is configurable by using the `k3s_version` variable.

If needed, you can also edit `inventory/my-cluster/group_vars/all.yml` to match your environment.

### ☸️ Create Cluster

Start provisioning of the cluster using the following command:

```bash
ansible-playbook site.yml -i inventory/my-cluster/hosts.ini
```

After deployment control plane will be accessible via virtual ip-address which is defined in inventory/group_vars/all.yml as `apiserver_endpoint`

### 🔥 Remove k3s cluster

```bash
ansible-playbook reset.yml -i inventory/my-cluster/hosts.ini
```

>You should also reboot these nodes due to the VIP not being destroyed

## ⚙️ Kube Config

To copy your `kube config` locally so that you can access your **Kubernetes** cluster run:

```bash
scp debian@master_ip:/etc/rancher/k3s/k3s.yaml ~/.kube/config
```
If you get file Permission denied, go into the node and temporarly run:
```bash
sudo chmod 777 /etc/rancher/k3s/k3s.yaml
```
Then copy with the scp command and reset the permissions back to:
```bash
sudo chmod 600 /etc/rancher/k3s/k3s.yaml
```

You'll then want to modify the config to point to master IP by running:
```bash
sudo nano ~/.kube/config
```
Then change `server: https://127.0.0.1:6443` to match your master IP: `server: https://192.168.1.222:6443`

### 🔨 Testing your cluster

See the commands [here](https://technotim.live/posts/k3s-etcd-ansible/#testing-your-cluster).

### Troubleshooting

Be sure to see [this post](https://github.com/techno-tim/k3s-ansible/discussions/20) on how to troubleshoot common problems

### Testing the playbook using molecule

This playbook includes a [molecule](https://molecule.rtfd.io/)-based test setup.
It is run automatically in CI, but you can also run the tests locally.
This might be helpful for quick feedback in a few cases.
You can find more information about it [here](molecule/README.md).

### Pre-commit Hooks

This repo uses `pre-commit` and `pre-commit-hooks` to lint and fix common style and syntax errors.  Be sure to install python packages and then run `pre-commit install`.  For more information, see [pre-commit](https://pre-commit.com/)

## 🌌 Ansible Galaxy

This collection can now be used in larger ansible projects.

Instructions:

- create or modify a file `collections/requirements.yml` in your project

```yml
collections:
  - name: ansible.utils
  - name: community.general
  - name: ansible.posix
  - name: kubernetes.core
  - name: https://github.com/techno-tim/k3s-ansible.git
    type: git
    version: master
```

- install via `ansible-galaxy collection install -r ./collections/requirements.yml`
- every role is now available via the prefix `techno_tim.k3s_ansible.` e.g. `techno_tim.k3s_ansible.lxc`

## Thanks 🤝

This repo is really standing on the shoulders of giants. Thank you to all those who have contributed and thanks to these repos for code and ideas:

- [k3s-io/k3s-ansible](https://github.com/k3s-io/k3s-ansible)
- [geerlingguy/turing-pi-cluster](https://github.com/geerlingguy/turing-pi-cluster)
- [212850a/k3s-ansible](https://github.com/212850a/k3s-ansible)

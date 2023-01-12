# HomeLab-IaC
# This is current a work in progress as im current setting up ansible pull to create a HA k3s Cluster

## Things to do to setup a node
update the node
```
sudo apt update
```
```
sudo apt upgrade
```

Install ansible
```
sudo apt install ansible
```

Pull Ansible command
```
ansible-pull -U https://github.com/Zacarrie/HomeLab-IaC.git
```
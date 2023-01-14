# Setting up Ansible and VM's
## Steps to setup host machine / computer being used to setup your cluster
For my "host machine" I am using wsl on my windows computer.
### First step is to install ansible on the host machine.
#### Update the host machine
```
sudo apt update
```
```
sudo apt upgrade
```
#### Install ansible to the host machine
```
sudo apt install ansible
```
#### Install requirments needed for ansible
```
ansible-galaxy collection install -r ./collections/requirements.yml
```
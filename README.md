# Ansible deployment of Kubernetes to a cluster of Raspberry PIs

## Installation requirements
- Oracle VirtualBox installed on your local system
- Git
- Web connection

I typically build a simple VM on my laptop (in Oracle VirtualBox) to become the provisioning 
system.  That VM is usually a simple Ubuntu installation.  To get started, login to the Ubuntu
VM you created and follow the steps below:

## 1. Clone the repository into your Ubuntu VM
```
git clone https://github.com/garyvalley/ansible-k3s
```

## 2. Ensure you know the IP addresses of your RPI (Raspberry PI) hosts

## 3. Update the inventory to match your host IPs and desired names
The format of the inventory file is given here
```
<group name>
  hosts:
    <host name 1>:
	    ansible_host: <ip address 1>
	    ansible_user: <ubuntu>
    <host name 2>:
	    ansible_host: <ip address 2>
	    ansible_user: <ubuntu>
  children:
    k3s_master:
      hosts:
        <host_name>
    k3s_nodes:
      hosts:
        <host_name>
```

All components inside a "&lt;&gt;" tag are configurable and should be updated to 
match the environment.

## 4. Ensure the ansible.cfg file points at the appropriate path for your system

## 5. Generate SSH keypairs from the ansible-controller to the hosts
On the ansible-controller (i.e. the system where you have cloned this git repository 
to), get a ssh key:

```
sudo apt update
sudo apt upgrade
ssh-keygen
```

Next, copy that id to all the hosts in the cluster. For my four node installation, I did this 
manually, but a bash script would make sense if the number of nodes grows.
```
ssh-copy-id -i ~/.ssh/id_rsa ubuntu@<host>
```

Verify that you can connect to a server (by ip) in the cluster, but without a password
```
ssh ubuntu@<host>
```

## 6. Run the setup command
Run the setup command on the provisioning box (Ubuntu VM) to build the RPI cluster
```
ansible-playbook setup.yml
```

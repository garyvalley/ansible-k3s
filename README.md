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
	<host name 2>:
	    ansible_host: <ip address 2>
    vars:
	ansible_user: ubuntu
```

All components inside a "&lt;&gt;" tag are configurable and should be updated to 
match the environment.

## 4. Ensure the ansible.cfg file points at the appropriate path for your system

## 5. Run the setup command
Run the setup command on the provisioning box (Ubuntu VM) to build the RPI cluster
```
ansible-playbook setup.yml
```

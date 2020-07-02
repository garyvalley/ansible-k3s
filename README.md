# Ansible deployment of Kubernetes to a cluster of Raspberry PIs
This project is an automated deployment of Rancher's K3s (lightweight kubernetes) on
a cluster of Raspberry Pi's (RPIs).  This has been tested with Raspberry Pi 3B+ and 4B 
systems (but not earlier models). These RPIs were configured with 64Gb SD cards.  The
following setup should produce an automated working k3s cluster:

## 1. Clone the repository into your deployment machine
```
git clone https://github.com/garyvalley/ansible-k3s
```

## 2. Set up your raspberry pi hosts
In the test environment, the RPIs are hardwired to a Netgear 5-port switch and a USB
hub to power them.  Then, the following is required:

  ### Flash each RPI
  I used the Balena Etcher tool (free) to flash the SD cards with the Ubuntu 20.04 LTS for ARM
  operating system that I downloaded from [here] (https://ubuntu.com/download/raspberry-pi). I used
  a USB SD card reader/writer to do this.  

  ### Set up your RPIs
  Ensure you know the IP addresses of your RPI (Raspberry PI) hosts. (I found mine by using 
  their MAC addresses and looking them up in the Google Wifi list.)  You'll need these to connect 
  to each RPI. Once you have those IP addresses, you'll need to do the following on each host:
  1. SSH to each RPI. 
  ```
  ssh ubuntu@<ip_address>
  ```
  Ubuntu 20.04 will make you change the password on the first login. (The default
  username and password is ubuntu/ubuntu.) You should change this to a password you know.

  2. Wait for a bit. Ubuntu 20.04 will automatically start updating itself upon receiving an
  internet connection. This might take a while on RPIs. If you know how, you can log on to the
  systems and do this manually. If not, simply waiting will complete the task. (Don't worry. The
  automated install will guarantee updated/upgraded packages.)  

  3. Login to each RPI (using the password you created in step 1) and reboot them.
  ```
  ssh ubuntu@<ip_address>
  sudo reboot
  ```

## 3. Update the inventory to match your host IPs and desired names
The format of the inventory file is given here
```
---
<group_name>
  hosts:
    <host_name_1>:
	    ansible_host: <ip_address_1>
	    ansible_user: <ubuntu>
    <host name 2>:
	    ansible_host: <ip_address_2>
	    ansible_user: <ubuntu>
    ...
  children:
    k3s_master:
      hosts:
        <host_name_1>
    k3s_nodes:
      hosts:
        <host_name_2>
        ...
```

All components inside a "&lt;&gt;" tag are configurable and should be updated to 
match the environment. My inventory list looks like this:
```
---
all:
  hosts:
    master:
      ansible_host: 192.168.86.100
      ansible_user: ubuntu
      hostname: master
    node1:
      ansible_host: 192.168.86.101
      ansible_user: ubuntu
      hostname: node1
    node2:
      ansible_host: 192.168.86.102
      ansible_user: ubuntu
      hostname: node2
    node3:
      ansible_host: 192.168.86.103
      ansible_user: ubuntu
      hostname: node3
  children:
    k3s_master:
      hosts:
        master:
    k3s_nodes:
      hosts:
        node1:
        node2:
        node3:
```

## 4. Ensure the ansible.cfg file points at the appropriate path for your system.
You'll want to edit the ansible.cfg file (in the top level directory of the git
repo) to point to where you've stored the inventory file you created in step 3 as well
as the location of the roles directory.  If you continue with the structure from
the git repository you needn't change anything here.

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

## 6. Run the setup playbook
Run the setup command on the deployment system to build the RPI cluster
```
ansible-playbook setup.yml
```

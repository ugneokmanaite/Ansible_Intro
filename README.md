
# Infrastructure as Code IAC

## Why do we use it?
To help speed up the process of configuration management or orchestration 
- For config management we use Ansible
- For orchestration we use terraform

## How does it speed the process?
- By creating a script in YML using Ansible


![image](https://trello-attachments.s3.amazonaws.com/5ee361944edc9009544b535d/5f310e992d8c171f82cea510/44bea8bf1d4a82a875c9c92bd3a752d9/Screenshot_(191).png)
- Many servers running all around the world in the cloud
- - YMAL = we can create one file in the controller and define what items we want to install in one file, and then we would like to update both servers (VMs with the relevant things)


## What is Ansible ?
- Automation tool for configuration management

## Why should we use Ansible?
- its simple, agentless, IT automation tools

- Agentless? = we do not need to install ansible in app, or db servers, we just need it in the controller and connectivity from controller to the servers

- Allows us to connect to any server in the world using ssh

## How so we connect?
- connect using ssh = related to simplicity 
- 3 VMs- 1 will be a virtual controller , app and db

## What are the dependencies?
- Python in both VM App and VM db (ubuntu 16.04 has python configured already)

## How does it fit into DevOps/ Benefit DevOps?
- Saves time, open source, makes config management predictable, cost effective
- it automates the process of configuration management 
- open source = we dont need to buy it = provides detailed documentation online for ease of use
- does not diffrentiate between different providers as long as you have the correct script written

## Step 1: Created a Vagrantfile & README.md

## Step 2: `vagrant up`

## Step 3: ssh into all VMs and run `sudo apt-get update` to check the connectivity of internet for all 3

## Step 4: Now we need to use one VM as the Controller
- we will use AWS for this 
- therefore need to install Ansible on chosen controller
- look at the file system on controller using tree
- consider how to create hoest entries 
- tell controller which IPs to communicate with

## Step 5: Go inside controller VM and run relevant installation commands

```
vagrant ssh aws
sudo apt-get install software-properties-common -y
sudo apt-add-repository ppa:ansible/ansible
sudo apt-get update
sudo apt-get install ansible -y
```

## Step 6: install tree to view folders in an organised way
`sudo apt-get install tree`

## Step 7: enter inside ansible folder
`cd /etc/ansible` and enter `tree` command

## Step 8: Test if other VM IPs are reachable (Testing network connectivity)

```
ping 192.168.33.11
ping 192.168.33.10
```

- should respond with pong if successful

## Go into hosts file and edit to enter the IP details of the VMs that will be used from controller
```
[web]
192.168.33.10 ansible_connection=ssh ansible_ssh_user=vagrant ansible_ssh_pass=vagrant
[db]
192.168.33.11 ansible_connection=ssh ansible_ssh_user=vagrant ansible_ssh_pass=vagrant
```

- we have told the hosts file that these are the 2 servers that we would like ansible controller to communicate with

## Step 9: SSH into the VMs 

```
ssh vagrant@IP

sudo apt-get update

exit
```

- do this to ensure you can connect to the VMs and they have internet connectivity

## Step 10: Ping these machines with ansible command

```
sudo ansible all -m ping

```
- checks network connectivity


** ERROR = AWS VM WAS CORRUPT SO HAD TO USE WEB AS THE CONTROLLER AND AWS AS WEB **
** HAD TO DELETE ANSIBLE FOLDER MANUALLY IN VAGRANT AND REINSTALL ON THE WEB VM IN ORDER TO USE AS CONTROLLER
```
echo /home/vagrant/.ansible/tmp/ansible-tmp-15
```

## Step 11: Ansible ad-hoc commands

### Why should we use them and not playbook?
- its easy, fast, robust with one command we can find out space, what is already installed etc

```
ansible aws -a "date"
ansible db -a "uname -a"
ansible all -a "ls -a"
ansible all -m shell -a "ls -a"
ansible all -m shell -a "free"
ansible all -m shell -a 'uptime' --become
ansible all -m shell -a "env"
[displays all env variables]
ansible all -m shell -a "ip addr"
[displays all ip addresses]
ansible all -m shell -a 'ps -ef' 
ansible all -m shell -a 'ps -a'
ansible all -m shell -a "ifconfig"
ansible db -m shell -a "hostname -I"
[returns private and public ip nothing else]
```

## Step 12: Ansible playbook

### What are they?
- YAML (Yes another markup language) .yml or .ymal
- Units of scripts which describe work to configure server/servers 

### Syntax
- `---` starts with 3 dashes
- indentation is super important in YAML

### Widely used in DevOps Practices!!

### Why should we use them/benefits?
- for configuration management - install programs, update programs etc
- why over ad hoc commands? it automates the tasks in multiple servers 

## Step 13: Creating a playbook

1. in ansible folder create a new file
```
sudo nano nginx install_nginx_on_web.yml
```

2. Edit the file
```
# this is YAML file to install nginx in our web server

---

# where do we want to install
- hosts: aws

# get the facts
  gather_facts: yes

# work from root user
  become: true

# what do we want ansible to do for us in this playbook?
  tasks:
  - name: Install nginx

# telling ansible which package to install and the state of it - states are: present/absent
    apt: pkg=nginx state=present
```

3. `ansible-playbook install_nginx_on_web.yml`

4. Run the IP given (web VM) on browser

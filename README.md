# Project-Deployment
This is the project to practice DevOps process, where we will do hands-on of deployment (as used in the real-time projects in the industry)

## Pre-requisites
1. Two VMs with Ubunto 20.04 installation

## Tools need to be use
1. Jenkins
2. Ansible
3. Nexus

------------------
Lets install Ansible on first VM (master) and check the version with the following commands:
```
sudo apt update
```
```
sudo apt install ansible
```
```
version --ansible
```

<img width="782" alt="ansible-version" src="https://github.com/user-attachments/assets/5e35e9b4-eb00-424e-833e-a90789f2344f" />

## Task: Set Up SSH Key-Based Authentication
This task allows Ansible to connect securely to remote machines.

Step 1: Generate an SSH Key on the Ansible Control Node
Run this command on the VM where Ansible is installed:
```
ssh-keygen -t rsa -b 4096
```
Step 2: Copy the SSH Key to the Target (Managed) Node
Now, transfer the SSH key to the remote VM (the target machine Ansible will manage). Replace 192.168.1.100 with your remote VM’s actual IP:
```
ssh-copy-id akshay@192.168.84.131
```
* It will ask for the password of the remote ubuntu user. Enter it.
* Once the key is copied, future SSH logins will be passwordless.

Step 3: Verify Passwordless SSH Access
Test if SSH works without a password:
```
ssh akshay@192.168.84.131
```
If you log in without a password prompt, it’s working!

<img width="793" alt="image" src="https://github.com/user-attachments/assets/d3239680-ac4b-4be3-af99-7c15ce1721aa" />

Next Steps: Test Ansible Connection
Run a simple ping test from your Ansible control node:
```
ansible all -m ping
```
If everything is configured correctly, you should see:

<img width="758" alt="ping-pong" src="https://github.com/user-attachments/assets/e90589f4-fa27-47ab-a272-e70400c8aaa7" />

Now your Ansible control node can communicate with the remote VM! 


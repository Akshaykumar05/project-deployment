# Project-Deployment
This is the project to practice DevOps process, where we will do hands-on of deployment (as used in the real-time projects in the industry)

## Pre-requisites
1. Two VMs with Ubunto 20.04 installation

## Tools need to be use
1. Jenkins
2. Ansible
3. Nexus
4. Tomcat

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

## Configure Ansible Inventory (hosts file)
Edit the Ansible inventory file (/etc/ansible/hosts) and add remote hosts you want to manage.
```
sudo vim /etc/ansible/hosts
```
<img width="774" alt="ansible-hosts-file" src="https://github.com/user-attachments/assets/d64b0a1f-813a-451b-a6b4-197604fcfd09" />

Now restart the ansible
```
sudo systemctl restart ansible
```

## Task: Set Up SSH Key-Based Authentication
This task allows Ansible to connect securely to remote machines.

Step 1: Generate an SSH Key on the Ansible Control Node
Run this command on the VM where Ansible is installed:
```
ssh-keygen -t rsa -b 4096
```
Step 2: Copy the SSH Key to the Target (Managed) Node
Now, transfer the SSH key to the remote VM (the target machine Ansible will manage). Replace 192.168.84.131 with your remote VM’s actual IP:
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

Example 2: Run a Simple Command
These commands remotely execute system commands on all managed nodes using Ansible.

1. Check System Uptime
```
ansible all -m command -a "uptime"
```
* Runs the uptime command on all remote machines.
* Displays how long the system has been running, number of users, and system load.

2. Check Disk Usage with File System Type
```
ansible all -m command -a "df -Th"
```
* Runs df -Th to display disk usage with file system types.
* The -T flag shows the file system type.
* The -h flag makes the output human-readable.

3. Get Hostname of Remote Machines
```
ansible all -m command -a "hostname"
```
* Runs hostname to get the hostname of each remote machine.

<img width="770" alt="ansible-commands" src="https://github.com/user-attachments/assets/b8a653e5-d32b-4afd-a98f-b057be1619fb" />

------------------
Next, we will configure Jenkins on our base. The steps are following:

![Install-Jenkins-on-Ubuntu-20 04--2-](https://github.com/user-attachments/assets/79eb17ed-eea5-4e17-814a-b4e46e878b70)

### Java Installation
Jenkins is a Java application, hence the system must have Java 8 or later installed. The open-source Java Platform implementation, OpenJDK 11, will be installed.
To install OpenJDK 11, issue the following commands as root, a user with sudo permissions, or both:
```
sudo apt update
```
```
sudo apt install openjdk-11-jdk
```
Verify that the installation was successful by looking at the Java version:
```
java -version
```
### Installing Jenkins
add the Jenkins GPG key to your Ubuntu system before installing Jenkins.
```
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null
```
The Jenkins repository should then be added to the system using:
```
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]  https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
```






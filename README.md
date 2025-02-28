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
To update the apt package list and install the most recent version of Jenkins after the Jenkins repository has been activated.
```
sudo apt update
```
```
sudo apt install -y jenkins
```
```
systenctl status jenkins
```
<img width="917" alt="jenkins-status" src="https://github.com/user-attachments/assets/d0966adc-d93b-4e5d-a8c1-1617a917e1a2" />


### Setting up Jenkins
Open your browser and type your domain or IP address followed by port 8080, http://your_ip_or_domain:8080 to configure your new Jenkins installation.

You will be prompted to enter the Administrator password that is created during the installation on a page that looks something like the following:



Now, use the cat command to display the password on the terminal:
```
cat /var/lib/jenkins/secrets/initialAdminPassword
```
You should see a 32-character long alphanumeric password, as shown below:
"""06cbf25d811a424bb236c76fd6e04c47"""

Click “Continue” after copying the administrator password from the terminal and paste it there.

The setup procedure will ask you if you want to install suggested plugins or only certain plugins on the following screen.

<img width="797" alt="jenkins-custom" src="https://github.com/user-attachments/assets/f00394b5-6409-44e2-be5d-5f7344e10725" />

-----------------
## Tomcat installation on Other Server (VM-2)
We need to install and setup Tomcat on the different server.
.
.
.
.
* Start the tomcat
  ```
  sudo /opt/tomcat/bin/startup.sh
  ```
  <img width="704" alt="image" src="https://github.com/user-attachments/assets/7fb0f1a4-c39c-43ed-ab9e-06b5fae2674e" />

These scripts are used to start, stop and, otherwise manage the Tomcat instance.

### Creating SystemD Unit File
Instead of using the shell scripts to start and stop the Tomcat server, we’ll set it to run as a service.
Open the text editor and create a ```tomcat.service``` unit file in the ```/etc/systemd/system/``` directory:
```
sudo vim /etc/systemd/system/tomcat.service
```
Paste the following configuration:
```
[Unit]
Description=Tomcat 9 servlet container
After=network.target

[Service]
Type=forking

User=tomcat
Group=tomcat

Environment="JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64"
Environment="JAVA_OPTS=-Djava.security.egd=file:///dev/urandom -Djava.awt.headless=true"

Environment="CATALINA_BASE=/opt/tomcat"
Environment="CATALINA_HOME=/opt/tomcat"
Environment="CATALINA_PID=/opt/tomcat/temp/tomcat.pid"
Environment="CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC"

ExecStart=/opt/tomcat/bin/startup.sh
ExecStop=/opt/tomcat/bin/shutdown.sh

Restart=always
SuccessExitStatus=143

[Install]
WantedBy=multi-user.target
```
Save and close the file and notify systemd that a new unit file exists:
```
sudo systemctl daemon-reload
```
Enable and start the Tomcat service:
```
sudo systemctl enable --now tomcat
```
Check the service status:
```
sudo systemctl status tomcat
```
The output should show that the Tomcat server is enabled and running:

<img width="797" alt="image" src="https://github.com/user-attachments/assets/3fab1163-3429-42e2-a91a-a963bd6c478f" />

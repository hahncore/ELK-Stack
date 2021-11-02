# ELK-Stack
A secure Azure server using ELK
## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

[Diagrams/CloudDiagram.drawio.png](https://github.com/hahncore/ELK-Stack/blob/7eff5256a7fe0f0cdeca18d2db0b5f47f8cc9ad0/Diagrams/CloudDiagram.drawio.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the playbook file may be used to install only certain pieces of it, such as Filebeat.
 
[Playbooks/install-elk.yml](https://github.com/hahncore/ELK-Stack/blob/cdcd0db3494974ebdfeff77ac0f4cc570208df7e/Playbooks/install-elk.yml)

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build

### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly redundant, in addition to restricting traffic to the network.
- The Load Balancer adds additional layers of security from emerging threats such as DDoS attacks and authenticates user access. In addition to the added layer of security         provided by the Load Balancer, the Jump Box acts as a "bridge" between two trusted networks and is treated as a single entryway to a server group. 

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the data and system logs.
- Filebeat: Watches and records log files from locations specified and forwards them to Logstash for indexing.
- Metricbeat: Is used to monitor and collect data such as system CPU, memory and load in a docker environment.

The configuration details of each machine may be found below.

| Name      | Function  |       IP Address       | Operating System |
|-----------|-----------|------------------------|------------------|
| Jump Box  | Gateway   | 20.115.1.109/10.0.0.1  |      Linux       |
| Web-1     | WebServer |        10.0.0.9        |      Linux       |
| Web-2     | WebServer |        10.0.0.10       |      Linux       |
| Web-3     | WebServer |        10.0.0.12       |      Linux       |
| Elk-Server| Elk Stack | 104.43.249.22/10.1.0.6 |      Linux       |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the JumpBox Provisioner machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- 47.45.69.169

Machines within the network can only be accessed by JumpBox Provisioner.
- I allowed my JumpBox Provisioner to access the ELK-VM.
- 10.0.0.1

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box |         Yes         |     47.45.69.169     |
|  Elk-VM  |         No          |     47.45.69.169     |
|   Web-1  |         No          |       10.0.0.8       |
|   Web-2  |         No          |       10.0.0.8       |
|   Web-3  |         No          |       10.0.0.8       |


### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because it allows us to do complex routine tasks and have them automated, saving the person time and less risk of error for repeated tasks.

The playbook implements the following tasks:
- Install Docker.io
- Install Python3-pip
- Install Docker module
- Increase virtual memory
- Use more memory
- Download and launch a docker elk container
- Enable service docker on boot

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

[Images/Elk-Server Docker PS](https://github.com/hahncore/ELK-Stack/blob/6c87af561b2242440b3f6bb0be31ad721a1571b3/Images/Elk-Server%20Docker%20PS.PNG)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- Web-1 (10.0.0.9)
- Web-2 (10.0.0.10)
- Web-3 (10.0.0.12)

We have installed the following Beats on these machines:
- Filebeats
- Metricbeats

These Beats allow us to collect the following information from each machine:
- Logbeats allows us to monitor the log files or locations that are specified to collect log events to be forwarded for indexing. 
- Metricbeats helps us monitor our servers metrics and statistics such as inbound and outbound traffic that can output such as Logstash.

### Using the Playbook

In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the filebeat-config.yml file to /etc/ansible.
- Update the filebeat-config.yml file to include the Elk-Server's IP at line 1106 and 1806.
- Run the playbook, and navigate to http://104.43.249.22:5601 to check that the installation worked as expected.

- Copy the metricbeat-config.yml file to /etc/ansible.
- Update the metricbeat-config.yml file to include the Elk-Server's IP at line 62 and 95.
- Run the playbook, and navigate to http://104.43.249.22:5601 to check that the installation worked as expected.

- Name: filebeat-playbook.yml, located in /etc/ansible/roles
- Run the file located in /etc/ansible/hosts to run the playbook on a specific machine. Specify in the /etc/ansible/hosts file the IP addresses of your servers under "[webservers]" and be sure to remove the "#" comment. Specify in /etc/ansible/hosts file the IP address of your Elk-Server under "[elk]" and be sure to remove the "#" comment.
- Navigate to http://104.43.249.22:5601 to see that it is running. 

- Name: metricbeat-playbook.yml, located in /etc/ansible/roles
- Run the file located in /etc/ansible/hosts to run the playbook on a specific machine. Specify in the /etc/ansible/hosts file the IP addresses of your servers under "[webservers]" and be sure to remove the "#" comment. Specify in /etc/ansible/hosts file the IP address of your Elk-Server under "[elk]" and be sure to remove the "#" comment.
- Navigate to http://104.43.249.22:5601 to see that it is running. 

Filebeat
- Run 'curl https://gist.githubusercontent.com/slape/5cc350109583af6cbe577bbcc0710c93/raw/eca603b72586fbe148c11f9c87bf96a63cb25760/Filebeat > /etc/ansible/filebeat-config.yml'   to download the filebeat-config.yml file.
- Edit the file by running 'nano filebeat-config.yml'
- Add the internal IP of the Elk-Server and specify port 5601.
- Create the playbook by running 'nano filebeat-playbook.yml' and write the following script;

---
- name: installing and launching filebeat
  hosts: webservers
  become: yes
  tasks:
  
  - name: download filebeat deb
    command: curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.4.0-amd64.deb
    
  - name: install filebeat deb
    command: dpkg -i filebeat-7.4.0-amd64.deb
    
  - name: drop in filebeat.yml
    copy:
      src: /etc/ansible/filebeat-config.yml
      dest: /etc/filebeat/filebeat.yml
      
  - name: enable and configure system module
    command: filebeat modules enable system
    
  - name: setup filebeat
    command: filebeat setup
    
  - name: start filebeat service
    command: service filebeat start
    
  - name: enable service filebeat on boot
    systemd:
      name: filebeat
      enabled: yes
      
 - From within the same directory, run the playbook with the following command; 'ansible-playbook filebeat.playbook.yml'
 - Navigate to http://104.43.249.22:5601 to ensure the playbook ran correctly and is working.

Metricbeat
 - Run 'curl https://gist.githubusercontent.com/slape/5cc350109583af6cbe577bbcc0710c93/raw/eca603b72586fbe148c11f9c87bf96a63cb25760/Filebeat > /etc/ansible/filebeat-config.yml'   to download the filebeat-config.yml file.
- Edit the file by running 'nano metricbeat-config.yml'
- Add the internal IP of the Elk-Server and specify port 5601.
- Create the playbook by running 'nano metricbeat-playbook.yml' and write the following script;

---
- name: installing and launching metricbeat
  hosts: webservers
  become: yes
  tasks:
  
  - name: download metricbeat deb
    command: curl -L -O https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-7.4.0-amd64.deb
    
  - name: install metricbeat deb
    command: dpkg -i metricbeat-7.4.0-amd64.deb
    
  - name: drop in metricbeat.yml
    copy:
      src: /etc/ansible/metricbeat-config.yml
      dest: /etc/metricbeat/metricbeat.yml
      
  - name: enable and configure system module
    command: metricbeat modules enable docker
    
  - name: setup metricbeat
    command: metricbeat setup
    
  - name: start metricbeat service
    command: service metricbeat start
    
  - name: enable service metricbeat on boot
    systemd:
      name: metricbeat
      enabled: yes
      
 - From within the same directory, run the playbook with the following command; 'ansible-playbook filebeat.playbook.yml'
 - Navigate to http://104.43.249.22:5601 to ensure the playbook ran correctly and is working.


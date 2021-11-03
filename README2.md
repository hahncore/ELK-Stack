# ELK-Stack
A secure Azure server using ELK
## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

[Diagrams/CloudDiagram.drawio.png](https://github.com/hahncore/ELK-Stack/blob/7eff5256a7fe0f0cdeca18d2db0b5f47f8cc9ad0/Diagrams/CloudDiagram.drawio.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the playbook file may be used to install only certain pieces of it, such as Filebeat.

[Ansible/install-elk.yml](https://github.com/hahncore/ELK-Stack/blob/4c151d48007ffa3c11cada7d141e829f2f563324/Ansible/install-elk.yml)

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
|  Elk-VM  |         No          |       10.0.0.8       |
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
- Nano into /etc/ansible/hosts to edit the "hosts" file in order for the playbook to run on the specific machine.
- Specify the IP addresses of your Web VM's under "#[webservers]" as shown below. Be sure to remove the "#" comment
  - '10.0.0.9 ansible_python_interpreter=/usr/bin/python3'
  - '10.0.0.10 ansible_python_interpreter=/usr/bin/python3'
  - '10.0.0.12 ansible_python_interpreter=/usr/bin/python3'
- Create a new "[elk]" group under the  Web VM's you just added and add the private IP as shown below of the ELK-Server.
  - '10.1.0.6 ansible_python_interpreter=/usr/bin/python3'
- Save and Exit.

Filebeat
- Run 'curl https://gist.githubusercontent.com/slape/5cc350109583af6cbe577bbcc0710c93/raw/eca603b72586fbe148c11f9c87bf96a63cb25760/Filebeat > /etc/ansible/filebeat-config.yml' to copy the filebeat-config.yml file to /etc/ansible.
- Use nano to update the filebeat-config.yml file to include the Elk-Server's IP at line 1106 and 1806 as shown below.
  - Line 1105 'hosts: ["10.1.0.6:9200"]'
  - Line 1806 'host: "10.1.0.6:5601"'
- Save and exit.

Name: filebeat-playbook.yml, located in /etc/ansible/roles
- Create the playbook by running 'nano filebeat-playbook.yml' and write the following script;

[Ansible/filebeat-playbook.yml](https://github.com/hahncore/ELK-Stack/blob/d746d38ac82030add2dc741592289a9bb2e4437a/Ansible/filebeat-playbook.yml)

 - Save and Exit.
 - From within the same directory, run the playbook with the following command; 'ansible-playbook filebeat-playbook.yml'
 - Navigate to http://104.43.249.22:5601 to ensure the playbook ran correctly and is working as shown below.

[Images/filebeat.png](https://github.com/hahncore/ELK-Stack/blob/3a2d9111d8b357ed3ea92c27613c30e7f765c5bf/Images/filebeat.PNG)

Metricbeat
- Run 'curl https://gist.githubusercontent.com/slape/58541585cc1886d2e26cd8be557ce04c/raw/0ce2c7e744c54513616966affb5e9d96f5e12f73/metricbeat > /etc/ansible/metricbeat-    config.yml' to copy the metricbeat-config.yml file to /etc/ansible.
- Use nano to update the metricbeat-config.yml file to include the Elk-Server's IP at line 62 and 95 as shown below.
  - Line 62 - 'host: "10.1.0.6:5601"'
  - Line 95 - 'hosts: "[10.1.0.6:9200"]'
- Save and exit.

Name: metricbeat-playbook.yml, located in /etc/ansible/roles
- Create the playbook by running 'nano metricbeat-playbook.yml' and write the following script;

[Ansible/metricbeat-playbook.yml](https://github.com/hahncore/ELK-Stack/blob/d746d38ac82030add2dc741592289a9bb2e4437a/Ansible/metricbeat-playbook.yml)

- Save and exit.
- From within the same directory, run the playbook with the following command; 'ansible-playbook metricbeat-playbook.yml'
- Navigate to http://104.43.249.22:5601 to ensure the playbook ran correctly and is working as shown below.

[Images/metric.png](https://github.com/hahncore/ELK-Stack/blob/3a2d9111d8b357ed3ea92c27613c30e7f765c5bf/Images/metric.PNG)

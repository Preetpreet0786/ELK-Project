## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

https://docs.google.com/document/d/1mVh8iOm8a_TyygRS1GtFgOjpHQHIGL3_K2tBo7jtLuM/edit?usp=sharing

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the playbook-yml file may be used to install only certain pieces of it, such as Filebeat.

Enter the playbook file
---
- name: Configure Elk VM with Docker
  hosts: ELKserver
  remote_user: sysadmin
  become: true
  tasks:
    #Use apt module
    - name: Install docker.io
      apt:
        update_cache: yes
        force_apt_get: yes
        name: docker.io
        state: present

     #Use apt module
    - name: Install python3-pip
      apt:
        force_apt_get: yes
        name: python3-pip
        state: present

      #Use pip module (It will default to pip3)
    - name: Install Docker module
      pip:
        name: docker
        state: present

       #Use sysctl module
    - name: Use more memory
      sysctl:
        name: vm.max_map_count
        value: "262144"
        state: present
        reload: yes

      #Use docker_container module
    - name: download and launch a docker elk container
      docker_container:
        name: elk
        image: sebp/elk:761
        state: started
        restart_policy: always
        #Please list the ports that ELK runs on
        published_ports:
          -  5601:5601
          -  9200:9200
          -  5044:5044
     
    - name: Enable docker service 
      systemd: 
        name: docker
        enabled: yes

This document contains the following details:
- Description of the Topologu
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting unsecure traffic to the network.
What aspect of security do load balancers protect? What is the advantage of a jump box?_
The load balancers protect organizations against DDos (denial of service attack ).The advantage of jump box it prevents all azure VM to expose to the public and we  use this jump box VM to connect to other azure VM using dynamic IP

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the _____ and system _____.
What does Filebeat watch for?
The Filebeat monitor the logfiles ,collect log events and forward them to elasticsearch or logstash for indexing 
What does Metricbeat record?
Metrcibeat preodically collects metrics from the operating system and from services running on the server.
The configuration details of each machine may be found below.
_Note: Use the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables) to add/remove values from the table_.

| Name     | Function | IP Address | Operating System |
|----------|----------|------------|------------------|
| Jump Box | Gateway  | 10.0.0.4   | Linux            |
| WEB1     |  DVWA    | 10.0.0.7   | Linux            |
| WEB2     |  DVWA    | 10.0.0.8   | Linux            |
| ELK      |  ELK     | 10.1.0.4   | Linux            |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Jumpbox machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
From my home IP address 141.168.83.125
Machines within the network can only be accessed by Jumpbox VM
Which machine did you allow to access your ELK VM? What was its IP address?
Jumpbox VM can access ELK VM and its IP address is 10.0.0.4

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box | no                  | 141.168.83.125       |
|  Web1    | no                  | 10.0.0.7             |
|  Web2    | no                  | 10.0.0.8             |
|  ELK.    | no                  | 10.0.0.4             |
### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous 
What is the main advantage of automating configuration with Ansible?
The main advantages of automating configuration with ansible are 
1.Open Source tool
2.Easy to use
3.Easily configure multiple servers
4.Enables automation

Powerful & FlexibleThe playbook implements the following tasks:
- _TODO: In 3-5 bullets, explain the steps of the ELK installation play. E.g., install Docker; download image; etc._
These are steps for ELK installation 

1.Create New Vnet and create peer network connection between 2 Vnet and new VM called ELK server
2.Configure Inbound and outbound security rules
3.Download and configure Docker
4.Configure elk-playbook.yml
5.Configure Host file
6.Execute playbook
7.Launch Container
8.Attach to Container
9.Launch browser and access KIBANA site.
The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![TODO: Update the path with the name of your screenshot of docker ps output](Images/docker_ps_output.png)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
 List the IP addresses of the machines you are monitoring_
 Web1 10.0.0.7 
 Web2 10.0.0.8
We have installed the following Beats on these machines:
 Specify which Beats you successfully installed
 Filebeat

These Beats allow us to collect the following information from each machine:
In 1-2 sentences, explain what kind of data each beat collects, and provide 1 example of what you expect to see. E.g., `Winlogbeat` collects Windows logs, which we use to track user logon events, etc._

Filebeat monitors the log files or locations that you specify, which we use to see what changes or messages the log files have received such as kill commands.

### Using the Playbook

In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the filebeat-config.yml  file to to /etc/ansible/roles/files/
- Update the host file to include  Elk-Server ip address
-Run the playbook, and navigate to  navigate to http://[Public IP of ELK server VM 104.43.13.170:5601/app/kibana to check that the installation worked as expected.  to check that the installation worked as expected.

Answer the following questions to fill in the blanks:_
Which file is the playbook? Where do you copy it?

Which file do you update to make Ansible run the playbook on a specific machine? How do I specify which machine to install the ELK server on versus which to install Filebeat on?_
we can do it by using nano to edity the /etc/ansible/host file to add webserver/elkserver ip addresses and enter the IP address of the specific machine you want the the Elk server on to work 
Which URL do you navigate to in order to check that the ELK server is running?
http://104.43.13.170:5601/app/kibana
_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc._

## Scripts

#  Cloud Security and Virtualization
This is a collection of Linux Scripts and Ansible Scripts from my Cyber Security class at Columbia.

Most of the scripts are used to configure cloud servers with differnt docker containers.

The final setup was 4 servers running vulnerable DVWA containers along with a jump box and a server running an ELK stack container.

## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![NETWORK TOPOLOGY Diagram](https://github.com/pgpguru/scripts/blob/main/Diagrams/cloudsecurity.PNG)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the _install_elk.yml Playbook____ file may be used to install only certain pieces of it, such as Filebeat.

 
---
## Elk-Server.yml
```yml
---
- name: Configure Elk VM with Docker
  hosts: elkservers
  remote_user: azureuser
  become: true
  tasks:
    ## Use apt module
    - name: Install docker.io
      apt:
        update_cache: yes
        name: docker.io
        state: present

      ## Use apt module
    - name: Install pip3
      apt:
        force_apt_get: yes
        name: python3-pip
        state: present

      ## Use pip module
    - name: Install Docker python module
      pip:
        name: docker
        state: present

      ## Use command module
    - name: Increase virtual memory
      command: sysctl -w vm.max_map_count=262144

      ## Use sysctl module
    - name: Use more memory
      sysctl:
        name: vm.max_map_count
        value: "262144"
        state: present
        reload: yes

      ## Use docker_container module
    - name: download and launch a docker elk container
      docker_container:
        name: elk
        image: sebp/elk:761
        state: started
        restart_policy: always
        published_ports:
          - 5601:5601
          - 9200:9200
          - 5044:5044

```
---
## Filebeat-playbook.yml
```yml
---
- name: Installing and Launch Filebeat
  hosts: webservers
  become: yes
  tasks:
    # Use command module
  - name: Download filebeat .deb file
    command: curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.4.0-amd64.deb
    # Use command module
  - name: Install filebeat .deb
    command: dpkg -i filebeat-7.4.0-amd64.deb
    # Use copy module
  - name: Drop in filebeat.yml
    copy:
      src: /etc/ansible/filebeat-config.yml
      dest: /etc/filebeat/filebeat.yml
    # Use command module
  - name: Enable and Configure System Module
    command: filebeat modules enable system
    # Use command module
  - name: Setup filebeat
    command: filebeat setup
    # Use command module
  - name: Start filebeat service
    command: service filebeat start
```
---
## Metricbeat-playbook.yml

```yml
---
- name: Install metric beat
  hosts: webservers
  become: true
  tasks:
    # Use command module
  - name: Download metricbeat
    command: curl -L -O https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-7.4.0-amd$
    # Use command module
  - name: install metricbeat
    command: dpkg -i metricbeat-7.4.0-amd64.deb
    # Use copy module
  - name: drop in metricbeat config
    copy:
      src: /etc/ansible/files/metricbeat-config.yml
      dest: /etc/metricbeat/metricbeat.yml
    # Use command module
  - name: enable and configure docker module for metric beat
    command: metricbeat modules enable docker
    # Use command module
  - name: setup metric beat
    command: metricbeat setup
    # Use command module
  - name: start metric beat
    command: service metricbeat start
```
---

### **This document contains the following details:**
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build
- bash scripts i made and used during my classes bash week
  - scirpts mainy are mainly used for system administration 


### **Description of the Topology**

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly ___Available__, in addition to restricting __Access___ to the network.
- _What aspect of security do load balancers protect? What is the advantage of a jump box?_
`Accessibilty and jump box is a single point of entry
Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the files and system metrics.`
- _What does Filebeat watch for?_ `any changes to the file system`
- _What does Metricbeat record?_`any metric i configured for this network`

> **The configuration details of each machine may be found below.**

| NAME | FUNCTION | IP ADDRESS(ES)| OPERATING SYSTEM |
| ---- | -------- | ------------- | ----------------|
|Jump Box|Gateway|10.0.0.4 & 52.188.213.221|Linux|
|DVWA|Ansible Container VM|10.0.0.5|Linux|
|WebVM1|Ansible Container VM|10.0.0.6|Linux|
|WebVM2|Ansible Container VM|10.0.0.7|Linux|
|Elk-Server|ELastic Stack Server|10.2.0.4 |Linux   

In addition to the above, Azure has provisioned a load balancer in front of all machines except for the jump box. The load balancer's targets are organized into the following availability zones:


Availability Zone 1: DVWA + WebVM1/2

Availability Zone 2: ELK

### **Access Policies**

The machines on the internal network are not exposed to the public Internet. 

>Only the __Jump box__ machine can accept connections from the Internet. Access to this machine is only allowed from my personal IP address.

>Machines within the network can only be accessed by the __Jumpbox vm__ .


A summary of the access policies in place can be found below.

| NAME | PUBLICLY ACCESSIBLE (YES or NO)| ALLOWED IP ADDRESSES|
|------|--------------------------------|---------------------|
Jump Box|YES|Whitelisted IP (My Personal IP)
DVWA|NO
WebVM1|NO
WebVM2|NO
Elk-Server|YES|10.2.0.4:6501

### **Elk Configuration**

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because ..._Scaling and Security_. Absible cuts out the repetive parts of seting up an appliaction enviorment by automating it. 


**The playbook implements the following tasks:**

>(Please see install_elk.yml file in the Ansible folder)
- Configure Elk VM with Docker
- Install docker.io
- Install pip3
- Install Docker python module
- Increase virtual memory
- Use more memory
- download and launch a docker elk container

 type `sudo docker start` then run `docker ps`

### **Target Machines & Beats**
This ELK server is configured to monitor the following machines:
```
10.0.0.5 (DVWA)
10.0.0.6 (WebVM1)
10.0.0.7 (WevVM2)
10.2.0.4 (ELK)
```
We have installed the following Beats on these machines:
```
10.0.0.5 (DVWA)
10.0.0.6 (WebVM1)
10.0.0.7 (WevVM2)
10.2.0.4 (ELK)
```

**These Beats allow us to collect the following information from each machine:**

- Metric beat monitors system-level CPU usage, memory, file system, disk IO, and network IO statistics for every process running on your systems.
- Filebeat is the logging agent generating the log files, tailing them, and forwarding the data to either Logstash for more advanced processing or directly into Elasticsearch for indexing
- Packetbeat provides real-time analytics for web, database and other network protocols

## **Using the Playbook**
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below: Jumbox - ansible container
- Copy the __config ___ file to ___/etc/ansible__.
- Update the ___Hosts__ file to include the Elk-Server ip address under the elkservers header
- Run the playbook, and navigate to _url to kibana port 5601___ to check that the installation worked as expected.




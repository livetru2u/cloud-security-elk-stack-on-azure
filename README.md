## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![Diagram Automated ELK Stack Deployment on Azure - Christina Chen Bertucci](https://github.com/livetru2u/cloud-security-elk-stack-on-azure/blob/main/Images/Diagram%20Automated%20ELK%20Stack%20Deployment%20on%20Azure.png)

The following four playbooks have been tested and used to generate a live ELK deployment on Azure. They can be used together recreate the entire deployment pictured above. 

  The first playbook will download, launch, and enable the DVWA containers in the VMs the elk stack will monitor. 
  - Playbook file [install_dvwa.yml](Ansible/install_dvwa.yml). -- Screenshot [screenshot of install_dvwa.yml run](https://github.com/livetru2u/cloud-security-elk-stack-on-azure/blob/main/Images/install-dvwa.yml%20run%20playbook.png).

  The second playbook will configure the ELK VM with docker by downloading, launching, and enabling the elk container. It will also publish the ports that   
    the elk stack will use - ports 5601, 9200, and 5044. 
  - Playbook file [install_elk.yml](Ansible/install_elk.yml). -- Screenshot [screenshot of install_elk.yml run](https://github.com/livetru2u/cloud-security-elk-stack-on-azure/blob/main/Images/install-elk.yml%20run%20playbook.png).

  The third playbook will install, launch, and enable filebeat 
  - Playbook file [filebeat-playbook.yml](Ansible/filebeat-playbook.yml). -- Screenshot [screenshot of filebeat-playbook.yml run](https://github.com/livetru2u/cloud-security-elk-stack-on-azure/blob/main/Images/filebeat-playbook.yml%20run%20playbook.png).

  The fourth playbook will install, launch, and enable metricbeat 
  - Playbook file [metricbeat-playbook.yml](Ansible/metricbeat-playbook.yml). -- Screenshot [screenshot of metricbeat-playbook.yml run](https://github.com/livetru2u/cloud-security-elk-stack-on-azure/blob/main/Images/metricbeat-playbook.yml%20run%20playbook.png).

  Alternatively, the above yaml playbook files may be used individually to install only certain pieces of it, such as Filebeat.

These documents contain the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
- Target Machines 
- Beats In Use
- Using the Playbook


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D@mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting access to the network.
The load balancer protects the group of servers in the backend pool from high latency issues by redirecting traffic to the other available servers to balance out variances in incoming traffic. The load balancer can also protect from any downtime to occur if there is total failure of a single server by redirecting traffic to remaining available servers. 

The jump box serves as the gateway and single point of entry to the internal network of private servers as a form of access control and a single place where machines can be managed. The advantage of a jump box is provide access between dissimilar security zones. 

Integrating an ELK server allows users to easily monitor the DVWA VMs for changes to the metric data and system log data.

The configuration details of each machine may be found below.

| Name         | Function  | IP Address | Operating System                  |
|--------------|-----------|------------|-----------------------------------|
| Jumpbox VM   | Gateway   | 10.0.0.4   | Linux - Ubuntu 20.04 LTS Gen 1    |
| VM 1         | DVWA      | 10.0.0.5   | Linux - Ubuntu 20.04 LTS Gen 1    |
| VM 2         | DVWA      | 10.0.0.6   | Linux - Ubuntu 20.04 LTS Gen 1    |
| VM 3         | DVWA      | 10.0.0.8   | Linux - Ubuntu 20.04 LTS Gen 1    |
| ELK VM       | Monitor   | 10.1.0.4   | Linux - Ubuntu 20.04 LTS Gen 1    | 

### Access Policies

- The machines on the internal network are not exposed to the public Internet. 
- Only the Load Balancer can accept connections from the Internet. 
- Machines within the internal network can only accessed by the Jumpbox VM.

A summary of the access policies in place can be found in the table below.

| Name            | Publicly Accessible | Allowed IP Addresses                          |
|-----------------|---------------------|-----------------------------------------------|
| Jumpbox VM      | Yes                 | SSH from My IP                                |
| VM 1            | No                  | SSH from 10.0.0.4                             |
| VM 2            | No                  | SSH from 10.0.0.4                             |          
| VM 3            | No                  | SSH from 10.0.0.4                             |
| ELK VM          | Yes                 | HTTP:5601 from My IP  &  SSH from 10.0.0.4    |
| Load Balancer   | Yes                 | HTTP:80                                       |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because configurations and deployment can be duplicated quickly for large and small scale applications.

The playbook implements the following tasks:
- Install Docker
- Install python3-pip
- Install Docker python module
- Download and launch a docker elk container image
- Enable Docker on boot

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

[screenshot of docker ps output](https://github.com/livetru2u/cloud-security-elk-stack-on-azure/blob/main/Images/docker%20ps%20elk%20successful.png)

### Target Machines

This ELK server is configured to monitor the following machines:
- VM 1 DVWA located at 10.0.0.5
- VM 2 DVWA located at 10.0.0.6
- VM 3 DVWA located at 10.0.0.8

Filebeat and Metricbeat are installed in all the DVWA VMs being monitored.  

### Beats In Use

These Beats allow us to collect the following information from each machine:
- Filebeat - the role of Filebeat is to shipper and centralize log data. It has been installed as an agent on the DVWA machines. Filebeat monitors the log files or 
  locations that you specify, collects log events, and forwards them either to Elasticsearch or Logstash for indexing. Here we have configured Filebeat to watch and collect apache, nginx, mysql logs. [screenshot of Filebeat Dashboard](https://github.com/livetru2u/cloud-security-elk-stack-on-azure/blob/main/Images/filebeat%20dashboard%20on%20kibana.png).

- Metricbeat - the role of Metricbeat is to periodically collect metric data from your target servers, this could be operating system metrics such as CPU or memory 
  or data related to services running on the server. It has been installed as an agent on the DVWA machines. These metrics and logs are sent to Elasticsearch, Logstash, and Kibana aka E.L.K. on the ELK Server. [screenshot of Metricbeat Dashboard](https://github.com/livetru2u/cloud-security-elk-stack-on-azure/blob/main/Images/metricbeat%20dashboard%20on%20kibana.png).

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the Jumpbox control node and follow the steps below:
- Copy the yaml playbook files and configuration files to /etc/ansible.
- Update the hosts file to include the IP adresses of the DVWA servers and ELK server you want to include when the playbook runs.
- Update NSG rules to allow access. 
- Run the playbook by using the command "ansible-playbook foo.yml" .
- To check that the ELK server is running, navigate to a URL type in the ELK server public IP and port it's running on to check that the installation worked as 
  expected.

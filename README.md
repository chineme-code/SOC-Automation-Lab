# SOC-Automation-Lab
 In this project, I guide you in building a SOC Automation Lab from scratch, using Wazuh for SIEM, The Hive for case management, and Shuffle for SOAR. This hands-on experience helps you configure and automate a lab that mimics a real SOC

## Objective
The objective of this project is to equip you with practical skills in setting up and automating a fully functional Security Operations Center (SOC) lab. By working through the process of configuring Wazuh for security monitoring, integrating The Hive for efficient case management, and deploying Shuffle for automating incident response, you'll gain hands-on experience in replicating the operations of a real-world SOC. This project is designed to enhance your understanding of key cybersecurity tools and to prepare you for tackling complex security challenges in a professional environment.

### Skills Learned
-SIEM Configuration and Management: Master setting up and managing Wazuh for effective security monitoring and incident detection.

-SOAR Integration: Gain hands-on experience integrating and automating security operations with Shuffle.

-Incident Response: Develop essential skills in identifying, containing, and responding to security threats.

-Case Management: Learn to organize and track security incidents using The Hive.

-Automation Scripting: Enhance your ability to create scripts that automate security tasks and response actions.

### Tools Used
- Wazuh
- The Hive
- Shuffle
- Python
- Virtual Machines
- Cloud Services.

## Steps
Chapter 1: Home Lab Architecture Documentation
System Components and Workflow

![image](https://github.com/user-attachments/assets/cb7d9253-21dd-49af-91d8-8a281251e658)

 
Windows 10 Client with Wazuh Agent: The setup initiates with a Windows 10 client system. This system is instrumented with the Wazuh Agent, responsible for monitoring the host's security events.
Wazuh Manager: The Wazuh Agent forwards the detected events to the Wazuh Manager. The Manager serves as the central processing entity, evaluating the events against its rules engine to determine if an alert should be triggered.
Alert Management:
•	Triggering Alerts: Upon detecting a suspicious event, the Wazuh Manager generates an alert.
•	Alert Enrichment: The generated alert may be enriched with additional threat intelligence using     OSINT sources. This step is crucial for augmenting the information within the alert to provide greater context and aid in decision-making.
Shuffle for Orchestration: Shuffle, an orchestration platform, receives alerts from the Wazuh Manager. It is tasked with automating the workflow by conducting response actions as predefined in the system's configuration.
TheHive for Case Management: Post-orchestration, alerts are sent to TheHive. Here, they are formatted into cases for SOC analysts to review and manage. TheHive serves as the interface for tracking incident response activities and managing the lifecycle of the alert as it turns into a case.
Communication Layer: The system includes an email communication mechanism that informs stakeholders of alerts and actions. This ensures that the SOC team is constantly updated on system status and incident progression.
SOC Analyst Interaction: At the core of the operation is the SOC Analyst, who interacts with the system via TheHive. They review the enriched alerts, oversee the automated response actions, and ensure incidents are managed effectively.
Response Actions: As a part of the automated workflow, response actions are executed. These can range from simple notifications to complex remediation tasks executed via the Shuffle platform.
Networking Considerations: The network is configured such that the Wazuh Manager receives events through a secure channel, ensuring that the communication is limited to the client's public IP address for enhanced security.

Installation Process for Wazuh on a Cloud Server
The Wazuh Manager was installed on a cloud server provided by DigitalOcean with the following specifications:
- RAM: 8GB+
- HDD: 50GB+
- OS: Ubuntu 22.04 LTS
Security configurations included setting up the server's firewall to only accept inbound connections from the laptop's public IP address, applicable to all TCP and UDP ports.
The installation steps were as follows:
Initial Server Setup: An update and upgrade of the server's packages were performed using the commands `apt-get update` and `apt-get upgrade -y`.
Wazuh Manager Installation:
•	The Wazuh installation script was obtained with `curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh`.
•	The installation was executed with `sudo bash ./wazuh-install.sh -a`.
Post-Installation of Wazuh:
Extraction of Wazuh credentials was done using `sudo tar -xvf wazuh-install-files.tar`.

Installation Process for TheHive on a Cloud Server
Server Specifications for TheHive:
- RAM: 8GB+ (16GB recommended)
- HDD: 50GB+
- OS: Ubuntu 22.04 LTS
Installing TheHive 5 and Elasticsearch with JVM Configuration
Step 1: Install Dependencies
```shell: sudo apt install -y wget gnupg apt-transport-https git ca-certificates ca-certificates-java curl software-properties-common python3-pip lsb-release```
Step 2: Install Java (Amazon Corretto)
```shell
wget -qO- https://apt.corretto.aws/corretto.key | sudo gpg --dearmor -o /usr/share/keyrings/corretto.gpg
echo "deb [signed-by=/usr/share/keyrings/corretto.gpg] https://apt.corretto.aws stable main" | sudo tee /etc/apt/sources.list.d/corretto.sources.list
sudo apt update && sudo apt install -y java-common java-11-amazon-corretto-jdk
echo JAVA_HOME="/usr/lib/jvm/java-11-amazon-corretto" | sudo tee /etc/environment 
export JAVA_HOME="/usr/lib/jvm/java-11-amazon-corretto"
```
Step 3: Install Cassandra
```shell
wget -qO - https://downloads.apache.org/cassandra/KEYS | sudo gpg --dearmor -o /usr/share/keyrings/cassandra-archive.gpg
echo "deb [signed-by=/usr/share/keyrings/cassandra-archive.gpg] https://debian.cassandra.apache.org 40x main" | sudo tee /etc/apt/sources.list.d/cassandra.sources.list
sudo apt update && sudo apt install -y cassandra
```
Step 4: Install Elasticsearch
```shell
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/elasticsearch-keyring.gpg] https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-7.x.list
sudo apt update && sudo apt install -y elasticsearch
```
Step 5: Configure Elasticsearch JVM Options (Optional)
1. Create JVM Options Directory: Execute `sudo mkdir -p /etc/elasticsearch/jvm.options.d` to create the necessary directory.
2. Create & Configure JVM Options File: Use `sudo nano /etc/elasticsearch/jvm.options.d/jvm.options` and input the following:
   ```
   -Dlog4j2.formatMsgNoLookups=true
   -Xms2g
   -Xmx2g
   ```
3. Save Changes: Exit nano with `CTRL + X`, confirm with `Y`, and press `Enter`.
4. Restart Elasticsearch: Apply changes with `sudo systemctl restart elasticsearch.service`.
Step 6: Install TheHive
```shell
wget -O- https://archives.strangebee.com/keys/strangebee.gpg | sudo gpg --dearmor -o /usr/share/keyrings/strangebee-archive-keyring.gpg
echo 'deb [signed-by=/usr/share/keyrings/strangebee-archive-keyring.gpg] https://deb.strangebee.com thehive-5.2 main' | sudo tee /etc/apt/sources.list.d/strangebee.list
sudo apt-get update && sudo apt-get install -y thehive
```
Verification and Logs Review
- Check the status of Elasticsearch and TheHive services with `sudo systemctl status elasticsearch.service` and `sudo systemctl status thehive`.
- Review Elasticsearch logs at `/var/log/elasticsearch/` for any issues.
- Validate JVM settings using the Elasticsearch Node Stats API with `curl -X GET "localhost:9200/_nodes/stats/jvm?pretty"` or by searching the logs.



Chapter 2: System Configuration and Integration
2.1 Cassandra and Elasticsearch Configuration:
After setting up the Cassandra database for TheHive, I proceeded to configure the underlying services. I initiated the process by accessing the server via Digital Ocean's droplet console and editing the `cassandra.yaml` file with `nano`. Here, I configured the `listen_address`, `rpc_address`, and `seeds` parameters to align with TheHive's IPV4 address. Following the Cassandra setup, the Elasticsearch service was configured by editing the `elasticsearch.yml` file, setting `cluster.name` to TheHive, adjusting the `node.name` to `node-1`, and assigning the network host to TheHive's IPV4 address. The `discovery.seed_hosts` and `cluster.initial_master_nodes` were also set to reflect our single-node setup.
 ![image](https://github.com/user-attachments/assets/4017ae4e-ac04-40f2-a21a-94419b750bc4)


2.2 TheHive Service Rights and Configuration:
Upon configuring TheHive, I initiated a permission audit for the `/opt/thp` directory with the command `ls -la /opt/thp`, which revealed that ownership was assigned to the root user. To rectify this and grant appropriate access to TheHive's operational user, I executed `chown -R thehive:thehive /opt/thp`. Following this, I accessed TheHive's configuration file, `application.conf`, via `nano /etc/thehive/application.conf`. Here, I designated TheHive's IPV4 address as the hostname for both the `db.janusgraph` and `index.search` configurations, ensuring connectivity and search functionality were correctly pointed to our server. The `cluster_name` was set to "test cluster," and the `application.baseUrl` was updated to TheHive's IPV4 address, solidifying the base URL for user interface access. Post-configuration, I verified operational status through the updated base URL `http://159.223.197.238:9000`.
 ![image](https://github.com/user-attachments/assets/afd54b4c-3650-4d44-a111-4edb634bd7b1)

2.3 Wazuh Server and Agent Integration:
The integration of the Wazuh server began with registering a new agent from the Wazuh dashboard, specifying the Windows 10 client system. The agent installation on the Windows VM was performed using PowerShell, followed by starting the service with `net start wazuhsvc`. This culminated in the successful display of an active agent on the Wazuh server.
 ![image](https://github.com/user-attachments/assets/ff69617d-41f0-47e2-a0c0-15640010c9d5)


Enhancing Wazuh Configuration and Integration
After successfully logging into the Wazuh platform and adding an agent, the next step involves detailed configuration for enhanced monitoring. Initially, selecting the appropriate system for Wazuh server installation is crucial. For this project, Windows 10 was chosen. The server address requires the IPv4 of the Wazuh server. After downloading and installing the Wazuh agent on the Windows 10 VM, I initiated the agent using PowerShell with `net start wazuhsvc`. This action resulted in the display of an active agent on the Wazuh server, signifying successful integration.

Advanced Telemetry and Alert Configuration
To generate and ingest telemetry from the Windows 10 VM into Wazuh, specific configurations were necessary. I started by accessing the `ossec.conf` file in the Wazuh agent directory and made a backup for safety. With administrative privileges, I edited the `ossec.conf` file to enable the ingestion of Sysmon logs. This step is pivotal for capturing detailed telemetry, especially for custom alerts like Mimikatz detection.

Streamlining Incident Response with SOAR Integration
The goal to connect with Shuffle (SOAR) and trigger automated actions in TheHive and to the SOC analyst required a series of intricate configurations. I accessed Shuffle.io, created a workflow for SOC automation with a focus on EDR and ticketing systems. A webhook was set up as a trigger, and its URI was incorporated into the `ossec.conf` file on the Wazuh manager using the `nano` editor. The integration settings were adjusted to capture Mimikatz alerts and route them effectively.

Workflow Optimization and Security Analysis
The SOC workflow was designed to be dynamic and responsive. Upon receiving a Mimikatz alert, Shuffle extracts the file's SHA256 hash. This hash is then checked against VirusTotal for reputation scoring. If deemed malicious, the details are forwarded to TheHive for case creation and an email alert is sent to the SOC analyst for further investigation. To streamline this process, regular expressions were utilized to parse only the SHA256 hash value from the alert, ensuring accurate and relevant data submission to VirusTotal.

Enhancing Collaboration with TheHive
In the final phase of integration, TheHive plays a critical role in case management and SOC team coordination. After setting up an additional organization in TheHive and configuring user roles, the connection between Shuffle and TheHive was established using an API key. This setup allows for seamless transfer of information from VirusTotal to TheHive, thereby facilitating efficient case creation and management.


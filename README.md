# 🛡️ Comprehensive Cloud Security SOC Lab: AWS & Wazuh SIEM Integration

## 📌 Project Objective
The goal of this project was to build a robust, end-to-end Security Operations Center (SOC) monitoring environment in the cloud. By integrating AWS CloudTrail and VPC Flow Logs with Wazuh SIEM, I established real-time visibility into both API-level infrastructure changes and network-level traffic, enabling the detection of misconfigurations, unauthorized access, and active network attacks.

## 🛠️ Tools & Technologies Used
*   **Cloud Platform:** Amazon Web Services (AWS)
*   **Security Services:** AWS CloudTrail, VPC Flow Logs, IAM (Identity and Access Management), Amazon S3, Security Groups
*   **SIEM:** Wazuh (Open Source Security Platform)
*   **Attack Simulation:** PowerShell / Linux Terminal (SSH Brute Force)
*   **OS:** Linux (Wazuh Server & Target EC2)

## 🏗️ What I Built (Architecture & Workflow)
This lab was completed in two distinct phases:

**Phase 1: IAM & API Activity Monitoring (CloudTrail)**
*   Implemented secure identity management using AWS IAM, adhering strictly to the Principle of Least Privilege.
*   Configured AWS CloudTrail to log API activity across the AWS infrastructure and route these logs to a secure S3 bucket.
*   Integrated Wazuh to ingest, parse, and monitor CloudTrail logs for critical events (e.g., `ListUsers`, `GetGroup`, login activities).

**Phase 2: Network Traffic Analysis & Threat Detection (VPC Flow Logs)**
*   Provisioned an Ubuntu EC2 instance as a target server.
*   Enabled AWS VPC Flow Logs to capture `ACCEPT` and `REJECT` network traffic, routing it to the S3 bucket.
*   Simulated a real-world reconnaissance and SSH Brute-Force attack against the target instance's public IP.
*   Successfully detected and analyzed the attack traffic within the Wazuh SIEM dashboard (Rule ID: `80402` - AWS VPC Flow: [REJECT]), extracting critical Indicators of Compromise (IOCs) like the attacker's Source IP and Port.

## ⚠️ Challenges Faced & How I Fixed Them

**Challenge 1: Wazuh not reading logs from S3 (Phase 1)**
*   **Problem:** The Wazuh manager was failing to fetch logs from the AWS S3 bucket despite configuration.
*   **Solution:** Identified that the IAM user lacked programmatic access. Generated Access Key/Secret Key for the `wazuh-s3-reader` user and configured the AWS profile locally on the Wazuh server using `aws configure`.

**Challenge 2: Parsing JSON logs in Wazuh (Phase 1)**
*   **Problem:** CloudTrail logs were arriving, but the SIEM couldn't read the structured data.
*   **Solution:** Ensured that `<jsonout_output>yes</jsonout_output>` was enabled in the `ossec.conf` file to properly structure the JSON logs into Wazuh-readable formats.

**Challenge 3: UnauthorizedOperation Error for Network Logs (Phase 2)**
*   **Problem:** Wazuh threw an exit code 12 error: `User is not authorized to perform: ec2:DescribeFlowLogs`. Wazuh could read CloudTrail logs but was blocked from reading VPC Flow logs.
*   **Solution:** Investigated the internal Wazuh logs (`ossec.log`). Created a custom IAM Inline Policy granting specific `ec2:DescribeFlowLogs` permissions and attached it to the `wazuh-s3-reader` user, instantly resolving the access block while maintaining least privilege.

## 💻 Key Commands & Scenarios

| Scenario / Problem | Command Used | Explanation |
| :--- | :--- | :--- |
| **Editing Configuration** | `sudo nano /var/ossec/etc/ossec.conf` | Used to inject the `<wodle name="aws-s3">` block and add the `vpcflow` bucket type. |
| **Applying Changes** | `sudo systemctl restart wazuh-manager` | Restarted the Wazuh backend service to apply configuration changes. |
| **Troubleshooting Connection** | `sudo grep -i "aws" /var/ossec/logs/ossec.log \| tail -n 20` | Extracted the last 20 AWS-related logs from Wazuh's internal system to identify IAM permission errors. |
| **Simulating Attack** | `ssh hacker@<Target-IP>` | Generated failed SSH connection attempts to trigger VPC flow rejection logs. |

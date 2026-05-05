# 🛡️ AWS & Wazuh Cloud Security Integration Lab

## 📌 Project Objective
The goal of this project was to build a robust Cloud Security Monitoring environment. By integrating AWS CloudTrail with Wazuh SIEM, I established real-time visibility into AWS infrastructure, enabling the detection of misconfigurations, unauthorized access, and IAM policy changes.

## Phase 1: IAM & Access Control
Implemented secure identity management using AWS IAM, least privilege principle, and MFA authentication.

## 🛠️ Tools & Technologies Used
* **Cloud Platform:** Amazon Web Services (AWS)
* **Security Services:** AWS CloudTrail, IAM (Identity and Access Management), Amazon S3
* **SIEM:** Wazuh (Open Source Security Platform)
* **OS:** Linux (Wazuh Server)

## 🚀 Step-by-Step Execution
1. **AWS IAM Setup:** Created a dedicated IAM user (`wazuh-s3-reader`) adhering to the Principle of Least Privilege, granting only `AmazonS3ReadOnlyAccess`.
2. **CloudTrail Configuration:** Enabled AWS CloudTrail to log API activity across the AWS infrastructure and route these logs to a secure S3 bucket.
3. **Wazuh Integration:** Configured the Wazuh Manager's `ossec.conf` file using the AWS S3 wodle to securely fetch logs from the S3 bucket.
4. **Threat Monitoring:** Successfully ingested and parsed AWS logs into the Wazuh dashboard, capturing critical events like `ListUsers`, `GetGroup`, and login activities.

## ⚠️ Challenges Faced & Solutions
* **Challenge 1:** *Wazuh not reading logs from S3.* 
  * **Solution:** Discovered that the IAM user lacked specific programmatic access. Created an Access Key/Secret Key for the `wazuh-s3-reader` user and configured the AWS profile locally on the Wazuh server using `aws configure`.
* **Challenge 2:** *Parsing JSON logs in Wazuh.*
  * **Solution:** Ensured that `<jsonout_output>yes</jsonout_output>` was enabled in `ossec.conf` to properly structure the AWS CloudTrail JSON logs into Wazuh readable formats.

## 💻 Key Commands & Scenarios
| Scenario / Problem | Command Used | Explanation |
| :--- | :--- | :--- |
| Editing Wazuh configuration to add AWS S3 module | `nano /var/ossec/etc/ossec.conf` | Used the nano text editor to inject the `<wodle name="aws-s3">` configuration block. |
| Applying the new configuration | `systemctl restart wazuh-manager` | Restarted the Wazuh service to apply changes made in `ossec.conf`. |
| Verifying if Wazuh is throwing any AWS connection errors | `cat /var/ossec/logs/ossec.log | grep aws` | Checked the internal Wazuh logs to troubleshoot S3 bucket connection issues. |

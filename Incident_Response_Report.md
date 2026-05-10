🚨 Cloud Incident Response (IR) Log (Sanitized for Public Release)
Ticket ID: INC-20260508-001

Date of Report: May 8, 2026

Reported By: Pranit Kalambate (Cloud Security Analyst)

Status: 🟢 CLOSED / REMEDIATED

1. Incident Summary
Incident Type: Cloud Security Misconfiguration / Policy Violation

Severity: CRITICAL (Level 12)

Detection Source: Wazuh SIEM (via AWS CloudTrail integration)

Rule Triggered: Rule ID 100050 - CRITICAL SOC ALERT: AWS S3 Bucket Public Access Control Modified!

2. Executive Summary
At 13:46:16 local time, the Wazuh SIEM triggered a Level 12 Critical alert indicating a severe misconfiguration within the AWS production environment. An IAM user successfully disabled the "Block Public Access" feature on a centralized AWS S3 log storage bucket ([REDACTED_S3_BUCKET_NAME]). This action exposed potentially sensitive cloud logs to the public internet. The SOC team immediately triaged the alert, identified the threat actor's IP and ARN, and executed remediation playbooks to secure the bucket, preventing any data exfiltration.

3. Indicators of Compromise (IOCs) & Evidence
During the deep-dive log investigation of the raw JSON payload in Wazuh, the following critical data points were extracted:

Attacker / Source IP Address: [REDACTED_SOURCE_IP]

Compromised User ARN: arn:aws:iam::[REDACTED_ACCOUNT_ID]:user/[REDACTED_IAM_USER]

AWS API Action Executed: PutBucketPublicAccessBlock

Targeted AWS Resource: AWS::S3::Bucket ([REDACTED_S3_BUCKET_NAME])

User Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36...

4. Incident Timeline (Audit Trail)
May 8, 2026 @ 08:11:36Z: The IAM user [REDACTED_IAM_USER] executes the PutBucketPublicAccessBlock API call, disabling public access blocks on the S3 bucket.

May 8, 2026 @ 08:11:40Z: AWS CloudTrail successfully logs the API event and routes the JSON log to the secure storage bucket.

May 8, 2026 @ 13:46:16: Wazuh Manager ingests the log. The Custom XML Detection Rule (id="100050") successfully parses the payload and triggers a Level 12 Critical Alert on the SOC Dashboard.

May 8, 2026 @ 13:50:00: SOC Analyst acknowledges the alert and begins log investigation.

May 8, 2026 @ 13:55:00 (Containment): SOC Analyst logs into the AWS Management Console, navigates to the affected S3 bucket permissions, and enforces "Block all public access" to ON.

5. Remediation & Mitigation
Immediate Action Taken: The misconfigured S3 bucket was immediately secured by manually re-enabling the "Block all public access" feature within the AWS S3 Console.

Verification: Verified that subsequent CloudTrail logs showed the PutBucketPublicAccessBlock setting returned to a secure state.

6. Lessons Learned & Recommendations
Root Cause: An administrator account with broad permissions bypassed standard security policies.

Recommendation 1: Implement AWS Service Control Policies (SCPs) to explicitly deny the s3:PutBucketPublicAccessBlock action for all users, regardless of their IAM policies, to prevent accidental public exposure.

Recommendation 2: Enforce stricter MFA conditions for high-privileged IAM accounts when modifying critical infrastructure configurations.

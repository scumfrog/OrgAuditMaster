# OrgAuditMaster: AWS Organization-wide Security Auditor

**OrgAuditMaster** is a command-line utility designed to provide an audit overview of various AWS resources. It assists in quickly extracting valuable information related to security configurations in AWS.

## 🚀 Features

- List AWS PrivateLink endpoints.
- Review S3 bucket policies for all your buckets.
- List all your KMS keys.
- Provide an overview of all your Lambda functions.
- A special feature to review resources across all accounts in an AWS Organization.

## 📋 Prerequisites

1. AWS CLI configured with appropriate permissions.
2. `jq` tool for JSON processing. The script will attempt to install this if not found.
3. For the AWS Organizations feature, ensure you have permissions set up to assume roles in other accounts.

## 🛠 Usage

1. Make sure you've set up AWS CLI:

```bash
aws configure
```

Make the script executable and run:

```chmod +x OrgAuditMaster.sh
./OrgAuditMaster.sh
```

2. Choose the desired operation from the on-screen menu.

⚠ Recommendations

- Permissions: Ensure AWS permissions are correctly set, especially if trying to assume roles in other accounts.
- Data Sensitivity: Handle outputs responsibly, as they can reveal sensitive infrastructure details.

📄 License
This project is licensed under the MIT License.

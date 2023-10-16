#!/bin/bash

# OrgAuditMaster: AWS Organization-wide Security Auditor

# Check for jq
if ! command -v jq &> /dev/null
then
    echo "jq is not installed. Attempting to install..."
    
    # Installing jq based on OS
    if [[ "$OSTYPE" == "linux-gnu"* ]]; then
        sudo apt-get update && sudo apt-get install -y jq
    elif [[ "$OSTYPE" == "darwin"* ]]; then
        brew install jq
    else
        echo "Please install jq manually from https://stedolan.github.io/jq/download/"
        exit 1
    fi
fi

function list_private_link_endpoints() {
    REGION=$(aws configure get region)
    echo "Listing VPC Endpoints in region $REGION..."
    aws ec2 describe-vpc-endpoints --region $REGION --query "VpcEndpoints[?VpcEndpointType=='Interface'].[VpcEndpointId,ServiceName]" --output json
}

function review_s3_bucket_policies() {
    echo "Reviewing S3 Bucket Policies..."
    BUCKETS=$(aws s3api list-buckets --query "Buckets[].Name" --output text)
    for BUCKET in $BUCKETS; do
        echo "Policy for bucket $BUCKET:"
        aws s3api get-bucket-policy --bucket $BUCKET --output json || echo "No policy set for $BUCKET"
    done
}

function list_kms_keys() {
    echo "Listing KMS Keys..."
    aws kms list-keys --output json
}

function review_lambda_functions() {
    REGION=$(aws configure get region)
    echo "Reviewing Lambda Functions in region $REGION..."
    aws lambda list-functions --region $REGION --output json
}

function review_all_organizations_accounts() {
    MANAGEMENT_ACCOUNT_ID=$(aws sts get-caller-identity --query "Account" --output text)
    echo "Reviewing resources for all accounts in the organization from management account $MANAGEMENT_ACCOUNT_ID..."
    
    ACCOUNT_IDS=$(aws organizations list-accounts --query "Accounts[].Id" --output text)
    
    for ACCOUNT_ID in $ACCOUNT_IDS; do
        echo "Attempting to assume role in account $ACCOUNT_ID..."

        # Assuming the OrganizationAuditRole in the target account
        CREDENTIALS=$(aws sts assume-role --role-arn "arn:aws:iam::$ACCOUNT_ID:role/OrganizationAuditRole" --role-session-name "OrgAuditSession" --query "Credentials" --output json)

        # Using temporary credentials from assumed role to review resources
        AWS_ACCESS_KEY_ID=$(echo $CREDENTIALS | jq -r .AccessKeyId)
        AWS_SECRET_ACCESS_KEY=$(echo $CREDENTIALS | jq -r .SecretAccessKey)
        AWS_SESSION_TOKEN=$(echo $CREDENTIALS | jq -r .SessionToken)

        echo "Reviewing resources for account $ACCOUNT_ID using assumed role..."
        AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY AWS_SESSION_TOKEN=$AWS_SESSION_TOKEN list_private_link_endpoints
    done
}

# Main menu
while :
do
    echo "*********************************"
    echo "         OrgAuditMaster          "
    echo "*********************************"
    echo "1. List PrivateLink Endpoints"
    echo "2. Review S3 Bucket Policies"
    echo "3. List KMS Keys"
    echo "4. Review Lambda Functions"
    echo "5. Review All Organization Accounts"
    echo "6. Exit"
    echo "*********************************"
    read -p "Choose an option [1-6]: " option

    case $option in
        1) list_private_link_endpoints;;
        2) review_s3_bucket_policies;;
        3) list_kms_keys;;
        4) review_lambda_functions;;
        5) review_all_organizations_accounts;;
        6) exit 0;;
        *) echo "Invalid option. Please choose between [1-6].";;
    esac
done

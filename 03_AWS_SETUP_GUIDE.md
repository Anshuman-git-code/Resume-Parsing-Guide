# AWS Account Setup Guide for Team

## Table of Contents
1. [Creating Shared AWS Account](#creating-shared-aws-account)
2. [Setting Up IAM Users](#setting-up-iam-users)
3. [Configuring AWS CLI](#configuring-aws-cli)
4. [Cost Management](#cost-management)
5. [Security Best Practices](#security-best-practices)

---

## Creating Shared AWS Account

### Step 1: Create AWS Free Tier Account (Team Lead Only)

1. **Go to:** https://aws.amazon.com/free/
2. **Click:** "Create a Free Account"
3. **Email:** Use a dedicated team email (e.g., `resumeparser.team@gmail.com`)
   - Create a new Gmail if needed
   - Share credentials securely with team
4. **Account Name:** `ResumeParserTeam` or similar
5. **Contact Information:**
   - Use team lead's information
   - Select "Personal" account type
6. **Payment Information:**
   - Add credit/debit card (required even for free tier)
   - AWS won't charge unless you exceed free tier limits
7. **Identity Verification:**
   - Phone verification required
8. **Select Support Plan:**
   - Choose "Basic Support - Free"

### Step 2: Secure Root Account

1. **Enable MFA (Multi-Factor Authentication):**
   - Go to: IAM Dashboard → Security Credentials
   - Click: "Assign MFA device"
   - Use Google Authenticator app
   - **Important:** Save backup codes in shared Google Doc

2. **Create Strong Password:**
   - Use password manager
   - Share securely with team (Google Drive encrypted file)

3. **Set Up Billing Alerts:**
   - Go to: Billing Dashboard → Billing Preferences
   - Enable: "Receive Free Tier Usage Alerts"
   - Enable: "Receive Billing Alerts"
   - Email: Team email address

---

## Setting Up IAM Users

### Why IAM Users?
- Don't use root account for daily work (security risk)
- Each team member gets their own credentials
- Can track who did what
- Can limit permissions per role

### Step 1: Create IAM Users (Team Lead)

**For Each Team Member:**

1. **Go to:** IAM Console → Users → Add User
2. **User Name:** `member1-firstname`, `member2-firstname`, etc.
3. **Access Type:** Check both:
   - ✅ Programmatic access (for AWS CLI)
   - ✅ AWS Management Console access (for web console)
4. **Console Password:**
   - Select "Custom password"
   - Create a temporary password
   - ✅ Check "User must create a new password at next sign-in"
5. **Click:** Next: Permissions

### Step 2: Assign Permissions

**Option A: Use Managed Policies (Easier)**

Create 4 users with different permissions:

**Team Lead (Full Access):**
```
Policies to Attach:
- AdministratorAccess
```

**Member 1 (Upload & Extraction):**
```
Policies to Attach:
- AmazonS3FullAccess
- AWSLambda_FullAccess
- AmazonTextractFullAccess
- CloudWatchLogsFullAccess
- IAMReadOnlyAccess
```

**Member 2 (Skill Parsing):**
```
Policies to Attach:
- AWSLambda_FullAccess
- AmazonS3ReadOnlyAccess
- ComprehendFullAccess
- CloudWatchLogsFullAccess
```

**Member 3 (Matching & Database):**
```
Policies to Attach:
- AWSLambda_FullAccess
- AmazonDynamoDBFullAccess
- CloudWatchLogsFullAccess
```

**Member 4 (Dashboard & API):**
```
Policies to Attach:
- AmazonS3FullAccess (for hosting dashboard)
- AmazonAPIGatewayAdministrator
- CloudFrontFullAccess
- CloudWatchLogsReadOnlyAccess
```

**Option B: Create Custom Policy (More Secure)**

If you want tighter control, create a custom policy:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:*",
        "lambda:*",
        "textract:*",
        "comprehend:*",
        "dynamodb:*",
        "apigateway:*",
        "cloudwatch:*",
        "logs:*"
      ],
      "Resource": "*"
    }
  ]
}
```

### Step 3: Download Credentials

1. **After creating user:** Download CSV file with credentials
2. **CSV Contains:**
   - User name
   - Access Key ID
   - Secret Access Key
   - Console login URL
   - Password (temporary)

3. **Share Securely:**
   - Upload to shared Google Drive (restricted access)
   - Or send via encrypted email
   - **Never commit to GitHub!**

### Step 4: Console Login URL

Your team's console login URL will be:
```
https://[account-id].signin.aws.amazon.com/console
```

Or create an account alias:
```
https://resumeparser-team.signin.aws.amazon.com/console
```

To create alias:
- IAM Dashboard → AWS Account → Account Alias → Create

---

## Configuring AWS CLI

### Step 1: Install AWS CLI

**macOS:**
```bash
# Using Homebrew
brew install awscli

# Verify installation
aws --version
```

**Windows:**
```bash
# Download installer from:
# https://aws.amazon.com/cli/

# Or use pip
pip install awscli

# Verify
aws --version
```

**Linux:**
```bash
# Using pip
pip3 install awscli

# Or using package manager
sudo apt-get install awscli  # Ubuntu/Debian
sudo yum install awscli      # CentOS/RHEL

# Verify
aws --version
```

### Step 2: Configure AWS CLI (Each Member)

```bash
aws configure
```

**You'll be prompted for:**

1. **AWS Access Key ID:** [From your CSV file]
2. **AWS Secret Access Key:** [From your CSV file]
3. **Default region name:** `us-east-1` (recommended)
4. **Default output format:** `json`

**Example:**
```
AWS Access Key ID [None]: AKIAIOSFODNN7EXAMPLE
AWS Secret Access Key [None]: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
Default region name [None]: us-east-1
Default output format [None]: json
```

### Step 3: Test Configuration

```bash
# Test if credentials work
aws sts get-caller-identity

# Should return:
{
    "UserId": "AIDAI...",
    "Account": "123456789012",
    "Arn": "arn:aws:iam::123456789012:user/member1-john"
}

# List S3 buckets (should be empty initially)
aws s3 ls
```

### Step 4: Configure Named Profiles (Optional)

If you need to switch between accounts:

```bash
# Configure with profile name
aws configure --profile resumeparser

# Use specific profile
aws s3 ls --profile resumeparser

# Set default profile
export AWS_PROFILE=resumeparser
```

---

## Cost Management

### Free Tier Limits (First 12 Months)

**S3:**
- 5 GB storage
- 20,000 GET requests
- 2,000 PUT requests

**Lambda:**
- 1 million requests/month
- 400,000 GB-seconds compute time

**DynamoDB:**
- 25 GB storage
- 25 read/write capacity units

**Textract:**
- 1,000 pages/month (DetectDocumentText)
- 100 pages/month (AnalyzeDocument)

**API Gateway:**
- 1 million API calls/month (first 12 months)

**Comprehend:**
- 50,000 units/month (first 12 months)

### Setting Up Billing Alerts

**Step 1: Enable Billing Alerts**
1. Go to: Billing Dashboard
2. Click: Billing Preferences
3. Enable: "Receive Billing Alerts"
4. Save preferences

**Step 2: Create CloudWatch Alarms**
1. Go to: CloudWatch → Alarms → Create Alarm
2. Select: Billing → Total Estimated Charge
3. Set threshold: $5 (first alert)
4. Create SNS topic: `billing-alerts`
5. Add email: Team email
6. Confirm subscription via email

**Create Multiple Alarms:**
- $5 threshold (warning)
- $10 threshold (concern)
- $20 threshold (stop everything!)

### Daily Cost Monitoring

**Team Lead Responsibility:**
```bash
# Check current month's costs
aws ce get-cost-and-usage \
  --time-period Start=2025-11-01,End=2025-11-30 \
  --granularity MONTHLY \
  --metrics BlendedCost

# Or use AWS Console:
# Billing Dashboard → Bills → Month-to-Date Spend
```

**Cost Optimization Tips:**
1. **Delete test resources** after use
2. **Use smallest Lambda memory** (128 MB initially)
3. **Limit Textract usage** (test with few PDFs)
4. **Set S3 lifecycle policies** (auto-delete after 30 days)
5. **Use DynamoDB on-demand pricing** (pay per request)

### Expected Costs for This Project

**Realistic Estimate:**
- S3: $0.50 (storage + requests)
- Lambda: $0.00 (within free tier)
- Textract: $1.50 (50 test PDFs)
- DynamoDB: $0.00 (within free tier)
- API Gateway: $0.00 (within free tier)
- Data Transfer: $0.50

**Total: ~$2.50 - $5.00 for entire project**

---

## Security Best Practices

### 1. Never Commit Credentials to GitHub

**Bad (Don't do this):**
```python
# config.py
AWS_ACCESS_KEY = "AKIAIOSFODNN7EXAMPLE"  # ❌ NEVER!
AWS_SECRET_KEY = "wJalrXUtnFEMI..."      # ❌ NEVER!
```

**Good (Do this):**
```python
# config.py
import os
AWS_ACCESS_KEY = os.environ.get('AWS_ACCESS_KEY_ID')
AWS_SECRET_KEY = os.environ.get('AWS_SECRET_ACCESS_KEY')
```

**Create .gitignore:**
```
# .gitignore
.env
credentials.csv
*.pem
.aws/
config.json
```

### 2. Use Environment Variables

**For Lambda Functions:**
```python
import os
import boto3

# AWS SDK automatically uses IAM role credentials
s3 = boto3.client('s3')

# For other configs
BUCKET_NAME = os.environ.get('BUCKET_NAME')
```

**For Local Development:**
```bash
# Create .env file (don't commit!)
export AWS_ACCESS_KEY_ID=your_key
export AWS_SECRET_ACCESS_KEY=your_secret
export AWS_DEFAULT_REGION=us-east-1

# Load in terminal
source .env
```

### 3. Use IAM Roles for Lambda

**Instead of hardcoding credentials in Lambda:**

1. **Create IAM Role:**
   - Go to: IAM → Roles → Create Role
   - Select: Lambda
   - Attach policies: S3, Textract, DynamoDB, etc.
   - Name: `ResumeParserLambdaRole`

2. **Attach to Lambda:**
   - When creating Lambda function
   - Select: "Use an existing role"
   - Choose: `ResumeParserLambdaRole`

3. **Lambda automatically gets credentials** - no keys needed!

### 4. S3 Bucket Security

**Make Bucket Private:**
```bash
# Block all public access
aws s3api put-public-access-block \
  --bucket resume-parser-team-xyz \
  --public-access-block-configuration \
  "BlockPublicAcls=true,IgnorePublicAcls=true,BlockPublicPolicy=true,RestrictPublicBuckets=true"
```

**Bucket Policy (Only Lambda can access):**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::ACCOUNT-ID:role/ResumeParserLambdaRole"
      },
      "Action": "s3:*",
      "Resource": [
        "arn:aws:s3:::resume-parser-team-xyz",
        "arn:aws:s3:::resume-parser-team-xyz/*"
      ]
    }
  ]
}
```

### 5. API Gateway Security

**Enable CORS:**
```json
{
  "Access-Control-Allow-Origin": "*",
  "Access-Control-Allow-Methods": "GET,POST,OPTIONS",
  "Access-Control-Allow-Headers": "Content-Type"
}
```

**Add API Key (Optional):**
1. API Gateway → API Keys → Create
2. Create Usage Plan
3. Associate with API stage
4. Require API key for endpoints

### 6. DynamoDB Security

**Enable Point-in-Time Recovery:**
```bash
aws dynamodb update-continuous-backups \
  --table-name resume-parser-candidates \
  --point-in-time-recovery-specification PointInTimeRecoveryEnabled=true
```

**Enable Encryption at Rest:**
- Enabled by default with AWS managed keys
- No additional cost

---

## Resource Naming Convention

**Use consistent naming across all resources:**

```
Pattern: resume-parser-[resource-type]-[description]

Examples:
- S3: resume-parser-bucket-main
- Lambda: resume-parser-upload-handler
- Lambda: resume-parser-textract-processor
- Lambda: resume-parser-skill-parser
- Lambda: resume-parser-job-matcher
- DynamoDB: resume-parser-candidates
- DynamoDB: resume-parser-jobs
- DynamoDB: resume-parser-matches
- IAM Role: resume-parser-lambda-role
- API: resume-parser-api
```

**Benefits:**
- Easy to identify project resources
- Easy to clean up after project
- Clear ownership

---

## Shared Resources Document

**Create a Google Doc with:**

```
# Resume Parser - AWS Resources

## Account Details
- Account ID: 123456789012
- Console URL: https://resumeparser-team.signin.aws.amazon.com/console
- Region: us-east-1

## IAM Users
- Team Lead: member1-anshuman
- Member 2: member2-[name]
- Member 3: member3-[name]
- Member 4: member4-[name]

## S3 Buckets
- Main Bucket: resume-parser-bucket-main
- Dashboard Hosting: resume-parser-dashboard

## Lambda Functions
- Upload Handler: resume-parser-upload-handler
- Textract Processor: resume-parser-textract-processor
- Skill Parser: resume-parser-skill-parser
- Job Matcher: resume-parser-job-matcher

## DynamoDB Tables
- Candidates: resume-parser-candidates
- Jobs: resume-parser-jobs
- Matches: resume-parser-matches

## API Gateway
- API Name: resume-parser-api
- Base URL: https://[api-id].execute-api.us-east-1.amazonaws.com/prod

## Current Costs
- Week 1: $X.XX
- Week 2: $X.XX
- Total: $X.XX

## Notes
- [Add any important notes or issues]
```

---

## Troubleshooting Common Issues

### Issue: "Access Denied" Error

**Cause:** Insufficient IAM permissions

**Solution:**
1. Check which action is denied (e.g., `s3:PutObject`)
2. Team Lead adds that permission to your IAM user
3. Wait 1-2 minutes for changes to propagate
4. Try again

### Issue: "Invalid Credentials" Error

**Cause:** Wrong access keys or expired credentials

**Solution:**
```bash
# Reconfigure AWS CLI
aws configure

# Or check current config
cat ~/.aws/credentials
cat ~/.aws/config

# Test credentials
aws sts get-caller-identity
```

### Issue: "Region Not Found" Error

**Cause:** Resource created in different region

**Solution:**
```bash
# Always use us-east-1 for this project
aws configure set region us-east-1

# Or specify in command
aws s3 ls --region us-east-1
```

### Issue: Unexpected Charges

**Cause:** Resources left running or high usage

**Solution:**
1. Check Billing Dashboard → Cost Explorer
2. Identify expensive service
3. Delete unused resources:
   ```bash
   # List all S3 buckets
   aws s3 ls
   
   # List all Lambda functions
   aws lambda list-functions
   
   # List all DynamoDB tables
   aws dynamodb list-tables
   ```

---

## Cleanup After Project (Nov 18+)

**To avoid ongoing charges:**

```bash
# Delete S3 buckets
aws s3 rb s3://resume-parser-bucket-main --force

# Delete Lambda functions
aws lambda delete-function --function-name resume-parser-upload-handler
aws lambda delete-function --function-name resume-parser-textract-processor
aws lambda delete-function --function-name resume-parser-skill-parser
aws lambda delete-function --function-name resume-parser-job-matcher

# Delete DynamoDB tables
aws dynamodb delete-table --table-name resume-parser-candidates
aws dynamodb delete-table --table-name resume-parser-jobs
aws dynamodb delete-table --table-name resume-parser-matches

# Delete API Gateway
aws apigateway delete-rest-api --rest-api-id [your-api-id]

# Delete CloudWatch Logs
aws logs delete-log-group --log-group-name /aws/lambda/resume-parser-upload-handler
# Repeat for other log groups
```

**Or use AWS Console:**
- Go to each service
- Select resources with "resume-parser" prefix
- Delete all

---

## Quick Reference Commands

```bash
# Check who you are
aws sts get-caller-identity

# List S3 buckets
aws s3 ls

# Upload file to S3
aws s3 cp file.pdf s3://bucket-name/

# List Lambda functions
aws lambda list-functions

# Invoke Lambda function
aws lambda invoke --function-name my-function output.json

# Query DynamoDB
aws dynamodb scan --table-name my-table

# Check current costs
# (Use AWS Console: Billing Dashboard)

# View CloudWatch logs
aws logs tail /aws/lambda/my-function --follow
```

---

## Support Resources

**AWS Documentation:**
- S3: https://docs.aws.amazon.com/s3/
- Lambda: https://docs.aws.amazon.com/lambda/
- Textract: https://docs.aws.amazon.com/textract/
- DynamoDB: https://docs.aws.amazon.com/dynamodb/

**AWS Free Tier:**
- https://aws.amazon.com/free/

**AWS Support:**
- Basic support included (documentation, forums)
- For billing issues: AWS Support Center

**Community Help:**
- Stack Overflow: [aws] tag
- AWS Forums: https://forums.aws.amazon.com/

---

**Remember:** 
- Keep credentials secure
- Monitor costs daily
- Use free tier wisely
- Clean up after project

**Good luck with your AWS setup! 🚀**

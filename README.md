## 📧 Serverless Marketing Email Campaign (AWS SES + Lambda + EventBridge)

### 🚀 Overview

This project is a **Serverless Marketing Email Campaign System** built on **AWS Cloud Services**.
It automates sending personalized marketing emails to multiple contacts using a **custom HTML email template** and a **CSV contact list**, orchestrated through **Lambda** and **EventBridge**.

This eliminates the need for manual email tools and enables **scalable**, **cost-efficient**, and **event-driven** marketing automation.

---

### 🧩 Architecture Diagram

```
Contacts (contacts.csv)
        ↓
AWS Lambda (Email Sender)
        ↓
Amazon SES (Email Service)
        ↑
Amazon EventBridge (Scheduled Trigger)
        ↑
IAM Roles (Permission Management)
```

---

### ⚙️ AWS Services Used

| Service                               | Purpose                                                                        |
| ------------------------------------- | ------------------------------------------------------------------------------ |
| **Amazon SES (Simple Email Service)** | Sends custom HTML emails to verified recipients                                |
| **AWS Lambda**                        | Serverless function that reads contacts, formats emails, and triggers SES      |
| **Amazon EventBridge**                | Triggers Lambda on a schedule (daily, weekly, or custom event)                 |
| **AWS IAM**                           | Provides permissions for Lambda and EventBridge to access SES and S3 (if used) |

---

### 🧠 Key Features

* 📬 Bulk email sending with personalization using CSV contacts
* 🧾 Custom HTML email templates (`email_template.html`)
* 🕒 Automated scheduling via EventBridge
* 🧑‍💻 No server management — 100% serverless
* 🔒 IAM roles for secure access control
* 💰 Pay-as-you-go AWS cost model

---

### 🧱 Project Structure

```
📦 marketing-email-campaign/
│
├── lambda_function.py            # Main AWS Lambda handler
├── email_template.html           # Custom email HTML template
├── contacts.csv                  # Contact list (Name, Email)
├── requirements.txt              # Python dependencies (boto3, csv, etc.)
├── policy-lambda.json            # IAM policy for Lambda execution
├── policy-eventbridge.json       # IAM policy for EventBridge scheduler
└── README.md                     # Project documentation
```

---

### 📋 Prerequisites

1. **AWS Account** with access to SES, Lambda, and EventBridge
2. **Verified email identity** in SES (sender email must be verified)
3. **Python 3.9+** installed locally (for packaging and testing Lambda code)
4. **AWS CLI** configured with appropriate IAM permissions

---

### 🧠 Setup Instructions

#### 1️⃣ Clone the repository

```bash
git clone https://github.com/<your-username>/marketing-email-campaign.git
cd marketing-email-campaign
```

#### 2️⃣ Configure your contacts list

Update **`contacts.csv`** in the following format:

```csv
name,email
John Doe,john@example.com
Jane Smith,jane@example.com
```

#### 3️⃣ Create and verify SES email identity

In the **AWS SES Console**:

* Go to **Verified Identities**
* Click **Create Identity**
* Choose **Email Address**
* Verify your sending email (e.g., `marketing@yourdomain.com`)

#### 4️⃣ Prepare the Lambda function

Install dependencies locally and zip for deployment:

```bash
pip install -r requirements.txt -t .
zip -r lambda_function.zip .
```

#### 5️⃣ Create IAM Role for Lambda

Attach the following permissions:

* `AmazonSESFullAccess`
* `CloudWatchLogsFullAccess`
* Custom inline policy for reading S3 (if template/contacts stored there)

Example policy snippet (`policy-lambda.json`):

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Action": ["ses:SendEmail", "ses:SendRawEmail"],
      "Resource": "*",
      "Effect": "Allow"
    }
  ]
}
```

#### 6️⃣ Deploy the Lambda Function

```bash
aws lambda create-function \
  --function-name marketing-email-sender \
  --runtime python3.9 \
  --role arn:aws:iam::<account-id>:role/<lambda-role-name> \
  --handler lambda_function.lambda_handler \
  --zip-file fileb://lambda_function.zip
```

#### 7️⃣ Configure EventBridge Schedule

Create a scheduled event (e.g., every day at 10 AM):

```bash
aws events put-rule \
  --name "DailyEmailCampaign" \
  --schedule-expression "cron(0 10 * * ? *)"
```

Then add Lambda as a target:

```bash
aws events put-targets \
  --rule "DailyEmailCampaign" \
  --targets "Id"="1","Arn"="arn:aws:lambda:<region>:<account-id>:function:marketing-email-sender"
```

---

### 💻 Lambda Function Logic

1. Reads all contacts from `contacts.csv`
2. Loads `email_template.html`
3. Replaces placeholders (like `{{name}}`) with actual values
4. Sends emails through **Amazon SES**
5. Logs success/failure details to **CloudWatch Logs**

Example snippet:

```python
response = ses_client.send_email(
    Source=sender_email,
    Destination={'ToAddresses': [recipient_email]},
    Message={
        'Subject': {'Data': subject},
        'Body': {'Html': {'Data': html_content}}
    }
)
```

---

### 📅 EventBridge Automation

You can configure different types of triggers:

* **Cron expressions** (e.g., `cron(0 12 * * ? *)`)
* **Rule-based triggers** (on upload, API call, or S3 event)

---

### 🧾 Logs & Monitoring

* View logs in **Amazon CloudWatch Logs**
* Each email send attempt (success/failure) is recorded
* Add metrics or alarms if needed using **CloudWatch Metrics**

---

### 🧠 Future Enhancements

* ✅ Store templates and contacts in **S3**
* ✅ Add **DynamoDB** for campaign history tracking
* ✅ Integrate **SNS** for notification alerts
* ✅ Add **API Gateway** to trigger emails on demand

---

### 🤝 Contributing

Feel free to fork the repo, open issues, and submit pull requests.
Contributions are welcome for:

* New features
* Better templates
* Multi-language email support

---

### 📜 License

This project is licensed under the **MIT License** – see the [LICENSE](LICENSE) file for details.



---

Would you like me to make this README **auto-generate badges, architecture diagram image (mermaid), and deployment commands section (for AWS SAM or Serverless Framework)** so it looks professional like production-grade repos?

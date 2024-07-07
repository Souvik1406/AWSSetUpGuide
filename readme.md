# Setting Up AWS S3 Buckets for CloudTrail Logs and CloudWatch Logs Export

## Step 1: Setting Up S3 Bucket for CloudTrail Logs

### 1.1 Create an S3 Bucket
1. Sign in to the AWS Management Console and open the [S3 console](https://console.aws.amazon.com/s3/).
2. Click on **Create bucket**.
3. Enter a unique name for your bucket (e.g., `my-cloudtrail-logs-bucket`).
4. Choose the region for your bucket (typically the same region as your CloudTrail trails for latency reasons).
5. Click **Create**.

### 1.2 Configure Bucket Policy for CloudTrail Logs
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AWSCloudTrailAclCheck",
            "Effect": "Allow",
            "Principal": {
                "Service": "cloudtrail.amazonaws.com"
            },
            "Action": "s3:GetBucketAcl",
            "Resource": "arn:aws:s3:::my-cloudtrail-logs-bucket"
        },
        {
            "Sid": "AWSCloudTrailWrite",
            "Effect": "Allow",
            "Principal": {
                "Service": "cloudtrail.amazonaws.com"
            },
            "Action": "s3:PutObject",
            "Resource": "arn:aws:s3:::my-cloudtrail-logs-bucket/AWSLogs/<YourAccountID>/*",
            "Condition": {
                "StringEquals": {
                    "s3:x-amz-acl": "bucket-owner-full-control"
                }
            }
        }
    ]
}
```
- Replace `my-cloudtrail-logs-bucket` with your actual bucket name.
- Replace `<YourAccountID>` with your AWS account ID.

### 1.3 Enable Logging in AWS CloudTrail
1. Open the [CloudTrail console](https://console.aws.amazon.com/cloudtrail).
2. Choose the trail for which you want to enable logging or create a new trail.
3. In the **Storage location** section, choose **Configure**.
4. Select **Existing S3 bucket** and choose the bucket you created (`my-cloudtrail-logs-bucket`).
5. Click **Save**.

## Step 2: Sending CloudWatch Logs to S3

### 2.1 Create an S3 Bucket for CloudWatch Logs (Optional)
If you want to archive CloudWatch Logs to S3, you need to create a separate S3 bucket for CloudWatch Logs.

### 2.2 Configure CloudWatch Logs to Export to S3
1. Sign in to the AWS Management Console and open the [CloudWatch console](https://console.aws.amazon.com/cloudwatch).
2. In the left navigation pane, choose **Logs**, then select the log group you want to export.
3. Choose **Actions** -> **Export data to S3**.
4. Configure the following settings:
   - **Destination S3 bucket**: Select the bucket where you want to export logs.
   - **S3 prefix**: (Optional) Enter a prefix for log object keys in S3.
   - **IAM role**: Choose an existing IAM role that grants CloudWatch Logs permission to write to S3.
   - **Encryption**: (Optional) Choose the encryption settings for the exported data.
5. Click **Start export**.

### Example IAM Policy for IAM Role (for CloudWatch Logs to S3)
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:PutObject",
                "s3:GetBucketAcl"
            ],
            "Resource": [
                "arn:aws:s3:::your-cloudwatch-logs-bucket/*",
                "arn:aws:s3:::your-cloudwatch-logs-bucket"
            ]
        }
    ]
}
```
- Replace `your-cloudwatch-logs-bucket` with your actual CloudWatch Logs S3 bucket name.

### 2.3 Verify Exported Logs in S3
After configuration, CloudWatch Logs will start exporting log data to the specified S3 bucket. You can verify this by checking the S3 bucket for exported log files.
```

This Markdown format provides a structured guide for setting up AWS S3 buckets for CloudTrail logs and configuring CloudWatch Logs to export logs to S3.

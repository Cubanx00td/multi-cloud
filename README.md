# Multi-Cloud Data Transfer with AWS and GCP

This project demonstrates how to set up a multi-cloud storage system that transfers data between Amazon S3 and Google Cloud Storage. It leverages Google's Storage Transfer Service to facilitate secure and efficient data movement between cloud providers.

## Overview

Multi-cloud storage strategies offer several benefits:
- Reduced dependency on a single vendor
- Cost optimization opportunities
- Improved data availability and redundancy
- Flexibility in choosing the best services from different providers

## Prerequisites

- An AWS account with administrative access
- A Google Cloud Platform account
- Basic familiarity with both AWS and GCP console interfaces
- AWS CLI (optional, for command-line operations)
- Google Cloud SDK (optional, for command-line operations)

## Project Timeline

Approximate completion time: 1 hour 30 minutes

## Steps

### 1. Setting up Data in AWS S3

1. Log in to your AWS Management Console
2. Navigate to the S3 service
3. Create a new S3 bucket with a unique name
   - Click "Create bucket"
   - Enter a globally unique bucket name
   - Select your preferred region
   - Configure additional settings as needed
   - Click "Create bucket"
4. Upload files to your new S3 bucket
   - Select your bucket
   - Click "Upload"
   - Select files (the example used 3 PDF files)
   - Click "Upload"

### 2. Creating an IAM Role for GCP Access

1. Navigate to the IAM service in AWS Console
2. Create a new custom IAM role
   - Click "Roles" in the left navigation
   - Click "Create role"
   - Select "Custom trust policy" as the trusted entity type
3. Configure the trust policy
   - Add a custom trust policy that identifies Google Cloud Storage based on its subject ID
   - This allows GCP's Storage Transfer Service to assume the role
   - Example trust policy structure (you'll need the specific GCP service account ID):
     ```json
     {
       "Version": "2012-10-17",
       "Statement": [
         {
           "Effect": "Allow",
           "Principal": {
             "Federated": "accounts.google.com"
           },
           "Action": "sts:AssumeRoleWithWebIdentity",
           "Condition": {
             "StringEquals": {
               "accounts.google.com:sub": "YOUR_GCP_SERVICE_ACCOUNT_ID"
             }
           }
         }
       ]
     }
     ```
4. Add permissions to the role
   - Attach policies that grant read access to S3 buckets
   - Follow the principle of least privilege by only granting necessary permissions
   - Example permissions needed:
     - `s3:GetObject`
     - `s3:ListBucket`
     - `s3:GetBucketLocation`

### 3. Setting up Google Cloud Storage Bucket

1. Log in to your Google Cloud Console
2. Navigate to Cloud Storage
3. Create a new bucket to serve as the destination
   - Click "Create bucket"
   - Enter a unique name for your bucket
   - Select your preferred region
   - Choose an appropriate storage class based on your access patterns
   - Configure additional settings as needed
   - Click "Create"

### 4. Configuring Storage Transfer Service

1. Navigate to Google Cloud's Storage Transfer Service
2. Set up a new transfer job
   - Click "Create transfer job"
   - Select "Amazon S3" as the source
   - Provide your AWS access details:
     - The ARN of the IAM role you created
     - Your S3 bucket name
3. Configure your destination
   - Select your newly created GCS bucket
4. Choose transfer options
   - Select between Batch transfer or Event-driven transfer
     - Batch: Transfers all data at once or on a schedule
     - Event-driven: Transfers data when new data is available
5. Set up the transfer schedule if using batch mode
6. Review and create the transfer job

### 5. Using a Manifest for Selective Transfer (Optional)

If you need to transfer only specific files rather than the entire bucket:

1. Create a manifest file listing the specific objects to transfer
2. In the Storage Transfer Service setup, select the manifest option
3. Specify the location of your manifest file
4. Complete the transfer job configuration as in step 4

### 6. Verifying the Transfer

1. Navigate to the Cloud Storage menu in GCP
2. Select your destination bucket
3. Verify that all files from your S3 bucket appear in the GCS bucket
4. Check the Storage Transfer Service jobs for completion status and any errors

## Troubleshooting

Common issues and solutions:

- **Transfer failures**: Check IAM permissions and ensure the role has the necessary access to S3.
- **Missing files**: Verify the S3 bucket name and confirm the transfer job completed successfully.
- **Access denied errors**: Double-check the trust policy in your IAM role and ensure the correct GCP service account ID is used.

## Security Considerations

- The identity federation approach used provides temporary credentials that typically expire in 15-60 minutes
- Following the principle of least privilege ensures that GCP only has the minimum required access to your AWS resources
- Consider encrypting sensitive data before transfer

## Next Steps

After completing this basic setup, consider:
- Automating regular transfers between clouds
- Setting up monitoring for transfer jobs
- Implementing data lifecycle policies on both clouds
- Exploring bi-directional transfer options

## Resources

- [AWS S3 Documentation](https://docs.aws.amazon.com/s3/)
- [Google Cloud Storage Documentation](https://cloud.google.com/storage/docs)
- [Storage Transfer Service Documentation](https://cloud.google.com/storage-transfer/docs)
- [NextWork.org](https://nextwork.org/) for more cloud computing projects

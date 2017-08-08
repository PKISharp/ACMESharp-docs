[ACMESharp Provider Extensions](../) > [Challenge Handlers](./) > aws-s3

# Challenge Handler: AWS S3

Provider for handling Challenges that manages file entries hosted in an AWS S3 bucket.  The handler depends on an appropriate DNS mapping to be configured externally that resolves to the target bucket.

| | |
|-|-|
| **Name:** | `aws-s3`
| **SupportedTypes:** | HTTP
| **IsCleanUpSupported:** | True
| **Assembly:** | `ACMESharp.Providers.AWS`
| **Version:** | `0.9.0.0`

## Parameters
---
### Bucket Name

Name of the S3 Bucket where files will be managed

| | |
|-|-|
| **Name:**          | `BucketName`
| **Type:**          | TEXT
| **IsRequired:**    | True
| **IsMultiValued:** | False

---
### IAM Access Key ID

Access Key ID of the IAM credential to use

| | |
|-|-|
| **Name:**          | `AwsAccessKeyId`
| **Type:**          | TEXT
| **IsRequired:**    | False
| **IsMultiValued:** | False

---
### IAM Secret Key

Secret Access Key of the IAM credential to use

| | |
|-|-|
| **Name:**          | `AwsSecretAccessKey`
| **Type:**          | TEXT
| **IsRequired:**    | False
| **IsMultiValued:** | False

---
### IAM Session Token

Session Token of the IAM credential to use

| | |
|-|-|
| **Name:**          | `AwsSessionToken`
| **Type:**          | TEXT
| **IsRequired:**    | False
| **IsMultiValued:** | False

---
### AWS Stored Credential Profile Name

The name of the stored credential profile

| | |
|-|-|
| **Name:**          | `AwsProfileName`
| **Type:**          | TEXT
| **IsRequired:**    | False
| **IsMultiValued:** | False

---
### AWS Stored Credential Profile Location

Overrides the default location to search for a stored credential profile

| | |
|-|-|
| **Name:**          | `AwsProfileLocation`
| **Type:**          | TEXT
| **IsRequired:**    | False
| **IsMultiValued:** | False

---
### IAM Role

Indicates an IAM Role to be used to resolve credentials; specify '*' to use first Role found

| | |
|-|-|
| **Name:**          | `AwsIamRole`
| **Type:**          | TEXT
| **IsRequired:**    | False
| **IsMultiValued:** | False

---
### API Endpoint Region

Region of the API endpoint to call

| | |
|-|-|
| **Name:**          | `AwsRegion`
| **Type:**          | TEXT
| **IsRequired:**    | False
| **IsMultiValued:** | False


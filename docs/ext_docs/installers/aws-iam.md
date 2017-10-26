[ACMESharp Provider Extensions](../) > [Installers](./) > aws-iam

# Installer: AWS IAM Server Certificate

Provider for uploading a certificate to IAM as a Server Certificate.

| | |
|-|-|
| **Name:** | `aws-iam`
| **IsUninstallSupported:** | False
| **Assembly:** | `ACMESharp.Providers.AWS`
| **Version:** | `0.9.1.0`

## Parameters
---
### Server Certificate Name

The unique Name that will identify the Server Certificate

| | |
|-|-|
| **Name:**          | `ServerCertificateName`
| **Type:**          | TEXT
| **IsRequired:**    | True
| **IsMultiValued:** | False

---
### IAM Certificate Path

Path under IAM to organize the Server Certificate (special considerations when used with CloudFront)

| | |
|-|-|
| **Name:**          | `Path`
| **Type:**          | TEXT
| **IsRequired:**    | False
| **IsMultiValued:** | False

---
### Use With CloudFront

Flag to indicate if the Server Certificate will be used with CloudFront

| | |
|-|-|
| **Name:**          | `UseWithCloudFront`
| **Type:**          | BOOLEAN
| **IsRequired:**    | False
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


[ACMESharp Provider Extensions](../) > [Installers](./) > aws-elb

# Installer: AWS Elastic Load Balancer

Provider for handling certificate installation to AWS Elastic Load Balancer.

| | |
|-|-|
| **Name:** | `aws-elb`
| **IsUninstallSupported:** | False
| **Assembly:** | `ACMESharp.Providers.AWS`
| **Version:** | `0.9.0.0`

## Parameters
---
### ELB Instance Name

The unique Name identifying the target ELB instance

| | |
|-|-|
| **Name:**          | `LoadBalancerName`
| **Type:**          | TEXT
| **IsRequired:**    | True
| **IsMultiValued:** | False

---
### ELB Listening Port

The listening port on the target ELB where the certificate will be installed

| | |
|-|-|
| **Name:**          | `LoadBalancerPort`
| **Type:**          | NUMBER
| **IsRequired:**    | True
| **IsMultiValued:** | False

---
### ELB Listening Protocol

The protocol used on the listening port on the target ELB, one of:  HTTPS, SSL

| | |
|-|-|
| **Name:**          | `LoadBalancerProtocol`
| **Type:**          | TEXT
| **IsRequired:**    | False
| **IsMultiValued:** | False

---
### ELB Listening Port

The listening port on the back-end instances of the ELB

| | |
|-|-|
| **Name:**          | `InstancePort`
| **Type:**          | NUMBER
| **IsRequired:**    | True
| **IsMultiValued:** | False

---
### ELB Listening Protocol

The protocol used  by the back-end instances of the ELB, one of:  HTTP, HTTPS, SSL, TCP

| | |
|-|-|
| **Name:**          | `InstanceProtocol`
| **Type:**          | TEXT
| **IsRequired:**    | False
| **IsMultiValued:** | False

---
### Existing IAM Server Certificate Name

An existing IAM Server Certificate name to install; either this *OR* the IAM Server Certificate installer parameters must be specified.

| | |
|-|-|
| **Name:**          | `ExistingServerCertificateName`
| **Type:**          | TEXT
| **IsRequired:**    | False
| **IsMultiValued:** | False

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


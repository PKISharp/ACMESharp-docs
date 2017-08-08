[ACMESharp Provider Extensions](../) > [Challenge Handlers](./) > aws-route53

# Challenge Handler: AWS Route 53

Provider for handling Challenges that manages DNS entries hosted in an AWS Route 53 zone.

| | |
|-|-|
| **Name:** | `aws-route53`
| **SupportedTypes:** | DNS
| **IsCleanUpSupported:** | True
| **Assembly:** | `ACMESharp.Providers.AWS`
| **Version:** | `0.8.5.0`

## Parameters
---
### Hosted Zone ID

The ID of Route 53 Zone to operate against

| | |
|-|-|
| **Name:**          | `HostedZoneId`
| **Type:**          | TEXT
| **IsRequired:**    | True
| **IsMultiValued:** | False

---
### Resource Record Type

Qualifies the type of RR that should be configured (TXT, A, CNAME)

| | |
|-|-|
| **Name:**          | `ResourceRecordType`
| **Type:**          | TEXT
| **IsRequired:**    | False
| **IsMultiValued:** | False

---
### Resource Record TTL

Specifies the time-to-live (TTL) in seconds of the RR (defaults to 300)

| | |
|-|-|
| **Name:**          | `ResourceRecordTtl`
| **Type:**          | TEXT
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


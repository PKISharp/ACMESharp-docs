[ACMESharp Provider Extensions](../) > [Challenge Handlers](./) > CloudFlare

# Challenge Handler: CloudFlare DNS

Provider for handling challenges that manages DNS entries hosted in a CloudFlare zone

| | |
|-|-|
| **Name:** | `CloudFlare`
| **SupportedTypes:** | DNS
| **IsCleanUpSupported:** | True
| **Assembly:** | `ACMESharp.Providers.CloudFlare`
| **Version:** | `0.9.0.0`

## Parameters
---
### Domain name

The domain name to operate against.

| | |
|-|-|
| **Name:**          | `DomainName`
| **Type:**          | TEXT
| **IsRequired:**    | True
| **IsMultiValued:** | False

---
### Email address

The email address of the CloudFlare account to operate against

| | |
|-|-|
| **Name:**          | `EmailAddress`
| **Type:**          | TEXT
| **IsRequired:**    | True
| **IsMultiValued:** | False

---
### Authentication Key

The authentication key of the CloudFlare account to operate against

| | |
|-|-|
| **Name:**          | `AuthKey`
| **Type:**          | TEXT
| **IsRequired:**    | True
| **IsMultiValued:** | False


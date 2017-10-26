[ACMESharp Provider Extensions](../) > [Challenge Handlers](./) > dnsme

# Challenge Handler: DNSMadeEasy Provider

A DNS Made Easy provider for handling Challenges. This provider supports the DNS Challenge type and computes all the necessary response values. It will create DNS entries in your account.

| | |
|-|-|
| **Name:** | `dnsme`
| **SupportedTypes:** | DNS
| **IsCleanUpSupported:** | False
| **Assembly:** | `ACMESharp.Providers.DNSMadeEasy`
| **Version:** | `0.9.1.0`

## Parameters
---
### API Key

The API Key for your DNS Made Easy account

| | |
|-|-|
| **Name:**          | `ApiKey`
| **Type:**          | TEXT
| **IsRequired:**    | True
| **IsMultiValued:** | False

---
### Secret Key

The Secret Key for your DNS Made Easy account

| | |
|-|-|
| **Name:**          | `SecretKey`
| **Type:**          | TEXT
| **IsRequired:**    | True
| **IsMultiValued:** | False

---
### Staging

True if we should use the staging API server

| | |
|-|-|
| **Name:**          | `Staging`
| **Type:**          | BOOLEAN
| **IsRequired:**    | False
| **IsMultiValued:** | False


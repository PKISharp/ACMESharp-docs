[ACMESharp Provider Extensions](../) > [Challenge Handlers](./) > manual

# Challenge Handler: Manual Provider

A manual provider for handling Challenges. This provider supports the DNS and HTTP Challenge types and computes all the necessary response values. It will provide instructions to the user on what to do with the values but actual steps must be implemented manually.

| | |
|-|-|
| **Name:** | `manual`
| **SupportedTypes:** | DNS, HTTP
| **IsCleanUpSupported:** | False
| **Assembly:** | `ACMESharp`
| **Version:** | `0.8.5.0`

## Parameters
---
### Write Out Path

Path to a file in which to write out manual instructions,or one of the special standard streams (OUT, ERR)

| | |
|-|-|
| **Name:**          | `WriteOutPath`
| **Type:**          | TEXT
| **IsRequired:**    | False
| **IsMultiValued:** | False

---
### Append

When true, output to a file will be appended

| | |
|-|-|
| **Name:**          | `Append`
| **Type:**          | BOOLEAN
| **IsRequired:**    | False
| **IsMultiValued:** | False

---
### Overwrite

When true, output to a file will overwrite the file

| | |
|-|-|
| **Name:**          | `Overwrite`
| **Type:**          | BOOLEAN
| **IsRequired:**    | False
| **IsMultiValued:** | False


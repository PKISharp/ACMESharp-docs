[ACMESharp Provider Extensions](../) > [Vault Storage](./) > local

# Vault Storage: Local Disk Vault

Vault provider based on system-local folder and files.

| | |
|-|-|
| **Name:** | `local`
| **Aliases:** | 
| **Assembly:** | `ACMESharp.Vault`
| **Version:** | `0.8.5.0`

## Parameters
---
### Root Path

Specifies the directory path where vault data files will be rooted.

| | |
|-|-|
| **Name:**          | `RootPath`
| **Type:**          | TEXT
| **IsRequired:**    | True
| **IsMultiValued:** | False

---
### Create Path

Specifies the Root Path should be created if it does not exist.

| | |
|-|-|
| **Name:**          | `CreatePath`
| **Type:**          | BOOLEAN
| **IsRequired:**    | True
| **IsMultiValued:** | False

---
### Bypass Encrypting File System (EFS)

Specifies not to use the OS-level support for encrypting files; this may be necessary on any file system that does not support EFS

| | |
|-|-|
| **Name:**          | `BypassEFS`
| **Type:**          | BOOLEAN
| **IsRequired:**    | False
| **IsMultiValued:** | False


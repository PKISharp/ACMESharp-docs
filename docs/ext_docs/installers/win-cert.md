[ACMESharp Provider Extensions](../) > [Installers](./) > win-cert

# Installer: Windows Certificate Store

Installer supprts importing certificate to the user or system Windows Certificate Store

| | |
|-|-|
| **Name:** | `win-cert`
| **IsUninstallSupported:** | False
| **Assembly:** | `ACMESharp.Providers.Windows`
| **Version:** | `0.9.1.0`

## Parameters
---
### Store Location

Optional store location (CurrentUser or LocalMachine);defaults to CurrentUser

| | |
|-|-|
| **Name:**          | `StoreLocation`
| **Type:**          | TEXT
| **IsRequired:**    | False
| **IsMultiValued:** | False

---
### Store Name

Optional store name

| | |
|-|-|
| **Name:**          | `StoreName`
| **Type:**          | TEXT
| **IsRequired:**    | False
| **IsMultiValued:** | False

---
### Friendly Name

Optional user-facing label to assign the certificate upon import

| | |
|-|-|
| **Name:**          | `FriendlyName`
| **Type:**          | TEXT
| **IsRequired:**    | False
| **IsMultiValued:** | False


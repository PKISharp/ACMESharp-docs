[ACMESharp Provider Extensions](../) > [Installers](./) > iis

# Installer: Internet Information Server (IIS)

Provider for installing certificates to a local IIS site

| | |
|-|-|
| **Name:** | `iis`
| **IsUninstallSupported:** | True
| **Assembly:** | `ACMESharp.Providers.IIS`
| **Version:** | `0.9.0.0`

## Parameters
---
### Web Site Ref

Either the Name or the ID of a local IIS Web Site

| | |
|-|-|
| **Name:**          | `WebSiteRef`
| **Type:**          | TEXT
| **IsRequired:**    | True
| **IsMultiValued:** | False

---
### Binding Address

An optional address to bind to; defaults to all interfaces

| | |
|-|-|
| **Name:**          | `BindingAddress`
| **Type:**          | TEXT
| **IsRequired:**    | False
| **IsMultiValued:** | False

---
### Binding Port

An optional port to bind to; defaults to HTTPS port (443)

| | |
|-|-|
| **Name:**          | `BindingPort`
| **Type:**          | NUMBER
| **IsRequired:**    | False
| **IsMultiValued:** | False

---
### Binding Host

An optional host name to bind to using SNI (IIS8+)

| | |
|-|-|
| **Name:**          | `BindingHost`
| **Type:**          | TEXT
| **IsRequired:**    | False
| **IsMultiValued:** | False

---
### Binding Host Required

An optional flag to indicate SNI is required (IIS8+)

| | |
|-|-|
| **Name:**          | `BindingHostRequired`
| **Type:**          | BOOLEAN
| **IsRequired:**    | False
| **IsMultiValued:** | False

---
### Force

An optional flag to overwrite an existing binding matching the target criteria

| | |
|-|-|
| **Name:**          | `Force`
| **Type:**          | BOOLEAN
| **IsRequired:**    | False
| **IsMultiValued:** | False

---
### Certificate Friendly Name

An optional user-facing label to assign the certificate upon import to the Windows Certificate Store.

| | |
|-|-|
| **Name:**          | `CertificateFriendlyName`
| **Type:**          | TEXT
| **IsRequired:**    | False
| **IsMultiValued:** | False


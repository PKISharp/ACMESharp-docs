[ACMESharp Provider Extensions](../) > [Challenge Handlers](./) > iis

# Challenge Handler: Internet Information Server (IIS)

Provider for handling Challenges that manages the local IIS site configuration.

| | |
|-|-|
| **Name:** | `iis`
| **SupportedTypes:** | HTTP
| **IsCleanUpSupported:** | True
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
### Override Site Root

The full path to the site root to use, overriding the value defined in the IIS Web Site settings.

| | |
|-|-|
| **Name:**          | `OverrideSiteRoot`
| **Type:**          | TEXT
| **IsRequired:**    | False
| **IsMultiValued:** | False

---
### Skip Local Web Config

When enabled, inhibits the generation of a local web.config file that controls MIME type mapping and IIS handler mapping.

| | |
|-|-|
| **Name:**          | `SkipLocalWebConfig`
| **Type:**          | BOOLEAN
| **IsRequired:**    | False
| **IsMultiValued:** | False


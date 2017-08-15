> NOTE:  This documentation applies to ACMESharp version 0.9.0 and later

The recommended approach to installing the latest release of the ACMESharp PowerShell client is from the [PowerShell Gallery](https://www.powershellgallery.com/) using the native `PowerShellGet` package management functionality.  (PowerShellGet is built-in with WMF5/PS5+ or can be [installed](https://docs.microsoft.com/en-us/powershell/gallery/psget/get_psget_module) for PS3+).

The ACMESharp PowerShell client is composed of several PowerShell modules:
* [ACMESharp](https://www.powershellgallery.com/packages/ACMESharp) - core module that provides the majority of cmdlets and base functionality, including support for interacting with an ACME CA (Let's Encrypt), managing extension providers and some basic provider implementations
* [ACMESharp Providers](https://www.powershellgallery.com/items?q=Tags%3A%22acmesharp_ext%22) - various extension modules that add functionality to the ACMESharp core through the extension provider model.  (More info about the [Extension Provider Model](https://github.com/ebekker/ACMESharp/wiki/%5BWIP%5D-ACMESharp-Details#extension-provider-model).)

To install and use the PowerShell client, you need to:
1. install the core module 
1. install one or more [extension modules](#acmesharp-extension-modules) based on your specific need
1. enable the extension module through the extension management cmdlets

## Installing ACMESharp PowerShell Modules

Note that you can install ACMESharp as system-wide PowerShell modules if you run as a privileged user, or as user-local PowerShell modules otherwise.  ACMESharp will work either way but some of its functionality will differ based on whether it executes in the context of a privileged user.  For example, local assets that are saved will be placed in different locations and some installers will be limited or forbidden unless executing in an elevated context.

### Install the Core Module

```PowerShell
PS> Install-Module ACMESharp
```

### Install one or more Extension Modules

Determine if you need to install one or more [extension modules](#acmesharp-extension-modules) to supplement core Providers.  In this example, we'll assume that we want to install Providers to support AWS and IIS.

```PowerShell
PS> Install-Module ACMESharp.Providers.AWS
PS> Install-Module ACMESharp.Providers.IIS
```

### Enable Extension Modules

Each extension module installed must be enabled so that it can be discovered and accessible by the core module cmdlets.

```PowerShell
PS> Import-Module ACMESharp
PS> Enable-ACMEExtensionModule ACMESharp.Providers.AWS
PS> Enable-ACMEExtensionModule ACMESharp.Providers.IIS
```

Now you should be able to list available providers for a particular extension type and see the added providers reflected in the list:

```PowerShell
## List the extension modules that are enabled
PS> Get-ACMEExtensionModule | select Name
Name
----
ACMESharp.Providers.AWS
ACMESharp.Providers.IIS

## List the discovered Challenge Handler Providers
PS> Get-ACMEChallengeHandlerProfile -ListChallengeHandlers
manual
aws-route53
aws-s3
iis

## List the discovered Installer Providers
PS> Get-ACMEInstallerProfile -ListInstallers
aws-iam
aws-elb
iis
```

## Default and Extended Providers

Several Extension Providers are included with the core PowerShell module and several others are available as extended Provided PowerShell modules.  (See what [extension types](https://github.com/ebekker/ACMESharp/wiki/%5BWIP%5D-ACMESharp-Details#extension-types) are possible.)

### ACMESharp Core Module

The core module includes support for the following providers out of the box:
* DNS Challenge Decoder for (`dns-01`)
* HTTP Challenge Decoder for (`http-01`)
* Manual Challenge Handler Provider for `dns` and `http` - generates instructions that must be implemented manually (`manual`)
* Default Vault Provider - simple, local file-based storage (`local`)
* Default PKI Tool - pure .NET implementation based on [BouncyCastle](http://bouncycastle.org/csharp/index.html) (`BouncyCastle`)
* *(no installers our provided out of the box)*

### ACMESharp Extension Modules

At present, the following additional extension providers are supported:
* **AWS** (`ACMESharp.Providers.AWS`):
  * Challenge Handler for `dns` *via **Route 53*** (`aws-elb`)
  * Challenge Handler for `http` *via **S3*** (`aws-s3`)
  * Installer *via **IAM Server Certificate*** (`aws-iam`)
  * Installer *via **IAM + ELB*** (`aws-elb`)
* **CloudFlare** (`ACMESharp.Providers.CloudFlare`):
  * Challenge Handler for `dns`
* **Microsoft IIS** (`ACMESharp.Providers.IIS`):
  * Challenge Handler for `http` (`iis`)
  * Installer (`iis`)
* **Microsoft Windows** (`ACMESharp.Providers.Windows`):
  * Installer *for **Windows Certificate Store*** (`???`)
* **Microsoft DNS**:
  * Challenge Handler for `dns` (`msdns`)
* **OpenSSL Library** - *deprecated*:
  * PKI Tool via OpenSSL native library (`OpenSSL-LIB`)
  * This module actually comes in two flavors since it is machine architecture-specific:
    * x86 Module (`ACMESharp.PKI.Providers.OpenSslLib32`)
    * x64 Module (`ACMESharp.PKI.Providers.OpenSslLib64`)
* **OpenSSL CLI** - *deprecated* (`ACMESharp.PKI.Providers.OpenSslCli`):
  * PKI Tool via the OpenSSL CLI executable (`OpenSSL-CLI`)

For more info, please see the [reference documentation](http://pkisharp.github.io/ACMESharp-docs/ext_docs) for standard Extension Providers.

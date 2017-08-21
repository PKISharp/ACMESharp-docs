The ACMESharp PowerShell modules make up the *client* that can be used by end-users to:
* talk to the Let's Encrypt CA (or any ACME-compliant service)
* manage local certificate assets used by ACME
* handle ACME challenges to prove domain ownership
* install ACME-issued certificates into a server or service

**If you just want to use ACMESharp to request and install certificates**, these are the components you want.  Please see the [instructions](https://github.com/ebekker/ACMESharp/wiki/%5BWIP%5D-Installation:-ACMESharp-PowerShell-client) on what to do with these links.

**If you are a *developer*** who wants to embed ACMESharp client libraries in your own projects, or who wants to develop extensions for ACMESharp, you should go [here](https://github.com/ebekker/ACMESharp/wiki/%5BWIP%5D-Downloads:-NuGet-Packages).

### PowerShell Modules

#### Stable Modules

| | Hosted on the [PowerShell Gallery](https://www.powershellgallery.com/items?q=Tags%3A%22acmesharp%22) |
|-|-|
| [![Powershellgallery Badge][psgallery-badge]][psgallery-base-status] | ACMESharp - version 0.9.0 base module
| Provider Modules:
| [![Powershellgallery Badge][psgallery-badge]][psgallery-aws-status] | AWS
| [![Powershellgallery Badge][psgallery-badge]][psgallery-cfl-status] | CloudFlare
| [![Powershellgallery Badge][psgallery-badge]][psgallery-iis-status] | Microsoft IIS
| [![Powershellgallery Badge][psgallery-badge]][psgallery-win-status] | Microsoft Windows


[psgallery-badge]: https://img.shields.io/badge/PowerShell_Gallery-LATEST-green.svg
[psgallery-base-status]: https://www.powershellgallery.com/packages/ACMESharp
[psgallery-aws-status]: https://www.powershellgallery.com/packages/ACMESharp.Providers.AWS
[psgallery-cfl-status]: https://www.powershellgallery.com/packages/ACMESharp.Providers.CloudFlare
[psgallery-iis-status]: https://www.powershellgallery.com/packages/ACMESharp.Providers.IIS
[psgallery-win-status]: https://www.powershellgallery.com/packages/ACMESharp.Providers.Windows

#### Pre-Release Modules

In order to use the pre-release modules, you need to define a PSRespository that points
to the pre-release feed and then reference that repository in your installation commands:
```PowerShell
## Pre-release example
Admin PS> Register-PSRepository -Name acmesharp-pre -PackageManagementProvider NuGet `
       >>     -InstallationPolicy Trusted `
       >>     -PublishLocation https://www.myget.org/F/acmesharp-posh-staging/api/v2/package `
       >>     -SourceLocation https://www.myget.org/F/acmesharp-posh-staging/api/v2
Admin PS> Install-Module -Name ACMESharp -Repository acmesharp-pre -AllowClobber
Admin PS> Install-Module -Name ACMESharp.Providers.IIS -Repository acmesharp-pre
```

| | Hosted on [MyGet](https://www.myget.org/gallery/acmesharp-posh-staging) |
|-|-|
| [![MyGet](https://img.shields.io/myget/acmesharp-posh-staging/v/ACMESharp.svg)](https://www.myget.org/feed/acmesharp-posh-staging/package/nuget/ACMESharp) | ACMESharp base module
| Provider Modules:
| [![MyGet](https://img.shields.io/myget/acmesharp-posh-staging/v/ACMESharp.Providers.AWS.svg)](https://www.myget.org/feed/acmesharp-posh-staging/package/nuget/ACMESharp.Providers.AWS) | AWS
| [![MyGet](https://img.shields.io/myget/acmesharp-posh-staging/v/ACMESharp.Providers.CloudFlare.svg)](https://www.myget.org/feed/acmesharp-posh-staging/package/nuget/ACMESharp.Providers.CloudFlare) | CloudFlare
| [![MyGet](https://img.shields.io/myget/acmesharp-posh-staging/v/ACMESharp.Providers.IIS.svg)](https://www.myget.org/feed/acmesharp-posh-staging/package/nuget/ACMESharp.Providers.IIS) | Microsoft IIS
| [![MyGet](https://img.shields.io/myget/acmesharp-posh-staging/v/ACMESharp.Providers.Windows.svg)](https://www.myget.org/feed/acmesharp-posh-staging/package/nuget/ACMESharp.Providers.Windows) | Microsoft Windows

Please see the [reference documentation](http://pkisharp.github.io/ACMESharp-docs/ext_docs) for the standard Provider Extensions.

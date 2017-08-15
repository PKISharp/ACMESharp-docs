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
| [![Powershellgallery Badge][psgallery-badge]][psgallery-status] | ACMESharp - complete 0.8.1 distribution

[psgallery-badge]: https://img.shields.io/badge/PowerShell_Gallery-LATEST-green.svg
[psgallery-status]: https://www.powershellgallery.com/packages/ACMESharp

#### Pre-Release Modules

| | Hosted on [MyGet](https://www.myget.org/gallery/acmesharp-posh-staging) |
|-|-|
| [![MyGet](https://img.shields.io/myget/acmesharp-posh-staging/v/ACMESharp.svg)](https://www.myget.org/feed/acmesharp-posh-staging/package/nuget/ACMESharp) | ACMESharp base module
| Provider Modules:
| [![MyGet](https://img.shields.io/myget/acmesharp-posh-staging/v/ACMESharp.Providers.AWS.svg)](https://www.myget.org/feed/acmesharp-posh-staging/package/nuget/ACMESharp.Providers.AWS) | AWS
| [![MyGet](https://img.shields.io/myget/acmesharp-posh-staging/v/ACMESharp.Providers.CloudFlare.svg)](https://www.myget.org/feed/acmesharp-posh-staging/package/nuget/ACMESharp.Providers.CloudFlare) | CloudFlare
| [![MyGet](https://img.shields.io/myget/acmesharp-posh-staging/v/ACMESharp.Providers.IIS.svg)](https://www.myget.org/feed/acmesharp-posh-staging/package/nuget/ACMESharp.Providers.IIS) | Microsoft IIS
| [![MyGet](https://img.shields.io/myget/acmesharp-posh-staging/v/ACMESharp.Providers.Windows.svg)](https://www.myget.org/feed/acmesharp-posh-staging/package/nuget/ACMESharp.Providers.Windows) | Microsoft Windows

Please see the [reference documentation](http://pkisharp.github.io/ACMESharp-docs/ext_docs) for the standard Provider Extensions.

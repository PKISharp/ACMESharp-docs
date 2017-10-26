# User Guide: ACMESharp PowerShell Client

> NOTE: This documentation applies to ACMESharp **version 0.9.0** and later

This document presents a detailed look at each step needed to use the **ACMESharp PowerShell Client**.  This is for users that want to generate and install PKI certificates, for example, from the Let's Encrypt CA project.

# The Long Version

This User Guide is a companion to the [Quick Start guide](Quick-Start.md) and covers all the same steps and the same sample scenario, but with more details and explanation.

To following along with this guide, you should first review and familiarize yourslef with the [outline of steps](Quick-Start.md#overview) and the description of the [Sample Scenario](Quick-Start.md#sample-scenario) from the Quick Start guide.  To reduce duplication, they won't be repeated here.

Let's begin...

> **PLEASE NOTE:** If at any time for the following sequence of steps you encounter an **`UnauthorizedAccessException`** exception or an error along the lines of **`Access to path denied`**, please see [this article](Local-Vault-EFS.md) for possible solution.

## Step 1: Install ACMESharp

The ACMESharp PowerShell client is published to the [PowerShell Gallery](https://www.powershellgallery.com/items?q=Tags%3A%22acmesharp%22) as a set of *PowerShell Modules*.  This is the official way that the client is distributed, and it's also the easiest way to install it.

> In order to install ACMESharp from the Gallery, you need to have support for **`PowerShellGet`** in your PowerShell environment.  Since PowerShell v5, this is included by default.  If you are using an earlier version of PowerShell, you can [install](https://docs.microsoft.com/en-us/powershell/gallery/psget/get_psget_module) support separately for v3 and v4.

We will need to install two (2) PowerShell modules from the Gallery:
* ACMESharp core module
* ACMESharp Provider Extension for IIS

In an elevated PowerShell prompt, execute:
```powershell
Admin PS> Install-Module -Name ACMESharp -AllowClobber
## Add support for working with IIS
Admin PS> Install-Module -Name ACMESharp.Providers.IIS
```
> Why do we need to specify the `-AllowClobber` flag?  The explanation is [here](Why-AllowClobber.md) >TODO<

Next, you need to enable the Provider Extension module:
```powershell
Admin PS> Import-Module ACMESharp
Admin PS> Enable-ACMEExtensionModule -ModuleName ACMESharp.Providers.IIS
## Verify the module was enabled
Admin PS> Get-ACMEExtensionModule | Select-Object -Expand Name
ACMESharp.Providers.IIS
```

> For more details about installation and alternative variations, please see the [installation docs](https://github.com/ebekker/ACMESharp/wiki/%5BWIP%5D-Installation:-ACMESharp-PowerShell-client) >TODO<.

## Step 2: Initialize Vault

ACMESharp needs a place to store various state and assets while interacting with an ACME server.  The PowerShell client uses a virtual storage provider simply called a **Vault**.  The simplest type of Vault is the *local* Vault, which just stores all of its assets on the local file system at a well-known path by default.  Because we are assuming running as an elevated user, the path will be restricted only to local Administrators.

A Vault instance is tied to a specific *ACME server endpoint*.  That is, when you initialize the Vault you specify the default base URL that will be used for all ACME communication going forward.  If you don't specify an endpoint, the default is the Let's Encrypt production endpoint, which is what we want to use for this scenario.

```powershell
## Make sure ACMESharp module is loaded in the current PS session
## NOTE: we will skip this command in subsequent steps because we
## assume that we're operating in a single PS session -- if you
## switch sessions in between steps, be sure to import the module
Admin PS> Import-Module ACMESharp
Admin PS> Initialize-ACMESharp
```

> This will use the *default Vault profile* for Administrators, which defines the path to the Vault storage directory as `C:\ProgramData\ACMESharp\sysVault`.  For more details about Vault profiles and configuration alternatives, please see the [Vault Details](Vault-Details.md) >TODO< documentation.

Up to this point, everything we have done has been confined to the local machine.  In the next step, we begin interacting with the ACME server and formally begin the ACME workflow.

## Step 3: Register Account

The ACME protocol defines a resource known as an *Account* which is essentially a logical container for metadata and other resources that are managed by ACME, such as Contacts, Identifiers and Certificates.  An Account is uniquely identified using a *key pair* - the private key is kept secret and maintained securely in the ACMESharp Vault.  The public key is shared with the ACME server when the Account is first *Registered*.  Afterwards, all actions under a given account are securely authenticated using the private key.

There are three (3) things that are required to successfully register a new Account:
1. A public/private key pair - as described above, used for future authentication.  ACMESharp automatically generate a new key pair an securely store it in the Vault.  All future operations will be automatically authenticated using the private key.
2. A contact list - used by an ACME server to notify an Account owner of various events such as an expiring Certificate or security incident.  A contact is defined using a URI format that defines the contact type and contact details.  While the ACME protocol is open-ended about the types of contacts that can be specified (e.g. email, telephone), the Let's Encrypt CA only supports email contact types, specified as URIs of the format `mailto:<email-address>`
3. An agreement to Terms-of-Service - the ACME server can publish a URL that defines its terms and conditions for using its services.  A client is required to agree to these terms of service in order to proceed.

To register a new Account, execute:
```powershell
Admin PS> New-ACMERegistration -Contacts mailto:some-user@example.com -AcceptTos
```

> This will register a new Account and agree to the ToS in one step.  If you want more details about the registration process and and how to update Account attributes, see the [Account Registration details](Account-Registration-Details.md) >TODO< documentation.

## Step 4: Validate Identifiers

Before an ACME server can issue a certificate for a DNS *Identifier* under an Account, it must be assured that the Identifier, or domain name, is under the control of the Account.  To do this, the ACME server issues some combination of *Challenges*.  The Account holder must complete the appropriate number and combination of Challenges in order to *validate* control of the DNS Identifier.  The ACME protocol defines a number of Challenge types, and Let's Encrypt only requires that we complete one of them to validate and Identifier.

> At this time, ACMESharp only supports two types, namely DNS-01 and HTTP-01.  Other types will be added in the future.

In this scenario we will be using the Challenge type known as `http-01`.  This Challenge proves control over a domain by placing a file whose content is defined by the ACME server at a well-known location on a web server that responds to requests over HTTP at the DNS domain name matching the DNS Identifier.

To begin the validation process, we submit the DNS Identifier to the ACME server:
```powershell
## We start by validating the first DNS domain name in our scenario
Admin PS> New-ACMEIdentifier -Dns www.example.com -Alias www-example-com
```

At the start of this guide we identified three (3) DNS Identifiers that we want to secure with a PKI certificate.  Here we start with the first one on that list.  Also we see the first incarnation of something that is intrinsic to ACMESharp and important to understand moving forward, the concept of an *Alias*.

### Introducing Resource Aliases

Because the ACME protocol defines a sequence of steps that generate and access various resources over the course of its normal workflow, it is necessary to reference resources in subsequent steps that were created in preceding steps.  ACMESharp tries to simplify this process by defining Aliases for these resources, which are simply labels that identify the associated resource or asset.

In the example above, we defined the Alias `www-example-com` to represent the Identifier `www.example.com`.  Aliases have to be unique within their resource type, but can be reused across types.  For example, another resource type that we will see below are Certificates.  We can reuse the label `www-example-com` for a Certificate resource because it is distinct from Identifier resources.  The rules for naming an Alias are pretty free-form *except* for a couple rules:
* **An Alias cannot start with an equals (`=`)**
* **An Alias cannot start with a number or minus (`0-9`,`-`)**

In the example above, we could have just as easily used the DNS name itself as the Alias (`www.example.com`) but we wanted to demonstrate that the Resource and the Alias are independent of each other.  Additionally, we do not have to assign an Alias to the Identifier resource (or any resource for that matter), this is strictly an optional step.  However, in the absence of an Alias, a Resource would later have to be referenced either by its formal unique ID (GUID) or by a relative sequence number (SeqNum).  Both of these alternatives are discouraged as they are more cumbersome to work with and neither of them reflects any intrinsic association to the Resource they represent.

> For more information about Aliases, please see the [Resources](Resources,-References-and-Aliases.md) overview documentation >TODO<.

### Handling an Identifier Challenge

When we submitted the DNS Identifier above, the ACME server responded with some details about what Challenges it supports and metadata for each one that we will use to satisfy each Challenge.  All of this happens behind the scenes and ACMESharp saves all the relevant details in the Vault for us, but if we wanted to inspect the details we can query for the Identifier resource saved to the Vault.  For example, if we want to see all the Challenges and the combinations that were returned by Let's Encrypt, we can issue the following:

```powershell
## Get the Challenge types returned by the ACME server
Admin PS> Get-ACMEIdentifier www-example-com | select -expand Challenges | select Type
Type
----
http-01
dns-01
tls-sni-01

## Get the combination of Challenges allowed by the ACME server
Admin PS> Get-ACMEIdentifier www-example-com | select -expand Combinations
0
1
2
```

As you can see, Let's Encrypt three (3) different types of Challenges (two of which are supported by ACMESharp) and it only require any one of them to be completed to validate the Identifier.  As per the scenario details, we're going to make use of the `http-01` Challenge type, with our IIS server.

To that end we need to make use of a *Challenge Handler* -- a component that knows how to handle a particular Challenge Type and can do so using a server or service of our choosing.  In our case need a Challenge Handler that supports IIS and the HTTP Challenge type.  In the installation step above, we made sure to install and enable the IIS Provider, which includes the Challenge Handler we need.

We can see what Challenge Handlers are available, as well as what Challenge types they support and what parameters they support:
```powershell
## List available Challenge Handlers
Admin PS> Get-ACMEChallengeHandlerProfile -ListChallengeHandlers
manual
iis

## Get details about the IIS Handler
Admin PS> Get-ACMEChallengeHandlerProfile -GetChallengeHandler iis

Name               : iis
Label              : Internet Information Server (IIS)
SupportedTypes     : HTTP
Description        : Provider for handling Challenges that manages the local IIS site configuration.
IsCleanUpSupported : True
Parameters         : {{ Name = WebSiteRef, Label = Web Site Ref... ((TRUNCATED-FOR-CLARITY)) }}
```

We see that two different Handlers are available to us.  Besides the `manual` Handler which is always available, we have the `iis` Handler.  We query for additional details about this Handler and see some metadata that describes the Handler, what Challenge Types it supports, and what parameters it supports.  We can get a cleaner view of the parameters, if we exclude everything else:
```powershell
Admin PS> Get-ACMEChallengeHandlerProfile -GetChallengeHandler iis -ParametersOnly

Name          : WebSiteRef
Label         : Web Site Ref
Type          : TEXT
IsRequired    : True
IsMultiValued : False
Description   : Either the Name or the ID of a local IIS Web Site

Name          : OverrideSiteRoot
Label         : Override Site Root
Type          : TEXT
IsRequired    : False
IsMultiValued : False
Description   : The full path to the site root to use, overriding the value defined in the IIS Web Site settings.

Name          : SkipLocalWebConfig
Label         : Skip Local Web Config
Type          : BOOLEAN
IsRequired    : False
IsMultiValued : False
Description   : When enabled, inhibits the generation of a local web.config file that controls MIME type mapping and
                IIS handler mapping.
```

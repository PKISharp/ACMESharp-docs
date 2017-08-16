> NOTE: This documentation applies to ACMESharp **version 0.9.0** and later

This document outlines the basic steps to get up and running quickly with the **ACMESharp PowerShell client**.  This is for users that want to generate and install PKI certificates, for example, from the **Let's Encrypt CA** project.

This document will show these basic steps to get up and running:
1. **Install ACMESharp** - installing the ACMESharp PowerShell client
2. **Initialize Vault** - initializing a local Vault to store generated assets
3. **Register Account** - register a new ACME account
4. **Validate Identifiers** - prove you control one or more DNS domains
5. **Generate Certificate** - request and retrieve a PKI certificate
6. **Install Certificate** - install the PKI certificate to a server or service

### Quick Start Scenario

Because there are many variations for each of the steps outlined above depending on your particular use case, we will define a fairly common scenario as an example to follow in this guide.  Within each section corresponding to the steps above, we will link to more detailed instructions that describe other options that may suit your needs better.

The scenario that we will follow in this Quick Start guide will be as follows:
* You are an Administrator with elevated privileges on a Windows Server
* You have access to an email address `some-user@example.com`
* You have IIS running on the local server and you want to obtain and install a PKI certificate
* Your IIS is accessible publicly on the Internet at the following DNS addresses:
  * `www.example.com`
  * `www.example.net`
  * `example.com`
* Your IIS site is named `MyExampleSite`
* Your IIS site is listening on port 80
* You want to obtain a free certificate from the [Let's Encrypt](https://www.letsencrypt.org) CA server.
* You want the certificate to identify `www.example.com` as the primary DNS name of your server, but also allow all the other names as alternatives.
* You want to install the free certificate into your IIS site and enable HTTPS traffic over the standard port (443).

Let's begin...

# TL;DR

Here we present the *really quick* Quick Start, only showing the steps needed to accomplish the scenario described above.
Following that we step through each of the commands and explain them in more detail.

```PowerShell
##
## Step 1:
## Install the base ACMESharp module which includes all the cmdlets
Admin PS> Install-Module -Name ACMESharp -AllowClobber
## Add support for working with IIS
Admin PS> Install-Module -Name ACMESharp.Providers.IIS
## Enable extension modules needed for IIS
Admin PS> Import-Module ACMESharp
Admin PS> Enable-ACMEExtensionModule -ModuleName ACMESharp.Providers.IIS
## Verify the module was enabled
Admin PS> Get-ACMEExtensionModule | Select-Object -Expand Name
## You should see this:
ACMESharp.Providers.IIS

## Step 2:
## Initialize a local ACMESharp Vault to store our state and assets
Admin PS> Initialize-ACMESharp

## Step 3:
## Register a new account with Let's Encrypt
Admin PS> New-ACMERegistration -Contacts mailto:some-user@example.com -AcceptTos

## Step 4:
## We start by validating the first DNS domain name in our scenario
Admin PS> New-ACMEIdentifier -Dns www.example.com -Alias www-example-com
## Make sure the IIS Challenge Handler is available
Admin PS> Get-ACMEChallengeHandlerProfile -ListChallengeHandlers 
## You should see this:
manual
iis
## Handle the challenge
Admin PS> Complete-ACMEChallenge -IdentifierRef www-example-com -ChallengeType http-01 -Handler iis -HandlerParams @{ WebSiteRef = 'MyExampleSite' }
Admin PS> Submit-ACMEChallenge -IdentifierRef www-example-com -ChallengeType http-01
## You should see something like (note the "Status" is "Pending"):
IdentifierType : dns
Identifier     : www.example.com
Uri            : https://acme-v01.api.letsencrypt.org/acme/authz/J-C2p4ZjSEcQSIbDI_kAeMBrHs_mJsP6x0uZaLVlZdA
Status         : pending
Expires        : 7/22/2017 6:15:39 PM
Challenges     : {, , iis}
Combinations   : {2, 1, 0}

## You need to give Let's Encrypt some time to process the submission
## and validate the challenge response -- usually it updates almost
## immediately, but we'll wait a little bit just to be sure...
Admin PS> sleep -s 60
## Update the status of the Identifier
Admin PS> Update-ACMEIdentifier -IdentifierRef pki1-acmetesting
## You should see something like (note the "Status" is "Valid"):
IdentifierPart : ACMESharp.Messages.IdentifierPart
IdentifierType : dns
Identifier     : www.example.com
Uri            : https://acme-v01.api.letsencrypt.org/acme/authz/J-C2p4ZjSEcQSIbDI_kAeMBrHs_mJsP6x0uZaLVlZdA
Status         : valid
Expires        : 9/14/2017 6:46:08 PM
Challenges     : {, , }
Combinations   : {2, 1, 0}

## We complete the same set of steps for the remaining DNS Identifiers
##  * www.example.net
##  * example.com
Admin PS> New-ACMEIdentifier -Dns www.example.net -Alias www-example-net
Admin PS> New-ACMEIdentifier -Dns example.com     -Alias root-example-com

Admin PS> Complete-ACMEChallenge -IdentifierRef www-example-net -ChallengeType http-01 -Handler iis -HandlerParams @{ WebSiteRef = 'MyExampleSite' }
Admin PS> Complete-ACMEChallenge -IdentifierRef root-example-com -ChallengeType http-01 -Handler iis -HandlerParams @{ WebSiteRef = 'MyExampleSite' }

Admin PS> Submit-ACMEChallenge -IdentifierRef www-example-net -ChallengeType http-01
Admin PS> Submit-ACMEChallenge -IdentifierRef root-example-com -ChallengeType http-01

## Give it a minute, just in case
Admin PS> sleep -s 60

Admin PS> Update-ACMEIdentifier -IdentifierRef www-example-net
Admin PS> Update-ACMEIdentifier -IdentifierRef root-example-com


## Step 5:
## Request and retrieve a certificate
Admin PS> New-ACMECertificate -Generate -IdentifierRef www-example-com -AlternativeIdentifierRefs @('www-example-net','root-example-com') -Alias cert-example-domains
## You should see something like this:
Id                       : 8071b73c-2fed-45df-93b0-85936aacb761
Alias                    : cert-example-domains
Label                    :
Memo                     :
IdentifierRef            : fa52309d-4184-4a8b-ad53-1682c15c3f03
IdentifierDns            : www.example.com
AlternativeIdentifierDns : {www.example.org, example.com}
KeyPemFile               :
CsrPemFile               :
GenerateDetailsFile      : 8071b73c-2fed-45df-93b0-85936aacb761-gen.json
CertificateRequest       :
CrtPemFile               :
CrtDerFile               :
IssuerSerialNumber       :
SerialNumber             :
Thumbprint               :
Signature                :
SignatureAlgorithm       :

## Submit the certificate request to Let's Encrypt:
Admin PS> Submit-ACMECertificate -CertificateRef cert-example-domains

## You should see something like this (note the 'IssuerSerialNumber' is missing):
Id                       : 60b69dd8-c806-40c7-af03-503597c13dcd
Alias                    : cert-example-domains
Label                    :
Memo                     :
IdentifierRef            : fa52309d-4184-4a8b-ad53-1682c15c3f03
IdentifierDns            : www.example.com
AlternativeIdentifierDns : {www.example.org, example.com}
KeyPemFile               : 60b69dd8-c806-40c7-af03-503597c13dcd-key.pem
CsrPemFile               : 60b69dd8-c806-40c7-af03-503597c13dcd-csr.pem
GenerateDetailsFile      : 60b69dd8-c806-40c7-af03-503597c13dcd-gen.json
CertificateRequest       : ACMESharp.CertificateRequest
CrtPemFile               : 60b69dd8-c806-40c7-af03-503597c13dcd-crt.pem
CrtDerFile               : 60b69dd8-c806-40c7-af03-503597c13dcd-crt.der
IssuerSerialNumber       :
SerialNumber             : 04671C4F6AF87F2DC3E4694C304EA8647CE1
Thumbprint               : 442A4AE5CC098ACBD0FC94A18F2AC59750EB724B
Signature                : 442A4AE5CC098ACBD0FC94A18F2AC59750EB724B
SignatureAlgorithm       : sha256RSA

## You need to do one update in order to retrieve the CA signer's public cert:
Admin PS> Update-ACMECertificate -CertificateRef cert-example-domains

## You should see something like this (note the 'IssuerSerialNumber' is populated):
Id                       : 60b69dd8-c806-40c7-af03-503597c13dcd
Alias                    : cert-pki1
Label                    :
Memo                     :
IdentifierRef            : fa52309d-4184-4a8b-ad53-1682c15c3f03
IdentifierDns            : pki1.acmetesting.ezstest.com
AlternativeIdentifierDns : {pki1-alt.acmetesting.ezstest.com}
KeyPemFile               : 60b69dd8-c806-40c7-af03-503597c13dcd-key.pem
CsrPemFile               : 60b69dd8-c806-40c7-af03-503597c13dcd-csr.pem
GenerateDetailsFile      : 60b69dd8-c806-40c7-af03-503597c13dcd-gen.json
CertificateRequest       : ACMESharp.CertificateRequest
CrtPemFile               : 60b69dd8-c806-40c7-af03-503597c13dcd-crt.pem
CrtDerFile               : 60b69dd8-c806-40c7-af03-503597c13dcd-crt.der
IssuerSerialNumber       : 0A0141420000015385736A0B85ECA708
SerialNumber             : 04671C4F6AF87F2DC3E4694C304EA8647CE1
Thumbprint               : 442A4AE5CC098ACBD0FC94A18F2AC59750EB724B
Signature                : 442A4AE5CC098ACBD0FC94A18F2AC59750EB724B
SignatureAlgorithm       : sha256RSA

## Step 6:
## Install the certificate into your site
Admin PS> Get-ACMEInstallerProfile -ListInstallers
## You see this:
iis


```



# Step 1: Install ACMESharp

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
> Why do we need to specify the `-AllowClobber` flag?  The explanation is [here](why-allowclobber) >TODO<

Next, you need to enable the Provider Extension module:
```powershell
Admin PS> Import-Module ACMESharp
Admin PS> Enable-ACMEExtensionModule -ModuleName ACMESharp.Providers.IIS
## Verify the module was enabled
Admin PS> Get-ACMEExtensionModule | Select-Object -Expand Name
ACMESharp.Providers.IIS
```

> For more details about installation and alternative variations, please see the [installation docs](https://github.com/ebekker/ACMESharp/wiki/%5BWIP%5D-Installation:-ACMESharp-PowerShell-client) >TODO<.

# Step 2: Initialize Vault

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

> This will use the *default Vault profile* for Administrators, which defines the path to the Vault storage directory as `C:\ProgramData\ACMESharp\sysVault`.  For more details about Vault profiles and configuration alternatives, please see the [Vault Details](https://github.com/ebekker/ACMESharp/wiki/%5BWIP%5D-Vault-Details) >TODO< documentation.

Up to this point, everything we have done has been confined to the local machine.  In the next step, we begin interacting with the ACME server and formally begin the ACME workflow.

# Step 3: Register Account

The ACME protocol defines a resource known as an *Account* which is essentially a logical container for metadata and other resources that are managed by ACME, such as Contacts, Identifiers and Certificates.  An Account is uniquely identified using a *key pair* - the private key is kept secret and maintained securely in the ACMESharp Vault.  The public key is shared with the ACME server when the Account is first *Registered*.  Afterwards, all actions under a given account are securely authenticated using the private key.

There are three (3) things that are required to successfully register a new Account:
1. A public/private key pair - as described above, used for future authentication.  ACMESharp automatically generate a new key pair an securely store it in the Vault.  All future operations will be automatically authenticated using the private key.
2. A contact list - used by an ACME server to notify an Account owner of various events such as an expiring Certificate or security incident.  A contact is defined using a URI format that defines the contact type and contact details.  While the ACME protocol is open-ended about the types of contacts that can be specified (e.g. email, telephone), the Let's Encrypt CA only supports email contact types, specified as URIs of the format `mailto:<email-address>`
3. An agreement to Terms-of-Service - the ACME server can publish a URL that defines its terms and conditions for using its services.  A client is required to agree to these terms of service in order to proceed.

To register a new Account, execute:
```powershell
Admin PS> New-ACMERegistration -Contacts mailto:some-user@example.com -AcceptTos
```

> This will register a new Account and agree to the ToS in one step.  If you want more details about the registration process and and how to update Account attributes, see the [Account Registration details](https://github.com/ebekker/ACMESharp/wiki/%5BWIP%5D-Account-Registration-Details) >TODO< documentation.

# Step 4: Validate Identifiers

Before an ACME server can issue a certificate for a DNS *Identifier* under an Account, it must be assured that the Identifier, or domain name, is under the control of the Account.  To do this, the ACME server issues some combination of *Challenges*.  The Account holder must complete the appropriate number and combination of Challenges in order to *validate* control of the DNS Identifier.  The ACME protocol defines a number of Challenge types, and Let's Encrypt only requires that we complete one of them to validate and Identifier.

> At this time, ACMESharp only supports two types, namely DNS-01 and HTTP-01.  Other types will be added in the future.

In this scenario we will be using the Challenge type known as `http-01`.  This Challenge proves control over a domain by placing a file whose content is defined by the ACME server at a well-known location on a web server that responds to requests over HTTP at the DNS domain name matching the DNS Identifier.

To begin the validation process, we submit the DNS Identifier to the ACME server:
```powershell
## We start by validating the first DNS domain name in our scenario
Admin PS> New-ACMEIdentifier -Dns www.example.com -Alias www-example-com
```

At the start of this guide we identified three (3) DNS Identifiers that we want to secure with a PKI certificate.  Here we start with the first one on that list.  Also we see the first incarnation of something that is intrinsic to ACMESharp and important to understand moving forward, the concept of an *Alias*.

## Introducing Resource Aliases

Because the ACME protocol defines a sequence of steps that generate and access various resources over the course of its normal workflow, it is necessary to reference resources in subsequent steps that were created in preceding steps.  ACMESharp tries to simplify this process by defining Aliases for these resources, which are simply labels that identify the associated resource or asset.

In the example above, we defined the Alias `www-example-com` to represent the Identifier `www.example.com`.  Aliases have to be unique within their resource type, but can be reused across types.  For example, another resource type that we will see below are Certificates.  We can reuse the label `www-example-com` for a Certificate resource because it is distinct from Identifier resources.  The rules for naming an Alias are pretty free-form *except* for a couple rules:
* **An Alias cannot start with an equals (`=`)**
* **An Alias cannot start with a number or minus (`0-9`,`-`)**

In the example above, we could have just as easily used the DNS name itself as the Alias (`www.example.com`) but we wanted to demonstrate that the Resource and the Alias are independent of each other.  Additionally, we do not have to assign an Alias to the Identifier resource (or any resource for that matter), this is strictly an optional step.  However, in the absence of an Alias, a Resource would later have to be referenced either by its formal unique ID (GUID) or by a relative sequence number (SeqNum).  Both of these alternatives are discouraged as they are more cumbersome to work with and neither of them reflects any intrinsic association to the Resource they represent.

> For more information about Aliases, please see the [Resources](https://github.com/ebekker/ACMESharp/wiki/%5BWIP%5D-Resources,-References-and-Aliases) overview documentation >TODO<.

## Handling an Identifier Challenge

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

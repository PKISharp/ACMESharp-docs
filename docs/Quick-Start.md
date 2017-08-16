# Quick Start: ACMESharp PowerShell Client

> NOTE: This documentation applies to ACMESharp **version 0.9.0** and later

This document runs through the basic steps to get up and running quickly with the **ACMESharp PowerShell client**.  This is for users that want to generate and install PKI certificates, for example, from the **Let's Encrypt CA** project.

## TL;DR

This *Quick Start* guide will only present the simplest form of each step needed to accomplish the scenario described below.  By design, there is very little detail or explanation of what is being done.  If you would like more detail and explanation for each step, please check out the detailed [User Guide](User-Guide) which runs through the same scenario as this Quick Start document, but explains each step in more detail and provides links for even more information.

## Overview

These are the steps that we will run through in the scenario described below:

Step | Name | Description
-----|------|------------
  1. | **Install ACMESharp**    | installing the ACMESharp PowerShell client
  2. | **Initialize Vault**     | initializing a local Vault to store generated assets
  3. | **Register Account**     | register a new ACME account
  4. | **Validate Identifiers** | prove you control one or more DNS domains
  5. | **Generate Certificate** | request and retrieve a PKI certificate
  6. | **Install Certificate**  | install the PKI certificate to a server or service

## Sample Scenario

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
* You want to install the free certificate into your IIS site and enable HTTPS traffic over the standard port (443) *and* the alternate port 8443, but only for requests targeting `www.example.net` (this last binding is only supported on newer versions of IIS that support SNI).

Let's begin...

## Step 1: Install ACMESharp
Install the base ACMESharp module which includes all the cmdlets and the Extension Provider module for working with IIS:

```PowerShell
Admin PS> Install-Module -Name ACMESharp -AllowClobber
Admin PS> Install-Module -Name ACMESharp.Providers.IIS

## Enable extension modules needed for IIS
Admin PS> Import-Module ACMESharp
Admin PS> Enable-ACMEExtensionModule -ModuleName ACMESharp.Providers.IIS

## Verify the module was enabled
Admin PS> Get-ACMEExtensionModule | Select-Object -Expand Name
## You should see this:
ACMESharp.Providers.IIS
```

## Step 2: Initialize Vault
Initialize a local ACMESharp Vault to store our state and assets:

```PowerShell
Admin PS> Initialize-ACMEVault
```

## Step 3: Register Account
Register a new account with Let's Encrypt:

```PowerShell
Admin PS> New-ACMERegistration -Contacts mailto:some-user@example.com -AcceptTos
```

## Step 4: Validate Identifiers
We start by validating the first DNS domain name in our scenario: `www.example.com`

```PowerShell
## Make sure the IIS Challenge Handler is available
Admin PS> Get-ACMEChallengeHandlerProfile -ListChallengeHandlers 
## You should see this:
manual
iis

## Create a new Identifier with Let's Encrypt
Admin PS> New-ACMEIdentifier -Dns www.example.com -Alias www-example-com

## Handle the challenge using HTTP validation on IIS
Admin PS> Complete-ACMEChallenge -IdentifierRef www-example-com -ChallengeType http-01 -Handler iis -HandlerParams @{ WebSiteRef = 'MyExampleSite' }

## Tell Let's Encrypt it's OK to validate now
Admin PS> Submit-ACMEChallenge -IdentifierRef www-example-com -ChallengeType http-01
## You should see something like this (note the "Status" is "Pending"):
IdentifierType : dns
Identifier     : www.example.com
Uri            : https://acme-v01.api.letsencrypt.org/acme/authz/J-C2p4ZjSEcQSIbDI_kAeMBrHs_mJsP6x0uZaLVlZdA
Status         : pending
Expires        : 7/22/2017 6:15:39 PM
Challenges     : {, , iis}
Combinations   : {2, 1, 0}
```

You need to give Let's Encrypt some time to process the submission and validate the challenge
response -- usually it updates almost immediately, but we'll wait a little bit just to be sure.

```PowerShell
## This suspends your PS session for a minute, or you
## can skip this command and just go grab some tea...
Admin PS> sleep -s 60

## Update the status of the Identifier
Admin PS> Update-ACMEIdentifier -IdentifierRef pki1-acmetesting
## You should see something like this (note the "Status" is "Valid"):
IdentifierPart : ACMESharp.Messages.IdentifierPart
IdentifierType : dns
Identifier     : www.example.com
Uri            : https://acme-v01.api.letsencrypt.org/acme/authz/J-C2p4ZjSEcQSIbDI_kAeMBrHs_mJsP6x0uZaLVlZdA
Status         : valid
Expires        : 9/14/2017 6:46:08 PM
Challenges     : {, , }
Combinations   : {2, 1, 0}
```

We complete the same set of steps for the remaining DNS Identifiers
 * `www.example.net`
 * `example.com`

```PowerShell
Admin PS> New-ACMEIdentifier -Dns www.example.net -Alias www-example-net
Admin PS> New-ACMEIdentifier -Dns example.com     -Alias root-example-com

Admin PS> Complete-ACMEChallenge -IdentifierRef www-example-net -ChallengeType http-01 -Handler iis -HandlerParams @{ WebSiteRef = 'MyExampleSite' }
Admin PS> Complete-ACMEChallenge -IdentifierRef root-example-com -ChallengeType http-01 -Handler iis -HandlerParams @{ WebSiteRef = 'MyExampleSite' }

Admin PS> Submit-ACMEChallenge -IdentifierRef www-example-net -ChallengeType http-01
Admin PS> Submit-ACMEChallenge -IdentifierRef root-example-com -ChallengeType http-01

## Give it a minute, just in case -- or go get more coffee
Admin PS> sleep -s 60

Admin PS> Update-ACMEIdentifier -IdentifierRef www-example-net
Admin PS> Update-ACMEIdentifier -IdentifierRef root-example-com
```

## Step 5: Generate Certificate
Request and retrieve a certificate:

```PowerShell
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
```

## Step 6: Install Certificate
Install the certificate into your site:

```PowerShell
## Make sure the IIS Installer is available
Admin PS> Get-ACMEInstallerProfile -ListInstallers
## You should see this:
iis

## Install the cert on the default HTTPS binding
Admin PS> Install-ACMECertificate -CertificateRef cert-pki1 -Installer iis -InstallerParameters @{
  WebSiteRef = 'MyExampleSite'
}

## Install the cert on the alternate HTTPS binding, but only for one DNS name
Admin PS> Install-ACMECertificate -CertificateRef cert-pki1 -Installer iis -InstallerParameters @{
  WebSiteRef = 'MyExampleSite'
  BindingHost = 'www.example.net'
  BindingPort = 8443
  CertificateFriendlyName = 'LetsEncryptCert'
}
```

And that's all folks.

---

For a more detailed look at the steps above, please check out the [User Guide](User-Guide).

# Why do we need `-AllowClobber`?

In the documentation for ACMESharp, whenever we describe installing the PowerShell Module client
we use the `Install-Module` cmdlet and we usually recommend specifying the `-AllowClobber`
parameter.

What is this parameter and why do we need it?

## What is clobbering?

By default when we use the `Install-Module` command (which is part of PowerShellGet or simply
*PSGet*) to install a new module to the local system, it queries what commands are exported by
the requested module and checks that list against all the existing modules already installed
on the system to see if there are any conflicts, i.e. if any of the commands have the same name.
If it finds any you will get an error and the installation will be aborted:

```PowerShell
## For example, this command
Install-Module -Name ACMESharp

## Might produce this error
**PackageManagement\Install-Package : A command with name 'Get-Certificate' is already available on this system. This module 'ACMESharp' may override the existing commands. If you still want to install this module 'ACMESharp', use -AllowClobber parameter.**
```

PSGet has a good reason for doing this -- it's possible for a new module to override or *clobber*
an existing command already available from another module, including core system modules that
provide many of the foundational commands users expect to find in a PowerShell installation.

## When to allow clobbering and is it safe?

In order to override the clobber-checking behavior you have explicitly specify the
`-AllowClobber` parameter flag when installing a new module.  This will allow the module to be
installed, but you have to be aware that the *last module imported* wins when it comes to
conflicting command names.

So if you have two modules with the same command exported, and both
are imported into the current session, the one that is imported second will have its command
exported last and thus it will be available in the session as the default command with that name.

This is safe as long as you are aware of it and intentionally override the default behavior,
and you have to do this if you want to make use of the new module on the local system.

## How to work with clobbered commands?

Now there are several ways that module authors and module users can avoid this situation
altogether, as well as handle the situation if there are conflicting commands.

### Command Prefixes by Convention

For starters, many module authors will typically define a common prefix which they will prepend
to the noun component of a command that follows the convention of <Verb>-<Noun>.  For example,
a module that defines commands for working with DNS records might prefix all of its nouns with
`DNS` as in `Get-DNSRecord` and `Remove-DNSZone` and `Start-DNSZoneTransfer`.  A vendor that
publishes a module for their products might use a prefix for their name or their product name
such as `AWS` or `EC2`.  This convention *hardcodes* the prefix within the command name, so it's
a permanent part of the command name.

### Command Prefixes at Time-of-Import

An alternative that's available to module users is to use the `-Prefix` parameter when importing
a module.  When specified, the value given to this parameter will be prepended to the noun component
of each exported command of the imported module.  For example, if we have a module `DateUtil` that
defines a `Get-Date` command, we can import it with the prefix `UTC` to make sure this command
doesn't clobber the original command of the same name that comes out-of-the-box with PowerShell:

```PowerShell
PS> Import-Module -Prefix UTC -Name DateUtil
PS> Get-Date      ## Calls the PowerShell built-in command
PS> Get-UTCDate   ## Calls the module command
```

### Default Command Prefixes

Combining the last two techniques is actually a third approach.  Instead of hardcoding a prefix
into the name of every exported command, a module author can specify a *default prefix* in the
module manifest.  This has the same effect as hardcoding the prefix when the module is imported
by default, however it also allows a module user to substitute their own prefix at the time of
import if she chooses to.  For example, the `ACMESharp` module defines a default prefix of
`ACME` so every command that it exports will contain a noun component that starts with that
prefix, as in:

* `Get-ACMEVault`
* `New-ACMECertificate`
* `Install-ACMECertificate`

But a user can choose a different prefix at the time of import using the `-Prefix` parameter
and it will produce a different set of exported command names into the current session.  If
we use a prefix of `LetsEncrypt`, the above 3 sample commands would become:

* `Get-LetsEncryptVault`
* `New-LetsEncryptCertificate`
* `Install-LetsEncryptCertificate`

### Fully-qualified Commands

Lastly, one option that is always available to users and that removes any ambiguity is to
fully qualify a command name.  A fully qualified command name, or *module-qualified* command
has been available since PowerShell 3.0 and it often needed when writing scripts or modules
that will be executed on system that you have no knowledge of and cannot anticipate its
state or configuration.

A module-qualified command uses the form `<Module>\<Command>` as in:
```PowerShell
PS> ACMESharp\Install-Certificate -CertificateRef my-cert -Installer iis -InstallerParameters @{ WebSiteRef = "Default Web Site" }
```

## What about ACMESharp? Why should we use `-AllowClobber`?

Now that we understand what clobbering is and why and when we need the `-AllowClobber`
parameter, what about ACMESharp?  ACMESharp uses the *default prefix* approach.  It
specifies a default command prefix of `ACME` but a user can choose to override this
prefix with one of their own at the time of import just as in the example above.

So then why do we recommend you specify the `-AllowClobber` flag when you install the
module?  Simple -- lots of users have reported (such as
[here](https://github.com/ebekker/ACMESharp/issues/159) and
[here](https://github.com/ebekker/ACMESharp/issues/168)) getting the clobber warning when trying
to install ACMESharp.  Specifically they get a conflict with the ACMESharp `Get-ACMECertificate`
command and the `Get-Certificate` command that was introduced to PowerShell with version 5.0.

As you can see the actual *resolved* default command names, that is, the naked commands you would
use by default in your PowerShell session, are distinctly different.  So what's the issue?

The ultimate issue in this case is a bug in the PowerShellGet module that handles installation
of modules and does this conflict detection.  This is a known [bug](https://github.com/PowerShell/PowerShellGet/issues/45) and for now the only way to work around
it is to specify the `-AllowClobber` flag which circumvents the conflict detection.  As long as
you trust the module provider and you have properly vetted that steps have been taken to
avoid any real conflicts (as we have hopefully demonstrated above), this is a safe workaround.


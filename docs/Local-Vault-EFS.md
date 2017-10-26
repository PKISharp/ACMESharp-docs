---
title: Local Vault and EFS
---

# Local Vault and EFS

## What is EFS?
The *Encrypting File System* or [EFS](https://en.wikipedia.org/wiki/Encrypting_File_System)
is a feature built into certain versions of Windows which supports toggling a flag on
certain files and directories so that they are stored encrypted on disk.

## Why do we use it?
The `local` Vault provider, which is the default provider (and only one at the time of this writing),
uses the local file system to store all of its assets.  For assets that are flagged as
sensitive, such as a certificate's private key, the Vault provider will attempt to store
these assets with the EFS flag enabled for better security.

## When does it fail?
However, there are cases when EFS is not supported because the OS or the
local file system does not support it.  In order to have support for EFS
you have to use the NTFS file system and you have to be using a version
of Windows geared toward *business environments*.  That means that server
version of Windows will support it and versions of client or desktop
Windows that are Professional edition or better will support it, but
lesser editions, such as Home editions will not.

In environments where EFS is not supported, any attempt to store files with
the EFS flag enabled will fail with a `System.UnauthorizedAccessException`
error and a message to the effect of `...Access to the path '...' is denied`.

There are **other scenarios** where an attempt to use EFS may fail, even if
you are using a supported OS and file system, such as if a Windows Domain
has been configured to disable EFS through the use of GPO.  We won't list
out all the possibilities here.  However you can test out the use of EFS
on your system by running this little piece of PowerShell code and seeing
if it succeeds:

```powershell
$efsTestFile = "$($env:ProgramData)\testing-efs.txt"
## If EFS isn't supported, it will fail with this call to Create
$x = [System.IO.File]::Create($efsTestFile, 100, "Encrypted")
$x.WriteByte(65)
$x.Close()
del $efsTestFile
```

## What can we do about it?
This error is meant to warn you that you will storing this sensitive
information in the clear.  If you wish to forgoe this warning, then
you can override the default behavior of the `local` provider by enabling
an optional flag [`BypassEFS`](ext_docs/vaults/local.md#bypass-encrypting-file-system-efs) on the Vault *profile*.

*However*, you will be required to create and use a *custom* Vault Profile
to do so.  Vault defines two (2) *built-in* Vault Profiles:
* `:sys` - the default system-wide Vault if you are running as an elevated user (admin)
* `:user` - the default user-specific Vault if you are running as a non-elevated user

The built-in Vault Profiles are always available, so they cannot be deleted
and they always have a pre-defined configuration set including their resolved
path and that they *do not bypass the EFS flag* -- as a security measure.

If you want to use ACMESharp on a system that does not support EFS, then you
have to create a custom Vault Profile using the `Set-ACMEVaultProfile` command
and you need to specify the `BypassEFS` provider parameter with a value of
`false`, for example:

```powershell
PS> Set-ACMEVaultProfile -ProfileName my-vault -Provider local -VaultParameters @{ RootPath = "$env:LocalAppData\MyAcmeVault"; CreatePath = $true; BypassEFS = $true }
PS> Initialize-ACMEVault -VaultProfile my-vault
PS> dir $env:LocalAppData\MyAcmeVault

    Directory: C:\Users\YourUserName\AppData\Local\MyAcmeVault

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        8/22/2017   5:01 PM              3 .acme.vault
-a----        8/22/2017   5:01 PM           1067 00-VAULT
```

In the script above, we created a new Vault Profile that defines the root location
of our Vault directory.  In this case we specify a sub-directory of our user local
app data folder.  We also set the flag that allows us to create that folder if it
doesn't exist when we initialize the Vault, and we also enabled the flag for
bypassing the use of EFS.

Notice how we had to specify the `-VaultProfile` parameter with the value of our
newly created custom profile name when we called to `Initialize-ACMEVault`.  If
didn't specify this parameter, then ACMESharp would resolve to one of the default
built-in profiles (either `:sys` or `:user`).  So in order to make sure that
ACMESharp is using our new custom profile, we have to specify the Vault profile
name with each ACMESharp command we invoke.

There is however an alternative if you don't want to have to specify the Vault
profile name each time -- ACMESharp will look for the environment variable
`ACMESHARP_VAULT_PROFILE` in the current PS session.  If it finds one, then it
will use that as the default Vault profile name for every command invoked.
You can still override the Vault profile for each command invocation using the
`-VaultProfile` parameter, but by default it will use the value in that
environment variable.

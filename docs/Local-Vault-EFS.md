---
title: Local Vault and EFS
---

# Local Vault and EFS

The *Encrypting File System* or [EFS](https://en.wikipedia.org/wiki/Encrypting_File_System)
is a feature built into certain versions of Windows which supports toggling a flag on
certain files and directories so that they are stored encrypted on disk.

The `local` Vault provider, which is the default provider (and only one at the time of this writing),
uses the local file system to store all of its assets.  For assets that are flagged as
sensitive, such as a certificate's private key, the Vault provider will attempt to store
these assets with the EFS flag enabled for better security.

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

This error is meant to warn you that you will storing this sensitive
information in the clear.  If you wish to forgoe this warning, then
you can override the default behavior of the `local` provider by enabling
an optional flag `BypassEFS` on the Vault *profile*.


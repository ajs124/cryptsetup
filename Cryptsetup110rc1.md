### cryptsetup 1.1.0-rc1 ###
Released on 2009-09-29.

The first cryptsetup 1.1.0-rc1 release candidate is available at

> http://code.google.com/p/cryptsetup/

> Download [cryptsetup-1.1.0-rc1.tar.bz2](http://cryptsetup.googlecode.com/files/cryptsetup-1.1.0-rc1.tar.bz2)

Feedback and bug reports are welcomed.

### changes since version 1.0.7 ###

Important changes:

  * Adds new libcryptsetup API (documented in libcryptsetup.h).

> The old API (using crypt\_options struct) is still available but will remain
> frozen and not used for new functions.
> Soname of library changed to libcryptsetup.so.1.0.0.
> (But only recompilation should be needed for old programs.)

> The new API provides much more flexible operation over LUKS device for
> applications, it is preferred that new applications will use libcryptsetup
> and not wrapper around cryptsetup binary.

  * Adds luksHeaderBackup and luksHeaderRestore commands.

> These commands allows binary backup of LUKS header.
> Please read man page about possible security issues with backup files.

  * Adds luksSuspend (freeze device and wipe key) and luksResume (with provided passphrase).

> luksSuspend wipe encryption key in kernel memory and set device to suspend
> (blocking all IO) state. This option can be used for situations when you need
> temporary wipe encryption key (like suspend to RAM etc.)
> Please read man page for more information.

  * Adds --master-key-file option for luksFormat and luksAddKey.

> User can now specify pre-generated master key in file, which allows regenerating
> LUKS header or add key with only master key knowledge.

  * Uses libgcrypt and enables all gcrypt hash algorithms for LUKS through -h luksFormat option.

> Please note that using different hash for LUKS header make device incompatible with
> old cryptsetup releases.

  * Introduce --debug parameter.

> Use when reporting bugs (just run cryptsetup with --debug and attach output
> to issue report.) Sensitive data are never printed to this log.

  * Move command successful messages to verbose level.

  * Require device-mapper library and libgcrypt to build.

  * Use dm-uuid for all crypt devices, contains device type and name now.

  * Remove support for dangerous non-exclusive option
> (it is ignored now, LUKS device must be always opened exclusive)

Other changes:

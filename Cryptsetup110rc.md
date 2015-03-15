### cryptsetup 1.1.0-rc4 ###
Released on 2009-12-30.

The cryptsetup 1.1.0-rc1 release candidate is available at

> http://code.google.com/p/cryptsetup/

> Download [cryptsetup-1.1.0-rc4.tar.bz2](http://cryptsetup.googlecode.com/files/cryptsetup-1.1.0-rc4.tar.bz2)

Feedback and bug reports are welcomed.

Cryptsetup 1.1.0-rc4 Release Notes


The rc should be last release before 1.1 stable version, the reason for another
testing compilation is important change in default paramaters.

### Changes since version 1.1.0-rc1 ###

  * **IMPORTANT: the default compiled-in cipher parameters changed**
    * plain mode: aes-cbc-essiv:sha256 (default is backward incompatible!).
    * LUKS mode: aes-cbc-essiv:sha256 (only key size increased)
    * In both modes is now default key size 256bits.

  * Default compiled-in parameters are now cofigurable through configure options:
> > ` --with-plain-* / --with-luks1-* ` (see configure --help)

  * If you need bacwkard compatible defaults for distribution use
> > ` configure --with-plain-mode=cbc-plain --with-luks1-keybits=128 `


> (This is mainly important for plain mode when the mode is stored in /etc/crypttab file.)

> Default compiled-in modes are printed in "cryptsetup --help" output.

  * **IMPORTANT: change in iterations count:**
  * The slot and key digest iteration minimum is now 1000.
  * The key digest iteration # is calculated from iteration time (approx 1/8 of req. time).

> For more info about above items see discussion [here](http://tinyurl.com/yaug97y)

Other changes:
~~* Fixed localisation to work again. Also cryptsetup is now translated by transaltionproject.org.
  * Added French, Indonesian and updated Swedish translation for now.
  * Fix some libcryptsetup probles, including
    * exported symbols and versions in libcryptsetup (properly use versionend symbols)
    * Add crypt\_log library function.
    * Add CRYPT prefix to enum defined in libcryptsetup.h.
  * Move duplicate Command failed message to verbose level (error is printed always).
  * Fix several problems in build system
    * use autopoint and clean gettext processing.
    * Check in configure if selinux libraries are required in static version.
    * Fix build for non-standard location of gcrypt library.
  * Add temporary debug code to find processes locking internal device.
  * Fix error handling during reading passhrase.~~

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

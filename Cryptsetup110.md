### cryptsetup 1.1.0 ###
Released on 2010-01-17.

The stable cryptsetup 1.1.0 release is available at

> http://code.google.com/p/cryptsetup/

> Download [cryptsetup-1.1.0.tar.bz2](http://cryptsetup.googlecode.com/files/cryptsetup-1.1.0.tar.bz2)

Feedback and bug reports are welcomed. You can use  [New issue](http://code.google.com/p/cryptsetup/issues/entry) report page.

## Cryptsetup 1.1.0 Release Notes ##

### Changes since version 1.0.7 ###

  * **IMPORTANT: the default compiled-in cipher parameters changed**
    * plain mode: aes-cbc-essiv:sha256 (default is backward incompatible!).
    * LUKS mode: aes-cbc-essiv:sha256 (only key size increased)
    * In both modes is now default key size 256bits.
    * Default compiled-in parameters are now cofigurable through configure options:
> > > ` --with-plain-* / --with-luks1-* ` (see configure --help)
    * If you need backward compatible defaults for distribution use

> > ` configure --with-plain-mode=cbc-plain --with-luks1-keybits=128 `

  * Default compiled-in modes are printed in "cryptsetup --help" output.

  * Change in iterations count (LUKS):

> The slot and key digest iteration minimum count is now 1000.
> The key digest iteration count is calculated from iteration time (approx 1/8 of req. time).
> For more info about above items see discussion here: http://tinyurl.com/yaug97y

  * New libcryptsetup API (documented in libcryptsetup.h).

> The old API (using crypt\_options struct) is still available but will remain
> frozen and not used for new functions.
> Soname of library changed to libcryptsetup.so.1.0.0.
> (But only recompilation should be needed for old programs.)

> The new API provides much more flexible operation over LUKS device for
> applications, it is preferred that new applications will use libcryptsetup
> and not wrapper around cryptsetup binary.

  * New luksHeaderBackup and luksHeaderRestore commands.

> These commands allows binary backup of LUKS header.
> Please read man page about possible security issues with backup files.

  * New luksSuspend (freeze device and wipe key) and luksResume (with provided passphrase).

> luksSuspend wipe encryption key in kernel memory and set device to suspend
> (blocking all IO) state. This option can be used for situations when you need
> temporary wipe encryption key (like suspend to RAM etc.)
> Please read man page for more information.

  * New --master-key-file option for luksFormat and luksAddKey.

> User can now specify pre-generated master key in file, which allows regenerating
> LUKS header or add key with only master key knowledge.

  * Uses libgcrypt and enables all gcrypt hash algorithms for LUKS through -h luksFormat option.

> Please note that using different hash for LUKS header make device incompatible with
> old cryptsetup releases.

  * Introduces --debug parameter.

> Use when reporting bugs (just run cryptsetup with --debug and attach output
> to issue report.) Sensitive data are never printed to this log.

  * Moves command successful messages to verbose level.

  * Requires device-mapper library and libgcrypt to build.

  * Uses dm-uuid for all crypt devices, contains device type and name now.

  * Removes support for dangerous non-exclusive option
> (it is ignored now, LUKS device must be always opened exclusive)

Other changes:

### Cryptsetup 1.6.4 ###
Released on 2014-02-27.

The stable cryptsetup 1.6.4 release is available at

> https://code.google.com/p/cryptsetup/

> Download [cryptsetup-1.6.4.tar.xz](https://www.kernel.org/pub/linux/utils/cryptsetup/v1.6/cryptsetup-1.6.4.tar.xz) and unpacked tar signature [cryptsetup-1.6.4.tar.sign](https://www.kernel.org/pub/linux/utils/cryptsetup/v1.6/cryptsetup-1.6.4.tar.sign)

Feedback and bug reports are welcomed. You can use  [New issue](http://code.google.com/p/cryptsetup/issues/entry) report page.

## Cryptsetup 1.6.4 Release Notes ##

### Changes since version 1.6.3 ###

  * Implement new erase (with alias luksErase) command.

> The erase cryptsetup command can be used to permanently erase all keyslots and make the LUKS container inaccessible.<br>(The only way to unlock such device is to use LUKS header backup created before erase command was used.)</li></ul>

<blockquote>You do not need to provide any password for this operation.

> This operation is irreversible.

  * Add internal "whirlpool\_gcryptbug hash" for accessing flawed<br>Whirlpool hash in gcrypt (requires gcrypt 1.6.1 or above).</blockquote>

> The gcrypt version of Whirlpool hash algorithm was flawed in some situations.

> This means that if you used Whirlpool in LUKS header and upgraded to new gcrypt library your LUKS container become inaccessible.

> Please refer to cryptsetup FAQ for detail how to fix this situation.

  * Allow to use --disable-gcrypt-pbkdf2 during configuration to force use internal PBKDF2 code.

  * Require gcrypt 1.6.1 for imported implementation of PBKDF2<br>(PBKDF2 in gcrypt 1.6.0 is too slow).</li></ul>

<ul><li>Add --keep-key to cryptsetup-reencrypt.</li></ul>

<blockquote>This allows change of LUKS header hash (and iteration count) without the need to reencrypt the whole data area.<br>(Reencryption of LUKS header only without master key change.)</blockquote>

  * By default verify new passphrase in luksChangeKey and luksAddKey commands (if input is from terminal).

  * Fix memory leak in Nettle crypto backend.

  * Support --tries option even for TCRYPT devices in cryptsetup.

  * Support --allow-discards option even for TCRYPT devices.<br>(Note that this could destroy hidden volume and it is not suggested by original TrueCrypt security model.)</li></ul>

<ul><li>Link against -lrt for clock_gettime to fix undefined reference to clock_gettime error (introduced in 1.6.2).</li></ul>

<ul><li>Fix misleading error message when some algorithms are not available.</li></ul>

<ul><li>Count system time in PBKDF2 benchmark if kernel returns no self usage info.<br>(Workaround to broken getrusage() syscall with some hypervisors.)</li></ul>


#### Release notes for [cryptsetup 1.6.0](Cryptsetup160.md). ####
#### Release notes for [cryptsetup 1.6.1](Cryptsetup161.md). ####
#### Release notes for [cryptsetup 1.6.2](Cryptsetup162.md). ####
#### Release notes for [cryptsetup 1.6.3](Cryptsetup163.md). ####
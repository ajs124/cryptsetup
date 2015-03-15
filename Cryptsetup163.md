### Cryptsetup 1.6.3 ###
Released on 2013-12-13.

The stable cryptsetup 1.6.3 release is available at

> http://code.google.com/p/cryptsetup/

> Download [cryptsetup-1.6.3.tar.bz2](http://cryptsetup.googlecode.com/files/cryptsetup-1.6.3.tar.bz2)

Feedback and bug reports are welcomed. You can use  [New issue](http://code.google.com/p/cryptsetup/issues/entry) report page.

## Cryptsetup 1.6.3 Release Notes ##

### Changes since version 1.6.2 ###

  * Fix cryptsetup reencryption tool to work properly with devices using 4kB sectors.

  * Always use page size if running through loop device,<br>this fixes failures for external LUKS header and filesystem requiring 4kB block size.</li></ul>

<ul><li>Fix TCRYPT system encryption mapping for multiple partitions.<br>Since this commit, one can use partition directly as device parameter.<br>If you need to activate such partition from image in file,<br>please first use map partitioned loop device (losetup -P) on image.<br>(Cryptsetup requires partition offsets visible in kernel sysfs in this mode.)</li></ul>

<ul><li>Support activation of old TrueCrypt containers using CBC mode and whitening (created in TrueCrypt version < 4.1).<br>This requires Linux kernel 3.13 or later.<br>(Containers with cascade CBC ciphers are not supported.)</li></ul>

<ul><li>Properly display keys in dump --dump-master-key command for TrueCrypt CBC containers.</li></ul>

<ul><li>Rewrite cipher benchmark loop which was unreliable on very fast machines.</li></ul>

<ul><li>Add warning if LUKS device was activated using non-cryptsetup library which did not set UUID properly (e.g. cryptmount).<br>(Some commands, like luksSuspend, are not available then.)</li></ul>

<ul><li>Support length limitation also for plain (no hash) length.<br>This can be used for mapping problematic cryptosystems which wipes some part of key (losetup sometimes set last 32 byte to zero,<br>which can be now configured as --hash plain:31 parameter).</li></ul>

<ul><li>Fix hash limit if parameter is not a number.<br>(The whole key was set to zero instead of command failure.)</li></ul>

<ul><li>Unify --key-slot behavior in cryptsetup_reencrypt tool.</li></ul>

<ul><li>Update dracut example scripts for system reencryption on first boot.</li></ul>

<ul><li>Add command line option --tcrypt-backup to access TCRYPT backup header.</li></ul>

<ul><li>Fix static compilation with OpenSSL.</li></ul>

<h4>Release notes for <a href='Cryptsetup160.md'>cryptsetup 1.6.0</a>.</h4>
<h4>Release notes for <a href='Cryptsetup161.md'>cryptsetup 1.6.1</a>.</h4>
<h4>Release notes for <a href='Cryptsetup162.md'>cryptsetup 1.6.2</a>.</h4>
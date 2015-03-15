### Cryptsetup 1.6.6 ###
Released on 2014-08-16.

The stable cryptsetup 1.6.6 release is available at

> https://code.google.com/p/cryptsetup/

> Download [cryptsetup-1.6.6.tar.xz](https://www.kernel.org/pub/linux/utils/cryptsetup/v1.6/cryptsetup-1.6.6.tar.xz) and unpacked tar signature [cryptsetup-1.6.6.tar.sign](https://www.kernel.org/pub/linux/utils/cryptsetup/v1.6/cryptsetup-1.6.6.tar.sign)

Feedback and bug reports are welcomed. You can use  [New issue](http://code.google.com/p/cryptsetup/issues/entry) report page.

## Cryptsetup 1.6.6 Release Notes ##

### Changes since version 1.6.5 ###
  * LUKS: Fix keyslot device access for devices which do not support direct IO operations.<br>(Regression in 1.6.5.)</li></ul>

<ul><li>LUKS: Fallback to old temporary keyslot device mapping method if hash (for ESSIV)<br>is not supported by userspace crypto library.<br>(Regression in 1.6.5.)</li></ul>

<ul><li>Properly activate device with discard (TRIM for SSDs) if requested even if dm_crypt module is not yet loaded.<br>Only if discard is not supported by the old kernel then the discard option is ignored.</li></ul>

<ul><li>Fix some static analysis build warnings (scan-build).</li></ul>

<ul><li>Report crypto lib version only once (and always add kernel version) in debug output.</li></ul>

<b>Cryptsetup API NOTE</b><br>The direct terminal handling for passphrase entry will be removed from libcryptsetup in next major version (application should handle it itself).<br>
<br>
It means that you have to always either provide password in buffer or set your own password callback function through crypt_set_password_callback().<br>See API documentation (or libcryptsetup.h) for more info.<br>
<br>
<br>
<h4>Release notes for <a href='Cryptsetup160.md'>cryptsetup 1.6.0</a>.</h4>
<h4>Release notes for <a href='Cryptsetup161.md'>cryptsetup 1.6.1</a>.</h4>
<h4>Release notes for <a href='Cryptsetup162.md'>cryptsetup 1.6.2</a>.</h4>
<h4>Release notes for <a href='Cryptsetup163.md'>cryptsetup 1.6.3</a>.</h4>
<h4>Release notes for <a href='Cryptsetup164.md'>cryptsetup 1.6.4</a>.</h4>
<h4>Release notes for <a href='Cryptsetup165.md'>cryptsetup 1.6.5</a>.</h4>
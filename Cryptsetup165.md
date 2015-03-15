### Cryptsetup 1.6.5 ###
Released on 2014-06-29.

The stable cryptsetup 1.6.5 release is available at

> https://code.google.com/p/cryptsetup/

> Download [cryptsetup-1.6.5.tar.xz](https://www.kernel.org/pub/linux/utils/cryptsetup/v1.6/cryptsetup-1.6.5.tar.xz) and unpacked tar signature [cryptsetup-1.6.5.tar.sign](https://www.kernel.org/pub/linux/utils/cryptsetup/v1.6/cryptsetup-1.6.5.tar.sign)

Feedback and bug reports are welcomed. You can use  [New issue](http://code.google.com/p/cryptsetup/issues/entry) report page.

## Cryptsetup 1.6.5 Release Notes ##

### Changes since version 1.6.4 ###
  * Allow LUKS header operation handling without requiring root privilege.

> It means that you can manipulate with keyslots as a regular user, only write access to device (or image) is required.

> This requires kernel crypto wrapper (similar to TrueCrypt device handling) to be available (CRYPTO\_USER\_API\_SKCIPHER kernel option).<br>If this kernel interface is not available, code fallbacks to old temporary keyslot device creation (where root privilege is required).</li></ul>

<blockquote>Note that activation, deactivation, resize and suspend operations still need root privilege (limitation of kernel device-mapper backend).

  * Fix internal PBKDF2 key derivation function implementation for alternative crypto backends (kernel, NSS)<br>which do not support PBKDF2 directly and have issues with longer HMAC keys.</blockquote>

> This fixes the problem for long keyfiles where either calculation is too slow (because of internal rehashing in every iteration) or there is a limit (kernel backend seems to not support HMAC key longer than 20480 bytes).

> _(Note that for recent version of gcrypt, nettle or openssl the internal PBKDF2 code is not compiled in and crypto library internal functions are used instead.)_

  * Support for Python3 for simple Python binding.

> Python >= 2.6 is now required. You can set Python compiled version by setting --with-python\_version configure option (together with --enable-python).

  * Use internal PBKDF2 in Nettle library for Nettle crypto backend.

> Cryptsetup compilation requires Nettle >= 2.6 (if using Nettle crypto backend).

  * Allow simple status of crypt device without providing metadata header.

> The command "cryptsetup status" will print basic info, even if you do not provide detached header argument.

  * Allow to specify ECB mode in cryptsetup benchmark.

  * Add some LUKS images for regression testing.

> Note that if image with Whirlpool fails, the most probable cause is that you have old gcrypt library with flawed whirlpool hash.<br>Read FAQ section 8.3 for more info.</li></ul>

<b>Cryptsetup API NOTE</b><br>The direct terminal handling for passphrase entry will be removed from libcryptsetup in next major version (application should handle it itself).

It means that you have to always either provide password in buffer or set your own password callback function through crypt\_set\_password\_callback().<br>See API documentation (or libcryptsetup.h) for more info.<br>
<br>
<br>
<h4>Release notes for <a href='Cryptsetup160.md'>cryptsetup 1.6.0</a>.</h4>
<h4>Release notes for <a href='Cryptsetup161.md'>cryptsetup 1.6.1</a>.</h4>
<h4>Release notes for <a href='Cryptsetup162.md'>cryptsetup 1.6.2</a>.</h4>
<h4>Release notes for <a href='Cryptsetup163.md'>cryptsetup 1.6.3</a>.</h4>
<h4>Release notes for <a href='Cryptsetup164.md'>cryptsetup 1.6.4</a>.</h4>
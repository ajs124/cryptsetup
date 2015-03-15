![http://wiki.cryptsetup.googlecode.com/git/luks-logo.png](http://wiki.cryptsetup.googlecode.com/git/luks-logo.png)
# What the ..? #

**Cryptsetup** is utility used to conveniently setup disk encryption based on [dm-crypt](DMCrypt.md) kernel module.

These include **plain** **dm-crypt** volumes, **LUKS** volumes, **loop-AES** and **TrueCrypt** compatible format.

Project also includes **veritysetup** utility used to conveniently setup [dm-verity](DMVerity.md) block integrity checking kernel module.

# LUKS Design #

**LUKS** is the standard for Linux hard disk encryption. By providing a standard on-disk-format, it does not only facilitate compatibility among distributions, but also provides secure management of multiple user passwords. In contrast to existing solution, LUKS stores all setup necessary setup information in the partition header, enabling the user to transport or migrate his data seamlessly.

LUKS was designed according to TKS1, a template design developed in [TKS1](TKS1.md) for secure key setup. LUKS closely reassembles the structure recommended in the TKS1 paper, but also adds meta data for cipher setup management and LUKS also supports for multiple keys/passphrases.

# Why LUKS? #

  * compatiblity via standardization,
  * secure against low entropy attacks,
  * support for multiple keys,
  * effective passphrase revocation,
  * free

# Source and API docs #

For development version code, please refer to [Source](https://code.google.com/p/cryptsetup/source/checkout) page, mirror on [kernel.org](https://git.kernel.org/cgit/utils/cryptsetup/cryptsetup.git/) or [Github](https://github.com/mbroz/cryptsetup).

For libcryptsetup documentation see [libcryptsetup API](http://wiki.cryptsetup.googlecode.com/git/API/index.html) page.

# Released packages downloads #

For the latest released version and older releases see [Downloads](Downloads.md) page.

# Help! #

For cryptsetup and LUKS related questions, please use the dm-crypt mailing list, [dm-crypt@saout.de](mailto:dm-crypt@saout.de).<br>If you want to subscribe just send an empty mail to <a href='mailto:dm-crypt-subscribe@saout.de'>dm-crypt-subscribe@saout.de</a>.<br>You can also browse <a href='http://www.saout.de/pipermail/dm-crypt/'>list archive</a> or read it through <a href='http://news.gmane.org/gmane.linux.kernel.device-mapper.dm-crypt'>web interface</a>.
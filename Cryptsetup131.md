### cryptsetup 1.3.1 ###
Released on 2011-05-24.

The stable cryptsetup 1.3.1 release is available at

> http://code.google.com/p/cryptsetup/

> Download [cryptsetup-1.3.1.tar.bz2](http://cryptsetup.googlecode.com/files/cryptsetup-1.3.1.tar.bz2)

Feedback and bug reports are welcomed. You can use  [New issue](http://code.google.com/p/cryptsetup/issues/entry) report page.

## Cryptsetup 1.3.1 Release Notes ##

### Changes since version 1.3.0 ###
  * Fix keyfile=- processing in create command (regression in 1.3.0).
  * Simplify device path status check (use /sys and do not scan /dev).
  * Do not ignore device size argument for create command (regression in 1.2.0).
  * Fix error paths in blockwise code and lseek\_write call.
  * Add optional Nettle crypto backend support.

For changes in previous version please see [Cryptsetup 1.3.0 Release Notes](http://code.google.com/p/cryptsetup/wiki/Cryptsetup130).
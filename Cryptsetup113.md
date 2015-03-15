### cryptsetup 1.1.3 ###
Released on 2010-07-03.

The stable cryptsetup 1.1.3 release is available at

> http://code.google.com/p/cryptsetup/

> Download [cryptsetup-1.1.3.tar.bz2](http://cryptsetup.googlecode.com/files/cryptsetup-1.1.3.tar.bz2)

Feedback and bug reports are welcomed. You can use  [New issue](http://code.google.com/p/cryptsetup/issues/entry) report page.

## Cryptsetup 1.1.3 Release Notes ##

### changes since version 1.1.2 ###

  * Fix device alignment ioctl calls parameters. (Device alignment code was not working properly on some architectures like ppc64.)

  * Fix ` activate_by_* ` API calls to handle NULL device name as documented. (To enable check of passphrase/keyfile using libcryptsetup without activating the device.)

  * Fix udev support for old libdevmapper with not compatible definition.

For changes in previous versions please see

[Cryptsetup 1.1.2 Release Notes](http://code.google.com/p/cryptsetup/wiki/Cryptsetup112)

[Cryptsetup 1.1.1 Release Notes](http://code.google.com/p/cryptsetup/wiki/Cryptsetup111)

[Cryptsetup 1.1.0 Release Notes](http://code.google.com/p/cryptsetup/wiki/Cryptsetup110)
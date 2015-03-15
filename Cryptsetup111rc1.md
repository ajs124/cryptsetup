### cryptsetup 1.1.1-rc1,2 ###
Released on 2010-05-03.

The first cryptsetup 1.1.1-rc1 release candidate is available at

> http://code.google.com/p/cryptsetup/

> Download [cryptsetup-1.1.1-rc2.tar.bz2](http://cryptsetup.googlecode.com/files/cryptsetup-1.1.1-rc2.tar.bz2)

Feedback and bug reports are welcomed.

### changes since version 1.1.1-rc1 ###

  * Fix automatic dm-crypt module loading.

### changes since version 1.1.0 ###

Important changes:

  * Detects and use device-mapper udev support if available.

> This should allow synchronisation with udev rules and avoid races with udev.

> If package maintainer want to use old, direct libdevmapper device node creation,
> use configure option --disable-udev.

  * Supports device topology detection for data alignment.

> If kernel provides device topology ioctl calls, the LUKS data area
> alignment is automatically set to optimal value.

> This means that stacked devices (like LUKS over MD/LVM)
> should use the most optimal data alignment.

> (You can still overwrite this calculation using --align-payload option.)

  * Prefers some device paths in status display.

> So status command will try to find top level device name, like /dev/sdb.)

  * Fix package config file to use proper package version.

Other changes:

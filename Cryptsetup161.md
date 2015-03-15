### cryptsetup 1.6.1 ###
Released on 2013-03-31.

The stable cryptsetup 1.6.1 release is available at

> http://code.google.com/p/cryptsetup/

> Download [cryptsetup-1.6.1.tar.bz2](http://cryptsetup.googlecode.com/files/cryptsetup-1.6.1.tar.bz2)

Feedback and bug reports are welcomed. You can use  [New issue](http://code.google.com/p/cryptsetup/issues/entry) report page.

## Cryptsetup 1.6.1 Release Notes ##

### Changes since version 1.6.0 ###

  * Fix loop-AES keyfile parsing.<br>Loop-AES keyfile should be text keyfile, reject keyfiles which are not properly terminated.</li></ul>

<ul><li>Fix passphrase pool overflow for too long TCRYPT passphrase.<br>(Maximal TCRYPT passphrase length is 64 characters.)</li></ul>

  * Return EPERM (translated to exit code 2) for too long TCRYPT passphrase.

  * Fix deactivation of device when failed underlying node disappeared.

  * Fix API deactivate call for TCRYPT format and NULL context parameter.

  * Improve keyslot checker example documentation.

  * Report error message if deactivation fails and device is still busy.

  * Make passphrase prompts more consistent (and remove "LUKS" form prompt).

  * Fix some missing headers (compilation failed with alternative libc).

  * Remove non-functional API UUID support for plain and loopaes devices.<br>(For not persistent activation UUID.)</li></ul>

<ul><li>Properly cleanup devices on interrupt in api-test.</li></ul>

<ul><li>Support all tests run if kernel is in FIPS mode.</li></ul>

<h4>Release notes for [cryptsetup 1.6.0](Cryptsetup160.md). ####
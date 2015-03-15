### Cryptsetup 1.6.2 ###
Released on 2013-08-04.

The stable cryptsetup 1.6.2 release is available at

> http://code.google.com/p/cryptsetup/

> Download [cryptsetup-1.6.2.tar.bz2](http://cryptsetup.googlecode.com/files/cryptsetup-1.6.2.tar.bz2)

Feedback and bug reports are welcomed. You can use  [New issue](http://code.google.com/p/cryptsetup/issues/entry) report page.

## Cryptsetup 1.6.2 Release Notes ##

### Changes since version 1.6.1 ###
  * Print error and fail if more device arguments are present for isLuks command.

  * Fix cipher specification string parsing (found by gcc -fsanitize=address option).

  * Try to map TCRYPT system encryption through partition<br>(allows to activate mapping when other partition on the same device is mounted).</li></ul>

<ul><li>Print a warning if system encryption is used and device is a partition<br>(TCRYPT system encryption uses whole device argument.)</li></ul>

  * Disallow explicit small payload offset for LUKS detached header.<br>LUKS detached header only allows data payload 0 (whole data device is used)<br>or explicit offset larger than header + keyslots size.</li></ul>

  * Fix boundary condition for verity device that caused failure for certain device sizes.

  * Various fixes to documentation, including update FAQ,<br>default modes and TCRYPT description.</li></ul>

<ul><li>Workaround for some recent changes in automake (serial-tests).</li></ul>

<h4>Release notes for [cryptsetup 1.6.0](Cryptsetup160.md). ####
#### Release notes for [cryptsetup 1.6.1](Cryptsetup161.md). ####
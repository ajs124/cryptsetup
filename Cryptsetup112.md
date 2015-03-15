### cryptsetup 1.1.2 ###
Released on 2010-05-30.

The stable cryptsetup 1.1.2 release is available at

> http://code.google.com/p/cryptsetup/

> Download [cryptsetup-1.1.2.tar.bz2](http://cryptsetup.googlecode.com/files/cryptsetup-1.1.2.tar.bz2)

Feedback and bug reports are welcomed. You can use  [New issue](http://code.google.com/p/cryptsetup/issues/entry) report page.

## Cryptsetup 1.1.2 Release Notes ##

This release fixes a regression (introduced in 1.1.1 version) in handling
key files containing new line characters (affects only files read from standard input).

Cryptsetup can accept passphrase on stdin (standard input). Handling of new line (\n) character is defined by input specification:
  * if keyfile is specified as "-" (using --key-file=- of by positional argument in luksFormat and luksAddKey, like ` cat file | cryptsetup --key-file=- <action> `), input is processed as normal binary file and no new line is interpreted.
  * if there is no key file specification (with default input from stdin pipe like ` echo passphrase | cryptsetup <action> `) input is processed as input from terminal, reading will stop after new line is detected.

Moreover, luksFormat now understands --key-file (in addition to positional key file argument).

### changes since version 1.1.1 ###

  * Fix luksFormat/luksOpen reading passphrase from stdin and "-" keyfile.
  * Support --key-file/-d option for luksFormat.
  * Fix description of --key-file and add --verbose and --debug options to man page.
  * Add verbose log level and move unlocking message there.
  * Remove device even if underlying device disappeared (remove, luksClose).
  * Fix (deprecated) reload device command to accept new device argument.

For changes in previous version please see [Cryptsetup 1.1.1 Release Notes](http://code.google.com/p/cryptsetup/wiki/Cryptsetup111).
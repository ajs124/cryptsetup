Changes:

  * Fail early for unaccessible devices
  * Require O\_EXCL access for luksOpen, so users don't create two mappings for the same underlying block device (which would cause file system corruption when mounted twice).
  * Add -s option to luksAddKey so the user can specify the key slot for new key (Marc Merlin)
  * Clean temporary mappings when hitting Ctrl-C at the password prompt
  * Run udevsettle after device creation (Matthias Koenig)
  * Man page improvements (Jonas Meuer)
  * Swedish translation courtesy (Daniel Nylander)

Thanks to the following bug hunters:
Ludwig Nussel, Till Maas, Henrik Theiling, Jonas Meuer, Michal Hlavinka, Nikolay A. Fetisov

Download [cryptsetup-1.0.6.tar.bz2](http://cryptsetup.googlecode.com/files/cryptsetup-1.0.6.tar.bz2)
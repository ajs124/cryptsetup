### cryptsetup 1.4.1 ###
Released on 2011-11-09.

The stable cryptsetup 1.4.1 release is available at

> http://code.google.com/p/cryptsetup/

> Download [cryptsetup-1.4.1.tar.bz2](http://cryptsetup.googlecode.com/files/cryptsetup-1.4.1.tar.bz2)

Feedback and bug reports are welcomed. You can use  [New issue](http://code.google.com/p/cryptsetup/issues/entry) report page.

## Cryptsetup 1.4.1 Release Notes ##

### Changes since version 1.4.0 ###
  * Merge experimental Python cryptsetup (pycryptsetup) binding.

> This option is disabled by default, you can enable build of Python binding<br>with --enable-python configure switch.</li></ul>

<blockquote>Note that binding currently covers only partial libcryptsetup functions,<br> mainly LUKS device handling needed for Anaconda installer.<br>Until now provided separately as python-cryptsetup.<br>Thanks to Martin Sivak for the code.</blockquote>

<blockquote>See python subdirectory for more info.</blockquote>

<blockquote>Python binding code is experimental for now, no stable API guarantee.</blockquote>

<ul><li>Fix crypt_get_volume_key_size() for plain device<br>(cryptsetup status reported zero key size for plain crypt devices).</li></ul>

<ul><li>Fix typo in set_iteration_time API call (old name remains for compatibility reasons).</li></ul>

<ul><li>Fix FSF address in license and add LGPL license text.</li></ul>

For changes in previous version please see <a href='http://code.google.com/p/cryptsetup/wiki/Cryptsetup140'>Cryptsetup 1.4.0 Release Notes</a>.
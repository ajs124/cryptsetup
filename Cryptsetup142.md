### cryptsetup 1.4.2 ###
Released on 2012-04-12.

The stable cryptsetup 1.4.2 release is available at

> http://code.google.com/p/cryptsetup/

> Download [cryptsetup-1.4.2.tar.bz2](http://cryptsetup.googlecode.com/files/cryptsetup-1.4.2.tar.bz2)

Feedback and bug reports are welcomed. You can use  [New issue](http://code.google.com/p/cryptsetup/issues/entry) report page.

## Cryptsetup 1.4.2 Release Notes ##

### Changes since version 1.4.1 ###

  * Add --keyfile-offset and --new-keyfile-offset parameters to API and CLI.<br>These options can be used to skip start of keyfile or device used as keyfile.</li></ul>

<ul><li>Add repair command and crypt_repair() for known LUKS metadata problems repair.</li></ul>

<blockquote>Some well-known LUKS metadata corruptions are easy to repair,<br>this command should provide a way to fix these problems.</blockquote>

> Always create binary backup of header device before running repair, <br>(only 4kB - visible header) for example by using dd:</li></ul>

<blockquote>`dd if=/dev/<device> of=repair_bck.img bs=1k count=4`

> Then you can try to run repair:
> `cryptsetup repair <device>`

> Note, not all problems are possible to repair and if keyslot or some header<br>parameters are overwritten, device is lost permanently.</blockquote>

  * Fix header check to support old (cryptsetup 1.0.0) header alignment.<br>(Regression in 1.4.0)</li></ul>

<ul><li>Allow to specify --align-payload only for luksFormat.</li></ul>

<ul><li>Add --master-key-file option to luksOpen (open using volume key).</li></ul>

<ul><li>Support UUID=<b>LUKS_UUID</b> format for device specification.<br>You can open device by UUID (only shortcut to /dev/disk/by-uuid/ symlinks).</li></ul>

  * Support password verification with quiet flag if possible. (1.2.0)<br>Password verification can be still possible if input is terminal.</li></ul>

<ul><li>Fix retry if entered passphrases (with verify option) do not match.<br>(It should retry if requested, not fail.)</li></ul>

  * Fix use of empty keyfile.

  * Fix error message for luksClose and detached LUKS header.

  * Allow --header for status command to get full info with detached header.
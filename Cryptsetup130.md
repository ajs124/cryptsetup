### cryptsetup 1.3.0 ###
Released on 2011-04-05.

The stable cryptsetup 1.3.0 release is available at

> http://code.google.com/p/cryptsetup/

> Download [cryptsetup-1.3.0.tar.bz2](http://cryptsetup.googlecode.com/files/cryptsetup-1.3.0.tar.bz2)

Feedback and bug reports are welcomed. You can use  [New issue](http://code.google.com/p/cryptsetup/issues/entry) report page.

## Cryptsetup 1.3.0 Release Notes ##

### Changes since version 1.2.0 ###

## Important changes ##
  * Several userspace crypto backends support<br>cryptsetup now supports generic crypto backend interface which allows<br>compile package with various crypto libraries, these are already implemented:<br>
<ul><li><b>gcrypt</b> (default, used in previous versions)<br>
</li><li><b>OpenSSL</b>
</li><li><b>NSS</b> (because of missing ripemd160 it cannot provide full backward compatibility)<br>
</li><li><b>kernel</b> <b>userspace</b> <b>API</b> (provided by kernel 2.6.38 and above)<br>(Note that kernel userspace backend is very slow for this type of operation.<br>But it can be usefull for embedded systems, because you can avoid userspace<br>crypto library completely.)</li></ul></li></ul>

<blockquote>Backend is selected during configure time, using --with-crypto_backend option.</blockquote>

<blockquote><code>configure --with-crypto_backend=BACKEND (gcrypt/openssl/nss/kernel) [gcrypt]</code></blockquote>

<blockquote>Note that performance checked (iterations) in LUKS header will cause that<br>real iteration time will differ with different backends.<br>(There are huge differences in speed between libraries.)</blockquote>

<ul><li>Cryptsetup now automatically allocates <b>loopback</b> <b>device</b> (/dev/loop)<br>if device argument is file and not plain device.</li></ul>

<blockquote>This require Linux kernel 2.6.25 and above (which implements loop autoclear flag).</blockquote>

<blockquote>You can see backing file in cryptsetup status output if underlying device is loopback.</blockquote>

<ul><li>Introduce maximum default keyfile size, add configure option, visible in --help.</li></ul>

<blockquote>Cryptsetup now fails if read from keyfile exceeds internal limit.<br>You can always specify keyfile size (overrides limit) by using --keyfile-size option.</blockquote>

<ul><li>Adds <b>luksChangeKey</b> command</li></ul>

<blockquote><code>cryptestup luksChangeKey --key-file &lt;old keyfile&gt; &lt;new keyfile&gt; [--key-slot X]</code></blockquote>

<blockquote><code>cryptestup luksChangeKey [--key-slot X]</code>  (for passphrase change)</blockquote>

<blockquote>This command allows passphrase/keyfile change in one step.<br>If no key slot is specified (and there is still free key slot on device)<br>new slot is allocated before the old is purged.</blockquote>

<blockquote>If --key-slot option is specified (or there is no free slot) command will overwrite existing slot.<br><b>WARNING:</b> Be sure you have another slot active or header backup when using explicit<br>key slot (so you can unlock the device even after possible media failure).</blockquote>

<ul><li>Adds compatible support for <b>loop-AES</b> <b>encryption</b> <b>type</b> in <b>loopaesOpen</b> command.</li></ul>

<blockquote>Linux dm-crypt in 2.6.38 and above supports loop-AES compatible mapping<br>(including multi-key and special CBC mode, all three modes are supported).</blockquote>

<blockquote>If you have raw loop-AES keyfile (text file with uuencoded per-line keys),<br>you can access loop-AES volume using<br>
<blockquote><code>cryptsetup loopaesOpen &lt;device&gt; &lt;name&gt; [--key-size 128] --key-file &lt;key-file&gt;</code></blockquote></blockquote>

<blockquote>If you are using GPG encrypted keyfile<br>
<blockquote><code>gpg --decrypt &lt;key-file&gt; | cryptsetup loopaesOpen --key-file=- &lt;device&gt; &lt;name&gt;</code></blockquote></blockquote>

<blockquote>Do not forget to specify key size. Version and hash is automatically detected according to number of lines in key file.<br> For special configuration you can override IV sector offset using --skip option, device offset with --offset and hash algorithm using --hash, see man page for details.</blockquote>

<blockquote><i>Please note that loopAES dm-crypt mode is provided for compatibility reasons</i><br>(so you do not need to patch kernel and util-linux to map existing volumes)<br>but is is not, and never will be, optimized for speed.<br>It is experimental feature for now.<i></blockquote></i>

<ul><li>Require the whole key read from keyfile in create command (regression in 1.2.0).</li></ul>

<ul><li><b>WARNING:</b> This is the last cryptsetup release which supports library with  old API<br>(using struct crypt_options).<br>These calls are deprecated since 1.1.0 and AFAIK no application is using it in recent distros.<br>Removing compatible code will allow new features to be implemented easily.</li></ul>

<h2>Other changes</h2>
<ul><li>Lock memory also in luksDump command.<br>
</li><li>Fix return code when passphrase is read from pipe.<br>
</li><li>Increase libcryptsetup version (loopAES change), still fully backward compatible.<br>
</li><li>Fixes static build (--disable-static-cryptsetup now works properly).<br>
</li><li>Supports secure data flag for device-mapper iotcl (will be in 2.6.39,<br> forcing kernel to wipe all ioctl buffers with possible key data).<br>To enable this flag you need new device-mapper library, in LVM2 2.02.84.<br>
</li><li>Add copyright texts into some files and adds GPL exception allowing<br>to distribute resulting binaries linked with OpenSSL.<br>
</li><li>Update FAQ.<br>
</li><li>Fix message when locking memory fails.<br>
</li><li>Fix luksAddKey return code if master key is used.<br>
</li><li>Update some text files in distributions.<br>
</li><li>Add docs directory with Release Notes archive.<br>
</li><li>Do not hardcode loopback device name in tests, use internal loopback library.
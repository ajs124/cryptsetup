### cryptsetup 1.6.0 ###
Released on 2013-01-14.

The stable cryptsetup 1.6.0 release is available at

> http://code.google.com/p/cryptsetup/

> Download [cryptsetup-1.6.0.tar.bz2](http://cryptsetup.googlecode.com/files/cryptsetup-1.6.0.tar.bz2)

Feedback and bug reports are welcomed. You can use  [New issue](http://code.google.com/p/cryptsetup/issues/entry) report page.

## Cryptsetup 1.6.0-rc1 Release Notes ##

### Changes (since version 1.6.0-rc1) ###

  * Change LUKS default cipher to to use XTS encryption mode, aes-xts-plain64 (i.e. using AES128-XTS).
    * XTS mode becomes standard in hard disk encryption.
    * You can still use any old mode:
      1. compile cryptsetup with old default:
```
configure --with-luks1-cipher=aes --with-luks1-mode=cbc-essiv:sha256 --with-luks1-keybits=256
```
      1. format LUKS device with old default:
```
cryptsetup luksFormat -c aes-cbc-essiv:sha256 -s 256 <device>
```

  * Skip tests and fix error messages if running on old systems (or with old kernel).

  * Rename configure.in to configure.ac and fix issues with new automake and pkgconfig<br>and --disable-kernel_crypto option to allow compilation with old kernel headers.</li></ul>

<ul><li>Allow repair of 512 bits key header.</li></ul>

<ul><li>Fix status of device if path argument is used and fix double path prefix for non-existent device path.</li></ul>

<h3>Important changes (since version 1.5.1) ###

  * Cryptsetup and libcryptsetup is now released under GPLv2+ (GPL version 2 or any later).<br>Some internal code handling files (loopaes, verity, tcrypt and crypto backend wrapper) are LGPLv2+.<br>Previously code was GPL version 2 only.</li></ul>

  * Introducing new unified command **open** and **close**.
> > Example _(type defaults to luks)_:
```
    cryptsetup open --type plain|luks|loopaes|tcrypt <device> <name>
```


> with backward-compatible aliases _plainOpen_, _luksOpen_, _loopaesOpen_, _tcryptOpen_.<br>Basically <code>open --type xyz</code> has alias <code>xyzOpen</code>.</li></ul>

<blockquote>The **create** command (plain device create) is DEPRECATED but will be still supported.<br>(This command is confusing because of switched arguments order.)</blockquote>

> The **close** command is generic command to remove mapping and have backward compatible aliases (remove, luksClose, ...) which behaves exactly the same.

> While all old syntax is still supported, I strongly suggest to use new command syntax which is common for all device types (and possible new formats added in future).


  * cryptsetup now supports directly **TCRYPT (TrueCrypt and compatible tc-play) on-disk format**<br><i>(Code is independent implementation not related to original project).</i></li></ul>

<blockquote>Only dump (**tcryptDump** command) and activation (**open --type tcrypt** or **tcryptOpen**) of TCRYPT device are supported.<br>No header changes are supported.</blockquote>

> It is intended to easily access containers shared with other operating systems without need to install 3rd party software.<br>For native Linux installations LUKS is the preferred format.<br><b>WARNING: TCRYPT  extension  requires kernel userspace crypto API to be available</b><br>(kernel af_alg and algif_skcipher modules, introduced in Linux kernel 2.6.38).</li></ul>

<blockquote>Because  TCRYPT  header is encrypted, you have to always provide valid passphrase and keyfiles.<br>Keyfiles are handled exactly the same as in original format (basically, first 1MB of every keyfile is mixed using CRC32 into pool).</blockquote>

<blockquote>Cryptsetup should recognize all TCRYPT header variants ever released, except  legacy  cipher chains  using LRW encryption mode with 64 bits encryption block (namely Blowfish in LRW mode is not recognized, this is  limitation  of  kernel crypto API).</blockquote>

<blockquote>Device activation is supported only for LRW/XTS modes (again, limitation of kernel dmcrypt which do not implements TCRYPT extensions to CBC mode).<br>(So old containers cannot be activated, but you can use libcryptsetup for lost password search, example of such code is included in misc directory.)</blockquote>

<blockquote>Hidden header are supported using <b>--tcrypt-hidden</b> option, system encryption using <b>--tcrypt-system</b> option.</blockquote>

<blockquote>For detailed description see man page.</blockquote>

<blockquote><i>EXAMPLE:</i>
<ul><li>Dump device parameters of container in file<br>
<pre><code>     # cryptsetup tcryptDump tst<br>
       Enter passphrase: <br>
<br>
     TCRYPT header information for tst<br>
     Version:        5<br>
     Driver req.:    7<br>
     Sector size:    512<br>
     MK offset:      131072<br>
     PBKDF2 hash:    sha512<br>
     Cipher chain:   serpent-twofish-aes<br>
     Cipher mode:    xts-plain64<br>
     MK bits:        1536<br>
</code></pre>
</li></ul><blockquote>You can also dump master key using <b>--dump-master-key</b>.<br>Dump does not require superuser privilege.</blockquote></blockquote>

<ul><li>Activation of this container<br>
<pre><code>     # cryptsetup tcryptOpen tst tcrypt_dev       <br>
       Enter passphrase: <br>
      (Chain of dmcrypt devices is activated as /dev/mapper/tcrypt_dev.)<br>
</code></pre>
</li><li>See status of active TCRYPT device<br>
<pre><code>     # cryptsetup status tcrypt_dev<br>
<br>
     /dev/mapper/tcrypt_dev is active.<br>
     type:    TCRYPT<br>
     cipher:  serpent-twofish-aes-xts-plain64<br>
     keysize: 1536 bits<br>
     device:  /dev/loop0<br>
     loop:    /tmp/tst<br>
     offset:  256 sectors<br>
     size:    65024 sectors<br>
     skipped: 256 sectors<br>
     mode:    read/write<br>
</code></pre>
</li></ul><ul><li>And plaintext filesystem now ready to mount<br>
<pre><code>    # blkid /dev/mapper/tcrypt_dev<br>
    /dev/mapper/tcrypt_dev: SEC_TYPE="msdos" UUID="9F33-2954" TYPE="vfat" <br>
</code></pre></li></ul>

<ul><li>Add (optional) support for <b>lipwquality</b> for new LUKS passwords.</li></ul>

<blockquote>If password is entered through terminal (no keyfile specified) and cryptsetup is compiled with --enable-pwquality, default system pwquality settings are used to check password quality.</blockquote>

<blockquote>You can always override this check by using new <b>--force-password</b> option.</blockquote>

<blockquote>For more info about pwquality project see <a href='http://libpwquality.fedorahosted.org/'>http://libpwquality.fedorahosted.org/</a></blockquote>


<ul><li>Proper handle interrupt signals (ctrl+c and TERM signal) in tools</li></ul>

<blockquote>Code should now handle interrupt properly, release and explicitly wipe in-memory key materials on interrupt.<br>(Direct users of libcryptsetup should always call crypt_free() when code is interrupted to wipe all resources.<br>There is no signal handling in library, it is up to the tool using it.)</blockquote>


<ul><li>Add new benchmark command</li></ul>

<blockquote>The <b>benchmark</b> command now tries to benchmark PBKDF2 and some block cipher variants.<br>You can specify you own parameters (<b>--cipher</b>/<b>--key-size</b> for block ciphers, <b>--hash</b> for PBKDF2).</blockquote>

<blockquote>See man page for detailed description.<br><b>WARNING: benchmark requires kernel userspace crypto API to be available</b><br>(kernel af_alg and algif_skcipher modules, introduced in  Linux  kernel 2.6.38).</blockquote>

<blockquote><i>EXAMPLE:</i>
<pre><code>     # cryptsetup benchmark<br>
     # Tests are approximate using memory only (no storage IO).<br>
     PBKDF2-sha1       111077 iterations per second<br>
     PBKDF2-sha256      53718 iterations per second<br>
     PBKDF2-sha512      18832 iterations per second<br>
     PBKDF2-ripemd160   89775 iterations per second<br>
     PBKDF2-whirlpool   23918 iterations per second<br>
     #  Algorithm | Key | Encryption | Decryption<br>
          aes-cbc   128b  212.0 MiB/s  428.0 MiB/s<br>
      serpent-cbc   128b   23.1 MiB/s   66.0 MiB/s<br>
      twofish-cbc   128b   46.1 MiB/s   50.5 MiB/s<br>
          aes-cbc   256b  163.0 MiB/s  350.0 MiB/s<br>
      serpent-cbc   256b   23.1 MiB/s   66.0 MiB/s<br>
      twofish-cbc   256b   47.0 MiB/s   50.0 MiB/s<br>
          aes-xts   256b  190.0 MiB/s  190.0 MiB/s<br>
      serpent-xts   256b   58.4 MiB/s   58.0 MiB/s<br>
      twofish-xts   256b   49.0 MiB/s   49.5 MiB/s<br>
          aes-xts   512b  175.0 MiB/s  175.0 MiB/s<br>
      serpent-xts   512b   59.0 MiB/s   58.0 MiB/s<br>
      twofish-xts   512b   48.5 MiB/s   49.5 MiB/s<br>
</code></pre>
<blockquote>Or you can specify cipher yourself:<br>
<pre><code>     # cryptsetup benchmark --cipher cast5-cbc-essiv:sha256 -s 128<br>
     # Tests are approximate using memory only (no storage IO).<br>
     #  Algorithm | Key | Encryption | Decryption<br>
        cast5-cbc   128b   32.4 MiB/s   35.0 MiB/s<br>
</code></pre>
These tests do not use dmcrypt, only crypto API.<br>You have to benchmark the whole device stack and you can get completely different results.<br>But is is usable for basic comparison. (Note for example AES-NI decryption optimization effect in example above.)</blockquote></blockquote>

<h3>Features</h3>

<ul><li>Do not maintain ChangeLog file anymore, see git log for detailed changes, e.g. here <a href='http://code.google.com/p/cryptsetup/source/list'>http://code.google.com/p/cryptsetup/source/list</a></li></ul>

<ul><li>Move change key into library, add crypt_keyslot_change_by_passphrase().<br>This change is useful mainly in FIPS mode, where we cannot extract volume key directly from libcryptsetup.</li></ul>

<ul><li>Add verbose messages during reencryption.</li></ul>

<ul><li>Default LUKS PBKDF2 iteration time is now configurable.</li></ul>

<ul><li>Add simple cipher benchmarking API.</li></ul>

<ul><li>Add kernel skcipher backend.</li></ul>

<ul><li>Add CRC32 implementation (for TCRYPT).</li></ul>

<ul><li>Move PBKDF2 into crypto backend wrapper.<br>This allows use it in other formats, use library implementations and also possible use of different KDF function in future.</li></ul>

<ul><li>New PBKDF2 bechmark using getrusage().</li></ul>

<h3>Fixes</h3>

<ul><li>Avoid O_DIRECT open if underlying storage doesn't support it.</li></ul>

<ul><li>Fix some non-translated messages.</li></ul>

<ul><li>Fix regression in header backup (1.5.1) with container in file.</li></ul>

<ul><li>Fix blockwise read/write for end writes near end of device.<br>(was not used in previous versions)</li></ul>

<ul><li>Ignore setpriority failure.</li></ul>

<ul><li>Code changes to fix/ignore problems found by Coverity static analysis, including<br>Get page size should never fail.<br>Fix time of check/use (TOCTOU test) in tools<br>Fix time of check/use in loop/wipe utils.<br>Fix time of check/use in device utils.</li></ul>

<ul><li>Disallow header restore if context is non-LUKS device.
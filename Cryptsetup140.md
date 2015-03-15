### cryptsetup 1.4.0 ###
Released on 2011-10-26.

The stable cryptsetup 1.4.0 release is available at

> http://code.google.com/p/cryptsetup/

> Download [cryptsetup-1.4.0.tar.bz2](http://cryptsetup.googlecode.com/files/cryptsetup-1.4.0.tar.bz2)

Feedback and bug reports are welcomed. You can use  [New issue](http://code.google.com/p/cryptsetup/issues/entry) report page.

## Cryptsetup 1.4.0 Release Notes ##

### Changes since version 1.4.0 RC1 ###

  * Fix verbose messages in isLuks command.
  * Update on-disk format specification document.

### Changes since version 1.3.1 ###

## Important changes ##

**WARNING**: This release removes old deprecated API from libcryptsetup (all functions using struct crypt\_options).<br>
This require libcrypsetup version change and rebuild of applications using cryptsetup library.<br>All new API symbols are backward compatible.<br>
<br>
<ul><li>If device is not rotational disk, cryptsetup no longer tries to wipe keyslot with Gutmann algorithm<br>for magnetic media erase but simply rewrites area once by random data.</li></ul>

<ul><li>The on-disk LUKS header can now be detached (e.g. placed on separate device or in file)<br>using new --header option.</li></ul>

<blockquote>This option is only relevant for LUKS devices and can be used in luksFormat, luksOpen, luksSuspend,<br>luksResume and resize commands.</blockquote>

<blockquote>If used with luksFormat the --align-payload option is taken as absolute sector alignment on ciphertext<br>device and can be zero.<br><br><b>Example:</b></blockquote>

<blockquote>Create LUKS device with ciphertext device on /dev/sdb and header on device /dev/sdc.<br>Use all space on /dev/sdb (no reserved area for header).</blockquote>

<blockquote><code>cryptsetup luksFormat /dev/sdb --header /dev/sdc --align-payload 0</code></blockquote>

<blockquote>Activate such device:</blockquote>

<blockquote><code>cryptsetup luksOpen /dev/sdb --header /dev/sdc test_disk</code></blockquote>

<blockquote>You can use file for LUKS header (loop device will be used while manipulating with such detached header),<br>just you have to create large enough file in advance.</blockquote>

<blockquote><code>dd if=/dev/zero of=/mnt/luks_header bs=1M count=4</code><br>
<code>cryptsetup luksFormat /dev/sdb --header /mnt/luks_header --align-payload 0</code></blockquote>

<blockquote>Activation is the same as above.</blockquote>

<blockquote><code>cryptsetup luksOpen /dev/sdb --header /mnt/luks_header test_disk</code></blockquote>

<blockquote>All keyslot operations need to be run on <i>header</i> not on ciphertext device,<br>an example:</blockquote>

<blockquote><code>cryptsetup luksAddKey /mnt/luks_header</code></blockquote>

<blockquote>If you do not use --align-payload 0, you can later restore LUKS header on device itself<br>(and use it as normal LUKS device without detached header).<br><br><b>WARNING</b>: There is no possible check that specified ciphertext device matches detached on-disk header.<br>Use with care, it can destroy your data in case of a mistake.<br><br><b>WARNING</b>: Storing LUKS header in a file means that anti-forensic splitter cannot properly work<br>(there is filesystem allocation layer between header and disk).</blockquote>

<ul><li>Support --allow-discards option to allow discards/TRIM requests.</li></ul>

<blockquote>Since kernel 3.1, dm-crypt devices optionally (not by default) support block discards (TRIM) commands.</blockquote>

<blockquote>If you want to enable this operation, you have to enable it manually on every activation using --allow-discards</blockquote>

<blockquote><code>cryptsetup luksOpen --allow-discards /dev/sdb test_disk</code> <br><br><b>WARNING</b>: There are several security consequences, please read at least<br><a href='http://asalor.blogspot.com/2011/08/trim-dm-crypt-problems.html'>http://asalor.blogspot.com/2011/08/trim-dm-crypt-problems.html</a><br>before you enable it.</blockquote>

<ul><li>Add --shared option for creating non-overlapping crypt segments.</li></ul>

<blockquote>The --shared options checks that mapped segments are not overlapping and allows non-exclusive access to underlying device.</blockquote>

<blockquote>Only plain crypt devices can be used in this mode.</blockquote>

<blockquote>Example - map 64M of device disk and following 32 M area as another disk.</blockquote>

<blockquote><code>cryptsetup create outer_disk /dev/sdb --offset 0 --size 65536</code><br>
<code>cryptsetup create inner_disk /dev/sdb --offset 65536 --size 32768 --shared</code></blockquote>

<blockquote>(It can be used to simulate trivial hidden disk concepts.)</blockquote>

<ul><li>libcryptsetup API changes:<br>
<ul><li>Added options to suport detached metadata device<br>crypt_init_by_name_and_header()<br>crypt_set_data_device()<br>
</li><li>Add crypt_last_error() API call.<br>
</li><li>Fix plain crypt format parameters to include size option.<br>
</li><li>Add crypt_get_iv_offset() function.</li></ul></li></ul>

<ul><li>Remove old API functions (all functions using crypt_options).</li></ul>

<ul><li>Support key-slot option for luksOpen (use only explicit keyslot).<br>You can now specify key slot in luksOpen<br>and limit checking only to specified slot.</li></ul>

<ul><li>Support retries and timeout parameters for luksSuspend.<br>(The same way as in luksOpen.)</li></ul>

<ul><li>Add doxygen-like documentation (it will be available on project page later).<br>(To generate it manually run doxygen in docs directory.)</li></ul>

<h2>Other changes</h2>
<ul><li>Fix crypt_load to properly check device size.<br>
</li><li>Do not allow context format of already formatted device.<br>
</li><li>Do not allow key retrieval while suspended (key could be wiped).<br>
</li><li>Do not allow suspend for non-LUKS devices.<br>
</li><li>Fix luksKillSLot exit code if slot is inactive or invalid.<br>
</li><li>Fix exit code if passphrases do not match in luksAddKey.<br>
</li><li>Fix return code for status command when device doesn't exists.<br>
</li><li>Support Nettle 2.4 crypto backend (supports ripemd160).<br>
</li><li>Add LUKS on-disk format description into package.<br>
</li><li>Enhance check of device size before writing LUKS header.<br>
</li><li>Add more paranoid checks for LUKS header and keyslot attributes.<br>
</li><li>Use new /dev/loop-control (kernel 3.1) if possible.<br>
</li><li>Remove hash/hmac restart from crypto backend and make it part of hash/hmac final.<br>
</li><li>Improve check for invalid offset and size values.<br>
</li><li>Revert default initialisation of volume key in crypt_init_by_name().<br>
</li><li>Add more regression tests.<br>
</li><li>Add some libcryptsetup example files (see docs/examples).
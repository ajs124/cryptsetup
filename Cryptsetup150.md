### cryptsetup 1.5.0 ###
Released on 2012-07-10.

The stable cryptsetup 1.5.0 release is available at

> http://code.google.com/p/cryptsetup/

> Download [cryptsetup-1.5.0.tar.bz2](http://cryptsetup.googlecode.com/files/cryptsetup-1.5.0.tar.bz2)

Feedback and bug reports are welcomed. You can use  [New issue](http://code.google.com/p/cryptsetup/issues/entry) report page.

## Cryptsetup 1.5.0 Release Notes ##

This release covers mainly inclusion of
  * new **veritysetup** tool (and related libcryptsetup extensions for dm-verity).
  * new experimental **cryptsetup-reencrypt** tool (LUKS offline reencryption).

### Changes since version 1.5.0-rc2 ###
  * Add --device-size option for reencryption tool.
  * Switch to use unit suffix for --reduce-device-size option.
  * Remove open device debugging feature (no longer needed).
  * Fix library name for FIPS check.
  * Add example of using reencryption inside dracut (see misc/dracut).

### Changes since version 1.5.0-rc1 ###

Introduce **cryptsetup-reencrypt** - experimental offline LUKS reencryption tool.

**cryptsetup-reencrypt tool is EXPERIMENTAL**<br>
ALWAYS BE SURE YOU HAVE RELIABLE BACKUP BEFORE USING THIS TOOL<br>
<br>
This tool tries to simplify situation when you need to re-encrypt the whole LUKS device in situ (without need to move data elsewhere).<br>
<br>
This can happen for example when you want to change volume (master) key, encryption algorithm, or other encryption parameter.<br>
<br>
Cryptsetup-reencrypt can even optionally shift data on device (reducing data device size - you need some free space at the end of device).<br>
<br>
In general, cryptsetup-reencrypt can be used to<br>
<ul><li>re-generate volume key<br>
</li><li>change arbitrary encryption parameters<br>
</li><li>add encryption to not yet encrypted drive</li></ul>

Side effect of reencryption is that final device will contain only ciphertext (for all sectors) so even if device was not properly wiped by random data, after reencryption you cannot distinguish which sectors are used. (Reecryption is done always for the whole device.)<br>
<br>
There are for sure bugs, please <b>TEST IT IN TEST ENVIRONMENT before use for your data</b>.<br>
<br>
This tool is not resistant to HW and kernel failures - hw crash will cause serious data corruption.<br>
<br>
You can enable compilation of this tool with <code>--enable-cryptsetup-reencrypt</code> configure option (it is switched off by default).<br>
(Tool requires libcryptsetup 1.4.3 and later.)<br>
<br>
You have to provide all keyslot passphrases or use <code>--keyslot-option</code> (then all other keyslots will be disabled).<br>
<br>
<h4>EXAMPLES (from man page)</h4>

Reencrypt /dev/sdb1 (change volume key)<br>
<pre><code>   # cryptsetup-reencrypt /dev/sdb1<br>
</code></pre>

Reencrypt and also change cipher and cipher mode<br>
<pre><code>  # cryptsetup-reencrypt /dev/sdb1 -c aes-xts-plain64<br>
</code></pre>

<blockquote>Note: if you are changing key size, there must be enough space for keyslots in header or you have to use <code>--reduce-device-size</code> size and educe fs in advance.</blockquote>

Add LUKS encryption to not yet encrypted device.<br>
First, be sure you have space added to disk. Or, alternatively, shrink filesystem in advance.<br>
<br>
Here we need 4096 512-bytes sectors (enough for 2x128 bit key).<br>
<pre><code>  # fdisk -u /dev/sdb # move sdb1 partition end + 4096 sectors<br>
<br>
  # cryptsetup-reencrypt /dev/sdb1 --new --reduce-device-size 4096<br>
</code></pre>

There are some options which can improve performance (depends on system), namely --use-directio (use direct IO for all operations) can be faster on some systems. See man page.<br>
<br>
Progress and estimated time is printed during reencryption.<br>
<br>
You can suspend reencryption (using ctrl+c or term signal).<br>
To continue reencryption you have to provide only the device parameter (offset is stored in temporary log file).<br>
<br>
Please note LUKS device is marked invalid during reencryption and you have to retain tool temporary files until reencryption finishes.<br>
<br>
Temporary files are <b>LUKS-uuid.[log|org|new]</b>.<br>
<br>
<h4>Other changes</h4>

<ul><li>Fix luks-header-from-active script (do not use LUKS header on-disk, add UUID).<br>
</li><li>Add <code>--test-passphrase option</code> for luksOpen (check passphrase only).<br>
</li><li>Fix parsing of hexadecimal string (salt or root hash) in veritysetup.</li></ul>

<h3>Changes since version 1.4.3</h3>

Introduce veritysetup tool for dm-verity target management.<br>
<br>
The dm-verity device-mapper target was added to Linux kernel 3.4 and provides transparent integrity checking of block devices using a cryptographic digest provided by the kernel crypto API. This target is read-only.<br>
<br>
It is meant to be setup as part of a verified boot path (it was originally developed by Chrome OS authors as part of verified boot infrastructure).<br>
<br>
For deeper description please see <a href='http://code.google.com/p/cryptsetup/wiki/DMVerity'>http://code.google.com/p/cryptsetup/wiki/DMVerity</a> and kernel dm-verity documentation.<br>
<br>
The libcryptsetup library was extended to support manipulation with dm-verity kernel module and new veritysetup CLI tool is added.<br>
<br>
There are no additional library requirements (it uses the same crypto backend as cryptsetup).<br>
<br>
If you want compile cryptsetup without veritysetup toop, use <code>--disable-veritysetup</code> configure option. For other configuration option see <code>configure --help</code> and <code>veritysetup --help</code> (e.g. default parameters).<br>
<br>
Supported libcryptsetup functions new CRYPT_VERITY type:<br>
<pre><code>  crypt_init<br>
  crypt_init_by_name<br>
  crypt_set_data device<br>
  crypt_get_type<br>
  crypt_format<br>
  crypt_load<br>
  crypt_get_active_device<br>
  crypt_activate_by_volume_key (volume key == root hash here)<br>
  crypt_dump<br>
</code></pre>
and new introduced function<br>
<pre><code>  crypt_get_verity_info<br>
</code></pre>

Please see comments in <a href='http://code.google.com/p/cryptsetup/source/browse/lib/libcryptsetup.h'>libcryptsetup.h</a> and <a href='http://code.google.com/p/cryptsetup/source/browse/src/veritysetup.c'>veritysetup.c</a> as an code example how to use CRYPT_VERITY API.<br>
<br>
The <b>veritysetup</b> tool supports these operations:<br>
<br>
<ul><li><code>veritysetup format &lt;data_device&gt; &lt;hash_device&gt;</code><br>Formats <i>hash_device</i> (calculates all hash areas according to <i>data_device</i>).<br>This is initial command to prepare device <i>hash_device</i> for later verification.</li></ul>

<ul><li><code>veritysetup create &lt;name&gt; &lt;data_device&gt; &lt;hash_device&gt; &lt;root_hash&gt;</code><br>Creates (activates) a dm-verity mapping with <i>name</i> backed by device <i>data_device</i><br>and using <i>hash_device</i> for in-kernel verification.</li></ul>

<ul><li><code>veritysetup verify &lt;data_device&gt; &lt;hash_device&gt; &lt;root_hash&gt;</code><br>Verifies data in userspace (no kernel device is activated).</li></ul>

<ul><li><code>veritysetup remove &lt;name&gt;</code><br>Removes activated device from kernel (similar to dmsetup remove).</li></ul>

<ul><li><code>veritysetup status &lt;name&gt;</code><br>Reports status for the active kernel dm-verity device.</li></ul>

<ul><li><code>veritysetup dump &lt;hash_device&gt;</code><br>Reports parameters of verity device from on-disk stored superblock.</li></ul>

For more info see <code>veritysetup</code> --help and veritysetup man page.<br>
<br>
<h3>Other changes</h3>

<ul><li>Both data and header device can now be a file and<br>loop device is automatically allocated.</li></ul>

<ul><li>Require only up to last keyslot area for header device, previously<br>backup (and activation) required device/file of size up to data start<br>offset (data payload).</li></ul>

<ul><li>Fix header backup and restore to work on files with large data offset.<br>Backup and restore now works even if backup file is smaller than data offset.</li></ul>

<h3>Appendix: Examples of veritysetup use</h3>

<h4>Format device using default parameters, info and final root hash is printed:</h4>
<pre><code> # veritysetup format /dev/sdb /dev/sdc<br>
 VERITY header information for /dev/sdc<br>
 UUID:                   fad30431-0c59-4fa6-9b57-732a90501f75<br>
 Hash type:              1<br>
 Data blocks:            52224<br>
 Data block size:        4096<br>
 Hash block size:        4096<br>
 Hash algorithm:         sha256<br>
 Salt:                   5cc52759af76a092e0c21829cd0ef6938f69831bf86926525106f92a7e9e3aa9<br>
 Root hash:              7aefa4506f7af497ac491a27f862cf8005ea782a5d97f6426945a6896ab557a1<br>
</code></pre>

<h4>Activation of device in-kernel:</h4>
<pre><code> # veritysetup create vr /dev/sdb /dev/sdc 7aefa4506f7af497ac491a27f862cf8005ea782a5d97f6426945a6896ab557a1<br>
</code></pre>
<blockquote>Note - if device is corrupted, kernel mapping is created but will report failure:<br>
<pre><code> Verity device detected corruption after activation.<br>
</code></pre></blockquote>

<h4>Userspace verification:</h4>
<pre><code> # veritysetup verify /dev/sdb /dev/sdc 7aefa4506f7af497ac491a27f862cf8005ea782a5d97f6426945a6896ab557a1<br>
  Verification failed at position 8192.<br>
  Verification of data area failed.<br>
</code></pre>

<h4>Active device status report:</h4>
<pre><code> # veritysetup status vr<br>
 /dev/mapper/vr is active.<br>
   type:        VERITY<br>
   status:      verified<br>
   hash type:   1<br>
   data block:  4096<br>
   hash block:  4096<br>
   hash name:   sha256<br>
   salt:        5cc52759af76a092e0c21829cd0ef6938f69831bf86926525106f92a7e9e3aa9<br>
   data device: /dev/sdb<br>
   size:        417792 sectors<br>
   mode:        readonly<br>
   hash device: /dev/sdc<br>
   hash offset: 8 sectors<br>
</code></pre>

<h4>Dump of on-disk superblock information:</h4>
<pre><code> # veritysetup dump /dev/sdc<br>
 VERITY header information for /dev/sdc<br>
 UUID:                   fad30431-0c59-4fa6-9b57-732a90501f75<br>
 Hash type:              1<br>
 Data blocks:            52224<br>
 Data block size:        4096<br>
 Hash block size:        4096<br>
 Hash algorithm:         sha256<br>
 Salt:                   5cc52759af76a092e0c21829cd0ef6938f69831bf86926525106f92a7e9e3aa9<br>
</code></pre>

<h4>Remove mapping:</h4>
<pre><code> # veritysetup remove vr<br>
</code></pre>
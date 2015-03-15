# dm-crypt: Linux kernel device-mapper crypto target #

## About dm-crypt ##
[Device-mapper](http://sourceware.org/lvm2/) is infrastructure in the Linux 2.6 and 3.x kernel that provides a generic way to create virtual layers of block devices.

Device-mapper crypt target provides transparent encryption of block devices using the kernel crypto API.

The user can basically specify one of the symmetric ciphers, an encryption mode,
a key (of any allowed size), an iv generation mode and then the user can create
a new block device in /dev.

Writes to this device will be encrypted and reads decrypted.
You can mount your filesystem on it as usual or stack dm-crypt device with another device like RAID or LVM volume.

Basic documentation of dm-crypt mapping table comes with kernel source and the latest version is available
[in git repository](http://git.kernel.org/?p=linux/kernel/git/torvalds/linux-2.6.git;a=blob;f=Documentation/device-mapper/dm-crypt.txt).

To enable dm-crypt support, enable **CONFIG\_DM\_CRYPT** in Device Drivers/Multi-device support (RAID and LVM) configuration option. Most of distributions have dm-crypt included by default.

To configure you need userspace components: **device mapper library** (part of LVM2 package) and **cryptsetup**.
All these packages are usually included in your distro repository already.

Check **/proc/crypto** which  contains supported ciphers and modes (but note it contains only currently loaded crypto API modules).<br><i>There is currently no better way how to list all available variations of cipher and modes if the crypto modules are not loaded.</i>

<h1>Device mapper crypt table mapping line specification</h1>

Mapping table in device mapper is defined like<br>
<pre><code>&lt;start_sector&gt; &lt;size&gt; &lt;target name&gt; &lt;target mapping table&gt;<br>
</code></pre>
<ul><li><b>start_sector</b> is 0 (for tables using only one mapped segment, iow table contains only one line)<br>
</li><li><b>size</b> is size of device in sectors<br>
</li><li><b>target name</b> is name of mapping target, here "crypt" for dm-crypt</li></ul>

Sectors are always 512B sectors (even if device has bigger hw sector like 4k).<br>
<br>
Table fields are separated by space.<br>
<br>
<h2>dm-crypt target version</h2>
Every device-mapper target has internal version which is increased when some new feature is added. (With exceptions caused by developer's forgetfulness. ;-)<br>
<br>
To check which version you have installed, load the dm target module (dm-crypt.ko for dm-crypt) and use "dmsetup target" to check version.<br>
<br>
Note that crypt version target is not necessarily related to exact Linux kernel version, some distributions backport changes, so to check if some feature is implemented you need always check dm-crypt target version.<br>
<br>
<h2>Mapping table for crypt target</h2>

The basic syntax is common for all 1.x.y dm-crypt target versions.<br>
If some extension was added later, it is mentioned in the description.<br>
<pre><code>&lt;cipher[:keycount]-chainmode-ivmode[:ivopts]&gt; &lt;key&gt; &lt;iv_offset&gt; &lt;device path&gt; &lt;offset&gt; [&lt;#opt_params&gt; &lt;opt_params&gt;]<br>
</code></pre>

<ul><li><b>cipher</b>: Encryption block cipher.<br><i>Example: aes, twofish, serpent.</i></li></ul>

<ul><li><b>keycount</b>: Multi-key compatibility mode.<br>You can define <i>keycount</i> keys and then sectors are encrypted according to their offsets<br>(sector 0 uses key0, sector 1 uses key1 etc). <i>keycount</i> must be a power of two.<br>This parameter is used for loop-AES compatibility mode.<br><i>Available since: 1.10.0 (kernel 2.6.38)</i></li></ul>

<ul><li><b>chainmode</b>: Block cipher encryption mode.<br><i>Example: cbc, xts.</i></li></ul>

<ul><li><b>ivmode</b>: Initialization Vector (IV) used for selected block mode (if block mode requires IV).<br>Unless block ciphers and encryption modes, IV generators are implemented directly inside dm-crypt module.</li></ul>

<h3>IV generators</h3>

<ul><li><b>plain</b>: the initial vector is the 32-bit little-endian version of the sector number, padded with zeros if necessary.</li></ul>

<ul><li><b>plain64</b>: the initial vector is the 64-bit little-endian version of the sector number, padded with zeros if necessary.<br><i>Available since: 1.7.0 (no proper version set) (kernel 2.6.33)</i></li></ul>

<ul><li><b>essiv</b>: "encrypted sector|salt initial vector", the sector number is encrypted with the bulk cipher using a salt as key.<br>The salt is derived from the bulk cipher's key via hashing.<br><i>ESSIV takes hash algorithm as option, so the format is essiv:hash, e.g. essiv:sha256.</i><br><i>Available since: 1.1.0 (kernel 2.6.10)</i></li></ul>

<ul><li><b>benbi</b>: the 64-bit "big-endian 'narrow block'-count", starting at 1<br><i>Available since: 1.3.0 (no proper version set) (kernel 2.6.20)</i></li></ul>

<ul><li><b>null</b>: the initial vector is always zero.  Provides compatibility with obsolete loop_fish2 devices.<br><i>Available since: 1.5.0 (kernel 2.6.22)</i></li></ul>

<ul><li><b>lmk</b>:  Compatible implementation of the block chaining mode used by the Loop-AES block device encryption system.<br><i>Available since: 1.10.0 (kernel 2.6.38)</i></li></ul>

<ul><li><b>tcw</b>: Compatible implementation of the key seeded IV with additional whitening (to CBC mode)<br>used by the TrueCrypt encryption system (prior to version 4.2).<br><i>Available since: 1.13.0 (kernel 3.13)</i></li></ul>

Examples of full cipher/mode/iv specifications:<br>
<pre><code>aes-cbc-essiv:sha256<br>
aes-xts-plain64<br>
aes:64-cbc-lmk<br>
twofish-ecb<br>
serpent-cbc-plain<br>
</code></pre>

<ul><li><b>key</b>: Key used for encryption. It is encoded as a hexadecimal number (one character represents 4 bits).<br>You can only use key sizes that are valid for the selected cipher.<br>For multikey mode are all keys of the same length concatenated to one string.</li></ul>

<ul><li><b>iv_offset</b>: The IV offset is a sector count that is added to the sector number before creating the IV.<br>It can be used to create a map that starts after the first encrypted sector.<br>Usually you'll set it to zero except your device is only partially available or you need to configure some mode compatible with other encryption system.</li></ul>

<ul><li><b>device path</b>: This is the device that is going to be used as backend and contains the encrypted data.<br>You can specify it as a path like /dev/xxx or a device number major:minor.</li></ul>

<ul><li><b>offset</b>: Starting sector within the device where the encrypted data begins.</li></ul>

<ul><li><b>#opt_params</b>: Number of optional parameters. If there are no optional parameters, the optional parameters section can be skipped or it can be zero. Otherwise it is the number of following arguments.<br><i>Available since: 1.11.0 (kernel 3.1)</i></li></ul>

<h3>Optional parameters</h3>

<ul><li><b>allow_discards</b>: Allow block discard requests (a.k.a. TRIM) for the crypt device.<br>The default is to ignore discard requests.<br>Assess the specific security risks carefully before enabling this option. For example, allowing discards on encrypted devices<br>may lead to the leak of information about the ciphertext device (filesystem type, used space etc.) if the discarded blocks<br>can be located easily on the device later.<br><i>Available since: 1.11.0 (kernel 3.1)</i></li></ul>

<ul><li><b>same_cpu_crypt</b>: Perform encryption using the same cpu that IO was submitted on.<br>The default is to use an unbound workqueue so that encryption work is automatically balanced between available CPUs.<br><i>Available since: 1.14.0 (kernel 4.0)</i></li></ul>

<ul><li><b>submit_from_crypt_cpus</b>: Disable offloading writes to a separate thread after encryption.<br>There are some situations where offloading write bios from the encryption threads to a single thread degrades performance significantly.<br>The default is to offload write bios to the same thread because it benefits CFQ to have writes submitted using the same context.<br><i>Available since: 1.14.0 (kernel 4.0)</i></li></ul>

Examples of optional parameters section:<br>
<pre><code>1 allow_discards<br>
3 allow_discards same_cpu_crypt submit_from_crypt_cpus<br>
</code></pre>

<h3>Example of full mapping table</h3>
<pre><code>0 417792 crypt aes-xts-plain64 e8cfa3dbfe373b536be43c5637387786c01be00ba5f730aacb039e86f3eb72f3 0 8:16 0<br>
|    |     |    |   |     |                                 |                                   |  |   | <br>
start|     |    |  mode   IV                                |                                   |  |   offset<br>
     size  |  cipher                                        |                                   |  device<br>
         target                                        256bit-key                          IV offset<br>
<br>
<br>
0 417792 crypt serpent-cbc-essiv:sha256 a7f67ad520bd83b9725df6ebd76c3eee 0 /dev/sdb 0<br>
|    |     |      |     |     |     |                |                   |     |    | <br>
start|     |      |    mode   IV  IV-opts            |                   |     |   offset<br>
     size  |    cipher                               |                   |  device<br>
         target                                 128bit-key          IV offset<br>
<br>
<br>
Optional section example:<br>
<br>
0 417792 crypt serpent-cbc-essiv:sha256 a7f67ad520bd83b9725df6ebd76c3eee 0 /dev/sdb 0 1 allow_discards<br>
                                                                                      |        | <br>
                                                                                      |   optional parameter<br>
                                                                                    count<br>
<br>
</code></pre>

<h2>Configuration with dmsetup tool</h2>

Dmsetup is used to create and remove devices, get information about<br>
devices or reload tables (that means changing the mapping while the device is in use).<br>
<br>
Usually this tool is only used for low-level access to dm device,<br>
example here is mentioned just to show how the low level parameters works.<br>
Always prefer using cryptsetup if possible.<br>
<br>
To create device and specify mapping table, use<br>
<code>dmsetup create &lt;name&gt; --table " ..."</code> command.<br>
<br>
The second example above is then created using<br>
<pre><code>dmsetup create x --table "0 $(blockdev --getsz /dev/sdb) crypt serpent-cbc-essiv:sha256 a7f67ad...ee 0 /dev/sdb 0"<br>
</code></pre>

You can check the full mapping table using <code>dmsetup table</code> with optional <code>--showkeys</code> parameter.<br>
<br>
Note that for all device-mapper operations is required root privilege (CAP_SYSADMIN).<br>
<br>
The newly created device then appears as <b>/dev/mapper/name</b>.<br>
<br>
<h2>Configuration using cryptsetup</h2>

cryptsetup utility support several modes. <b>Plain mode</b> is just equivalent of direct configuration of dmcrypt target with passphrase hashing but without on-disk metadata.<br>
<br>
<b>LUKS</b> (Linux Unified Key Setup) is now the preferred way to set up disk<br>
encryption with dm-crypt using the <b>cryptsetup</b> utility, see <a href='http://code.google.com/p/cryptsetup/'>cryptsetup project page</a>.<br>
<br>
<h3>Example of using cryptsetup</h3>

If you have keyfile (in binary format) in file /key, you can setup mapping in example above as<br>
<pre><code>cryptsetup -d /key -s 128 -c serpent-cbc-essiv:sha256 create &lt;name&gt; /dev/sdb<br>
</code></pre>
If you want to use LUKS on-disk metadata with default cipher, use<br>
<pre><code>cryptsetup luksFormat &lt;device&gt;<br>
cryptsetup luksOpen &lt;device&gt; &lt;name&gt;<br>
</code></pre>
<hr />
The DMCrypt page is written and maintained by Milan Broz (with help of other project users and developers).<br>Please use <a href='http://code.google.com/p/cryptsetup/issues/entry'>New issue</a> report if there is some bug or problem on this page, thank you.
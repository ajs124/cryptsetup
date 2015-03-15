# dm-verity: device-mapper block integrity checking target #

## About dm-verity ##
[Device-mapper](http://sourceware.org/lvm2/) is infrastructure in the Linux 2.6 and 3.x kernel that provides a generic way to create virtual layers of block devices.

Device-mapper verity target provides read-only transparent integrity checking of block devices using kernel crypto API.

Basic documentation of dm-verity mapping table comes with kernel source and the latest version is available
[in git repository](http://git.kernel.org/?p=linux/kernel/git/torvalds/linux-2.6.git;a=blob;f=Documentation/device-mapper/verity.txt).

The dm-verity was designed and developed by Chrome OS authors for [verified boot](http://www.chromium.org/chromium-os/chromiumos-design-docs) implementation.

To enable dm-verity support, enable **CONFIG\_DM\_VERITY** in Device Drivers/Multi-device support (RAID and LVM) configuration option.

To configure you need userspace components: **device mapper library** (part of LVM2 package) and **veritysetup**.

# Device mapper verity table mapping line specification #

Mapping table in device mapper is defined like
```
<start_sector> <size> <target name> <target mapping table>
```
  * **start\_sector** is 0 (for tables using only one mapped segment, iow table contains only one line)
  * **size** is size of device in sectors
  * **target name** is name of mapping target, here "verity" for dm-verity

Sectors are always 512B sectors (even if device has bigger hw sector like 4k).

Table fields are separated by space.

## dm-verity target version ##
Every device-mapper target has internal version which is increased when some new feature is added.

To check which version you have installed, load the dm target module (dm-verity.ko for dm-verity) and use "dmsetup targets" to check version.

## Mapping table for verity target ##

The basic syntax is common for all 1.x.y dm-verity target versions.
If some extension was added later, it is mentioned in the description.
```
<ver> <data_dev> <hash_dev> <data_blk_size> <hash_blk_size> <#blocks> <hash_start> <alg> <digest> <salt>
```

  * **ver**: on-disk hash version
    * **0** is the original format used in the Chromium OS.<br>The salt is appended when hashing, digests are stored continuously and<br>the rest of the block is padded with zeros.<br>
<ul><li><b>1</b> is the current format that should be used for new devices.<br>The salt is prepended when hashing and each digest is<br>padded with zeros to the power of two.</li></ul></li></ul>

<ul><li><b>data_dev</b>: device containing the data the integrity of which needs to be checked.<br>It may be specified as a path, like /dev/sdaX, or a device number, major:minor.</li></ul>

<ul><li><b>hash_dev</b>: device that that supplies the hash tree data.<br>It may be specified similarly to the data device path and may be the same device.<br>If the same device is used, the hash_start should be outside of the dm-verity configured device size.</li></ul>

<ul><li><b>data_blk_size</b>: The block size in bytes on a data device.  Each block corresponds to one digest on the hash device.</li></ul>

<ul><li><b>hash_blk_size</b>: The size of a hash block in bytes.</li></ul>

<ul><li><b>#blocks</b>: The number of data blocks on the data device. Additional blocks are inaccessible.</li></ul>

<ul><li><b>hash_start</b>: offset, in hash_blk_size blocks, from the start of hash_device to the root block of the hash tree.</li></ul>

<ul><li><b>alg</b>: The cryptographic hash algorithm used for this device. This should be the name of the algorithm, like "sha256".</li></ul>

<ul><li><b>digest</b>: The hexadecimal encoding of the cryptographic hash of the root hash block and the salt.<br>This hash should be trusted.</li></ul>

<ul><li><b>salt</b>: The hexadecimal encoding of the salt value.</li></ul>

<h3>Example of full mapping table</h3>
<pre><code>0 417792 verity 1 /dev/sdb /dev/sdc 4096 4096 52224 1 sha256 2aa4f7b7b6...f4952060e8 762307f4bc8...d2a6b7595d8<br>
|    |     |    |     |     |        |    |    |    |    |              |                        |<br>
start|     |    |  data_dev |  data_block | #blocks | hash_alg      root_digest                salt<br>
     size  |  version    hash_dev         |     hash_offset<br>
         target                       hash_block<br>
</code></pre>

<h2>Configuration with dmsetup tool</h2>

Dmsetup is used to create and remove devices, get information about<br>
devices or reload tables (that means changing the mapping while the device is in use).<br>
<br>
Usually this tool is only used for low-level access to dm device,<br>
example here is mentioned just to show how the low level parameters works.<br>
Always prefer using veritysetup if possible.<br>
<br>
To create device and specify mapping table, use<br>
<code>dmsetup create &lt;name&gt; --table " ..."</code> command.<br>
<br>
The example above is then created using (after hash device was populated with hashes)<br>
<pre><code>SIZE=$(blockdev --getsz /dev/sdb)<br>
dmsetup create name -r --table "0 $SIZE verity 1 /dev/sdb /dev/sdc 4096 4096 52224 1 sha256 &lt;root_hash&gt; &lt;salt&gt;"<br>
</code></pre>

You can check the full mapping table using <code>dmsetup table</code>.<br>
<br>
Note that for all device-mapper operations is required root privilege (CAP_SYSADMIN).<br>
<br>
The newly created device then appears as <b>/dev/mapper/name</b>.<br>
<br>
<h2>Configuration using veritysetup</h2>

Preparing hash device (formatting)<br>
<pre><code>veritysetup format /dev/sdb /dev/sdc<br>
</code></pre>

Activation of verity data device<br>
<pre><code>veritysetup create name /dev/sdb /dev/sdc &lt;root_digest&gt;<br>
</code></pre>

<h2>Theory of operation</h2>

Dm-verity is meant to be setup as part of a verified boot path.<br>
<br>
When a dm-verity device is configured, it is expected that the caller has been authenticated in some way. After instantiation, all hashes will be verified on-demand during disk access. If they cannot be verified up to the root node of the tree, the root hash, then the I/O will fail. This should identify tampering with any data on the device and the hash data.<br>
<br>
Cryptographic hashes are used to assert the integrity of the device on a per-block basis. This allows for a lightweight hash computation on first read into the page cache. Block hashes are stored linearly, aligned to the nearest block size.<br>
<br>
<h2>Hash Tree</h2>

Each node in the tree is a cryptographic hash. If it is a leaf node, the hash is calculated of some data block on disk. If it is an intermediary node, the hash is calculated of a number of child nodes.<br>
<br>
Each entry in the tree is a collection of neighboring nodes that fit in one block. The number is determined based on block_size and the size of the selected cryptographic digest algorithm. The hashes are linearly-ordered in this entry and any unaligned trailing space is ignored but included when calculating the parent node.<br>
<br>
The tree looks something like:<br>
<pre><code>alg = sha256, num_blocks = 32768, block_size = 4096<br>
<br>
                                 [   root    ]<br>
                                /    . . .    \<br>
                     [entry_0]                 [entry_1]<br>
                    /  . . .  \                 . . .   \<br>
         [entry_0_0]   . . .  [entry_0_127]    . . . .  [entry_1_127]<br>
           / ... \             /   . . .  \             /           \<br>
     blk_0 ... blk_127  blk_16256   blk_16383      blk_32640 . . . blk_32767<br>
</code></pre>

<h2>Verity superblock format</h2>

While the kernel dm-verity doesn't read any on-disk metadata stored information (except hash blocks itself), it is useful to store hash device metadata information permanently to disk.<br>
The only trusted information is then root hash which is stored outside of data and hash verification device pair.<br>
<br>
Verity uses superblock by default (real hash area is then starting on next hash block offset after verity superblock). You can always disable use of verity suberblock by use of <code>--no-superblock</code> option.<br>
<br>
The current on-disk dm-verity specification (size of superblock is always 512 bytes):<br>
<pre><code>struct verity_sb {<br>
  uint8_t  signature[8];    /* "verity\0\0" */<br>
  uint32_t version;         /* superblock version, 1 */<br>
  uint32_t hash_type;       /* 0 - Chrome OS, 1 - normal */<br>
  uint8_t  uuid[16];        /* UUID of hash device */<br>
  uint8_t  algorithm[32];   /* hash algorithm name */<br>
  uint32_t data_block_size; /* data block in bytes */<br>
  uint32_t hash_block_size; /* hash block in bytes */<br>
  uint64_t data_blocks;     /* number of data blocks */<br>
  uint16_t salt_size;       /* salt size */<br>
  uint8_t  _pad1[6];<br>
  uint8_t  salt[256];       /* salt */<br>
  uint8_t  _pad2[168];<br>
} __attribute__((packed));<br>
</code></pre>
<hr />
The DMVerity page is written and maintained by Milan Broz (with help of other project users and developers).<br>Please use <a href='http://code.google.com/p/cryptsetup/issues/entry'>New issue</a> report if there is some bug or problem on this page, thank you.
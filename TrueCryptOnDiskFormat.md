# TrueCrypt on-disk header format #

This format description is based on original TrueCrypt manuals but mainly on independent cryptsetup implementation.

## TrueCrypt Containers ##

The encryption key is stored on the device itself in TrueCrypt header. The disk containing header and encrypted data is TrueCrypt container.

Header is encrypted with the same algorithm as the data but with key derived from user provided password and salt.

There are three volume types which use separate header locations:
  * **Normal encryption**
  * **Hidden volume** (another volume hidden in decoy encrypted volume)
  * **System encryption** (encryption of the bootable operating system partition)

## Header Format ##
For each encrypted volume type, location of the header on disk is on fixed and different position but all headers use the same internal format.

TrueCrypt header is always of length 512 bytes (one sector). Since TrueCrypt version 6.0 the reserved area for header was increased to 128kB but remaining space is used only to properly align data area for disks with larger block sizes. Cryptsetup reads only these 512 bytes ignoring remaining space.

TrueCrypt header starts with 64 bytes of randomly generated salt, different for each header (backup headers use always different salt even they are encrypted with the same password and stores the same master keys). The remaining 448 bytes contains metadata information about container and directly stored encryption keys and are encrypted by an algorithm from known algorithm list with key derived from provided password and salt.

The used algorithms (for key derivation and encryption) are found by trying all known algorithms with the provided password. Header is considered properly decrypted when its decrypted content (plaintext) starts with ASCII string "TRUE" and all available checksums are correct.

All integer values stored on disk are in Big Endian format (most significant byte stored first) except version\_tc which is surprisingly stored in Little Endian format.

### Content of on-disk TrueCrypt header structure ###
```
struct TrueCrypt_Header {

  /* Salt is visible (appears as random bytes) */
  char salt[64];

  /* Format (after decryption), 512 - 64(salt) = 448 bytes */
  char magic[4];          /* "TRUE" in ASCII */
  uint16_t version;       /* Header format version */
  uint16_t version_tc;    /* Minimal required program version */
  uint32_t keys_crc32;    /* CRC32 of keys area */
  uint64_t _reserved1[2]; /* unused (former modification time) */
  uint64_t hidden_volume_size; /* size of hidden volume or zero*/
  uint64_t volume_size;   /* size of volume or zero */
  uint64_t mk_offset;     /* encrypted payload offset in bytes */
  uint64_t mk_size;       /* encrypted area size; ignored by cryptsetup */
  uint32_t flags;         /* system/in-place; ignored by cryptsetup */
  uint32_t sector_size;   /* sector size in bytes, always 512 */
  uint8_t  _reserved2[120]; /* unused */
  uint32_t header_crc32;  /* CRC32 of header without keys; for version > 3 */

  /* Key storage area */
  char keys[256];
}
```

TrueCrypt automatically reads backup header (available since version 6.0) if primary header is damaged. To the contrary, cryptsetup requires exact specification which header user wants to use.

### Header locations on-disk and corresponding cryptsetup parameters ###
| **Header** | **Sector offset**<br>(512 bytes sectors) <table><thead><th> <b>Introduced</b> <br>(TC version) </th><th> <b>Obsoleted</b><br>(TC version) </th><th> <b>cryptsetup parameters</b> </th></thead><tbody>
<tr><td> Standard </td><td> 0 </td><td> 1.0 </td><td> - </td><td> - </td></tr>
<tr><td> Backup </td><td> -256<sup>*</sup> </td><td> 6.0 </td><td> - </td><td> --tcrypt-backup </td></tr>
<tr><td> Hidden </td><td> -3<sup>*</sup> </td><td> 3.0 </td><td> 6.0 </td><td> --tcrypt-hidden </td></tr>
<tr><td> Hidden </td><td> 128 </td><td> 6.0 </td><td> - </td><td> --tcrypt-hidden </td></tr>
<tr><td> Hidden backup </td><td> -128</td><td> 6.0 </td><td> - </td><td> --tcrypt-hidden<br>--tcrypt-backup </td></tr>
<tr><td> System </td><td> 62<sup>**</sup> </td><td> 5.0 </td><td> - </td><td> --tcrypt-system </td></tr></tbody></table>

<sup>*</sup> Offset from the end of disk.<br><sup>**</sup> Legacy MS DOS 1st partition is aligned to 63 sector.<br>
<br>
The system encryption header (the header for operating system partition) is located outside of partition itself, in space between partition table (with bootloader) and system partition start.<br>
<br>
<i>Location on 62 sector is probably derived from old MS DOS partition tables where the first partition always starts on 63 sector (so header is located in last "unused" sector; this applies even if first partition alignment is different). There is no guarantee that this area is not used by another software, so various disk management system can easily corrupt this header. Also system header has no backup on-disk, you have to use recovery procedure if such data corruption occurs.</i>

<h3>Example how to analyze header with cryptsetup</h3>
<pre>
$ cryptsetup tcryptDump /dev/sdb --tcrypt-backup<br>
Enter passphrase: ****<br>
<br>
TCRYPT header information for /dev/sdb<br>
Version:        5<br>
Driver req.:    7<br>
Sector size:    512<br>
MK offset:      131072<br>
PBKDF2 hash:    sha512<br>
Cipher chain:   aes<br>
Cipher mode:    xts-plain64<br>
MK bits:        512<br>
<br>
$ src/cryptsetup tcryptDump -q /dev/sdb --tcrypt-backup --dump-master-key<br>
Enter passphrase: ****<br>
TCRYPT header information for /dev/sdb<br>
Cipher chain:   aes<br>
Cipher mode:    xts-plain64<br>
Payload offset: 256<br>
MK bits:        512<br>
MK dump:        ad 78 33 d2 48 d5 c6 b6 32 e7 ce 9d 91 e1 ed 46<br>
38 a9 cf ca e7 66 ee 67 b0 24 31 29 93 3b 20 40<br>
c6 1b 5f 28 1a 35 ed 64 77 8b ee 50 3f 41 19 75<br>
52 3a 30 d0 83 c0 99 81 f1 49 87 ee b1 ad a1 e3<br>
</pre>

<h2>Key Derivation Algorithms</h2>
To derive key to decrypt a TrueCrypt header password based key derivation function (PBKDF2) is used.<br>
<br>
Maximal password length is limited to 64 bytes (length is limited by internal pool for keyfiles processing).<br>
<br>
<h3>Hash algorithms and iteration count used in PBKDF2</h3>
<table><thead><th> <b>Algorithm</b> </th><th> <b>PBKDF2</b><br>iterations</th><th> <b>Introduced</b><br>(TC version) </th><th> <b>Obsoleted</b><br>(TC version) </th></thead><tbody>
<tr><td> SHA1 </td><td> 2000 </td><td> 1.0 </td><td> 5.0 </td></tr>
<tr><td> SHA512 </td><td> 1000 </td><td> 5.0 </td><td> - </td></tr>
<tr><td> RIPEMD160 </td><td> 2000 </td><td> 2.1 </td><td> - </td></tr>
<tr><td> RIPEMD160 </td><td> 1000<sup>*</sup> </td><td> 5.0 </td><td> - </td></tr>
<tr><td> Whirlpool </td><td> 1000 </td><td> 4.0 </td><td> - </td></tr></tbody></table>

<sup>*</sup> Used for system encryption (processing in boot loader) only.<br>
<br>
<h2>Block Encryption Algorithms and Modes</h2>

<table><thead><th> <b>Algorithm</b> </th><th> <b>Block</b><br>(bits) </th><th> <b>Key</b><br>(bits) </th><th> <b>Mode</b> </th><th> <b>Introduced</b><br>(TC version) </th><th> <b>Obsoleted</b><br>(TC version) </th></thead><tbody>
<tr><td> AES256 </td><td> 128 </td><td> 256 </td><td> XTS </td><td> 5.0 </td><td> - </td></tr>
<tr><td> AES256 </td><td> 128 </td><td> 256 </td><td> LRW </td><td> 4.1 </td><td> 5.0 </td></tr>
<tr><td> AES256 </td><td> 128 </td><td> 256 </td><td> CBC </td><td> 2.0 </td><td> 4.1 </td></tr>
<tr><td> Serpent </td><td> 128 </td><td> 256 </td><td> XTS </td><td> 5.0 </td><td> - </td></tr>
<tr><td> Serpent </td><td> 128 </td><td> 256 </td><td> LRW </td><td> 4.1 </td><td> 5.0 </td></tr>
<tr><td> Serpent </td><td> 128 </td><td> 256 </td><td> CBC </td><td> 3.0 </td><td> 4.1 </td></tr>
<tr><td> Twofish </td><td> 128 </td><td> 256 </td><td> XTS </td><td> 5.0 </td><td> - </td></tr>
<tr><td> Twofish </td><td> 128 </td><td> 256 </td><td> LRW </td><td> 4.1 </td><td> 5.0 </td></tr>
<tr><td> Twofish </td><td> 128 </td><td> 256 </td><td> CBC </td><td> 3.0 </td><td> 4.1 </td></tr>
<tr><td> Blowfish (LE) </td><td> 64 </td><td> 448 </td><td> LRW </td><td> 4.1 </td><td> 4.3 </td></tr>
<tr><td> Blowfish (LE) </td><td> 64 </td><td> 448 </td><td> CBC </td><td> 1.0 </td><td> 4.1 </td></tr>
<tr><td> CAST5 </td><td> 64 </td><td> 128 </td><td> LRW </td><td> 4.1 </td><td> 4.3 </td></tr>
<tr><td> CAST5 </td><td> 64 </td><td> 128 </td><td> CBC </td><td> 1.0 </td><td> 4.1 </td></tr>
<tr><td> TripleDES (EDE) </td><td> 64 </td><td> 168 </td><td> LRW </td><td> 4.1 </td><td> 4.3 </td></tr>
<tr><td> TripleDES (EDE) </td><td> 64 </td><td> 168 </td><td> CBC </td><td> 1.0 </td><td> 4.1 </td></tr>
<tr><td> IDEA </td><td> 64 </td><td> 128 </td><td> CBC </td><td> 1.0 </td><td> 2.1a </td></tr></tbody></table>

<i>Cryptsetup uses common library for implementing cryptographic functions. and not all algorithms from list are available. For example, Blowfish cipher is not available because TrueCrypt uses it in Little Endian version and also Blowfish LRW mode is not available (missing support for 64bit block size). All ciphers since version 6.0 are fully supported though.</i>

<h2>Block Encryption Modes</h2>
Encrypted containers always use independently encrypted sectors (of size 512 bytes) despite underlying device can have larger hardware sector size.<br>
<br>
<h3>CBC Mode with Whitening</h3>
In CBC mode,  TrueCrypt adds whitening operation. Whitening is calculated using multiple cyclic redundancy code (CRC32) operations over sector number and seeded by secret key KW. Calculated per-sector value is then repeatedly applied to whole ciphertext sector.<br>
<br>
The initial vector (IV) required for CBC mode, is calculated by simple xor operation per sector from another key KIV and from sector number.<br>
<br>
<i>IV is partially predictable (for example you can predict next IV in some specific cases because sector numbers are consecutive). Whitening is just linear transformation (CRC32 is not cryptographically secure one way function). TrueCrypt CBC mode is vulnerable to watermarking attack (special pattern detection from ciphertext).</i>

<h3>LRW Mode</h3>

LRW mode uses special tweak key which solves predictable IV problem, so per-sector input can be directly visible sector number (sector number in 64bit Big Endian count starting with 1, in Linux dmcrypt notation it is "benbi" IV).<br>
<br>
<h3>XTS Mode</h3>

The XTS mode uses two independent keys, the first one is used for tweaking and the second for main encryption.<br>
<br>
Initial vector uses plain 64bit sector number starting with 0 (in Linux dmcrypt notation it is "plain64" IV).<br>
<br>
<h3>Example of cryptsetup try loop when searching for used algorithm</h3>
<pre>
# cryptsetup tcryptDump tc.img --debug<br>
...<br>
Enter passphrase:<br>
...<br>
# Trying to load TCRYPT crypt type from device tc.img.<br>
# Crypto backend (gcrypt 1.5.2) initialized.<br>
# Reading TCRYPT header of size 512 bytes from device tc.img.<br>
# TCRYPT: trying KDF: pbkdf2-ripemd160-2000.<br>
# TCRYPT:  trying cipher aes-xts-plain64<br>
# TCRYPT:  trying cipher serpent-xts-plain64<br>
# TCRYPT:  trying cipher twofish-xts-plain64<br>
# TCRYPT:  trying cipher twofish-aes-xts-plain64<br>
...<br>
# TCRYPT: trying KDF: pbkdf2-sha512-1000.<br>
# TCRYPT:  trying cipher aes-xts-plain64<br>
# TCRYPT:  trying cipher serpent-xts-plain64<br>
# TCRYPT:  trying cipher twofish-xts-plain64<br>
# TCRYPT:  trying cipher twofish-aes-xts-plain64<br>
# TCRYPT:  trying cipher serpent-twofish-aes-xts-plain64<br>
# TCRYPT: Signature magic detected.<br>
# TCRYPT: Header version: 5, req. 7, sector 512, mk_offset 131072, hidden_size 0, volume size 33292288<br>
# TCRYPT: Header cipher serpent-twofish-aes-xts-plain64, key size 192<br>
...<br>
</pre>

<h3>Cascade (Chained) Ciphers</h3>

Cascade ciphers (chain of different ciphers) appeared in TrueCrypt 3.0.<br>
<br>
Note that for cryptsetup order of ciphers in sequence is as the same as the keys are stored in header (or in other words, in order used for encryption). Original TrueCrypt uses reverse notation for some cascades.<br>
<br>
For stacking ciphers with different block sizes  TrueCrypt defines "inner CBC" mode. It means, that code applies block mode even inside cipher chain to prepare full block as input for another cipher. Inner mode applies only to cascades with Blowfish cipher.<br>
<br>
If the block size of all ciphers is the same, "outer CBC mode" is used and block output of one cipher is directly used as input for another in chain. Note that whitening and IV applies only once in chain here.<br>
<br>
<h3>Cascaded (chained) block cipher algorithms</h3>
<table><thead><th> <b>Algorithm</b> </th><th> <b>Mode</b> </th><th> <b>Introduced</b><br>(TC version) </th><th> <b>Obsoleted</b><br>(TC version) </th></thead><tbody>
<tr><td> Twofish-AES256 </td><td> XTS </td><td> 5.0 </td><td> - </td></tr>
<tr><td> Twofish-AES256 </td><td> LRW </td><td> 4.1 </td><td> 5.0 </td></tr>
<tr><td> Twofish-AES256 </td><td> outer CBC </td><td> 3.0 </td><td> 4.1 </td></tr>
<tr><td> Serpent-Twofish-AES256 </td><td> XTS </td><td> 5.0 </td><td> - </td></tr>
<tr><td> Serpent-Twofish-AES256 </td><td> LRW </td><td> 4.1 </td><td> 5.0 </td></tr>
<tr><td> Serpent-Twofish-AES256 </td><td> outer CBC </td><td> 3.0 </td><td> 4.1 </td></tr>
<tr><td> AES256-Serpent </td><td> XTS </td><td> 5.0 </td><td> - </td></tr>
<tr><td> AES256-Serpent </td><td> LRW </td><td> 4.1 </td><td> 5.0 </td></tr>
<tr><td> AES256-Serpent </td><td> outer CBC </td><td> 3.0 </td><td> 4.1 </td></tr>
<tr><td> AES256-Twofish-Serpent </td><td> XTS </td><td> 5.0 </td><td> - </td></tr>
<tr><td> AES256-Twofish-Serpent </td><td> LRW </td><td> 4.1 </td><td> 5.0 </td></tr>
<tr><td> AES256-Twofish-Serpent </td><td> outer CBC </td><td> 3.0 </td><td> 4.1 </td></tr>
<tr><td> Serpent-Twofish </td><td> XTS </td><td> 5.0 </td><td> - </td></tr>
<tr><td> Serpent-Twofish </td><td> LRW </td><td> 4.1 </td><td> 5.0 </td></tr>
<tr><td> Serpent-Twofish </td><td> outer CBC </td><td> 3.0 </td><td> 4.1 </td></tr>
<tr><td> Blowfish-AES256 </td><td> inner CBC </td><td> 3.0 </td><td> 4.1 </td></tr>
<tr><td> Serpent-Blowfish-AES256 </td><td> inner CBC </td><td> 3.0 </td><td> 4.1 </td></tr></tbody></table>

<i>Cascades in CBC mode are not supported in cryptsetup for activation because of missing kernel support but it is possible to decrypt header and analyze it.</i>

<i>Implementation of cipher chain for LRW and XTS mode is done by stacking separate kernel encryption devices on top of each other. For LRW, the tweaking key is always the same for all ciphers in chain (while encryption keys are independent). For XTS all keys, including tweaking, are independent.</i>

<h3>Example of activated volume with cipher cascade</h3>
<pre>
# lsblk /dev/loop0 -o NAME,TYPE<br>
loop0 loop<br>
└─tc_2   crypt -> aes-xts-plain64<br>
└─tc_1 crypt -> twofish-xts-plain64<br>
└─tc crypt -> serpent-xts-plain64<br>
<br>
# cryptsetup status tc<br>
/dev/mapper/tc is active.<br>
type: TCRYPT<br>
cipher: serpent-twofish-aes-xts-plain64<br>
...<br>
</pre>

<h2>Encryption Keys</h2>

There are two sets of encryption keys. One is generated from header salt and password and is used to decrypt on-disk header. The second set contains the real (master) encryption keys stored in header.<br>
<br>
There are keys K<sub>1</sub>, K<sub>2</sub>, K<sub>3</sub> used for encryption algorithm corresponding to ciphers in chain (if there is only one cipher or chain of two ciphers, remaining keys are omitted).<br>
<br>
For CBC mode, there are additionally K<sub>IV</sub> used as seed for initial vector calculation and K<sub>W</sub> used as seed for whitening.<br>
<br>
For some reason, password derived key shares K<sub>W</sub> with K<sub>IV</sub>.  For keys decrypted from header these two keys are independent. For LRW mode, there is additionally one tweaking key K<sub>TWK</sub> which is the same for all ciphers in chain. For XTS mode, there are independent tweaking keys K<sub>TWK1</sub>, K<sub>TWK2</sub>, K<sub>TWK3</sub> (the secondary keys for ciphers).<br>
<br>
Proper original documentation of location of these keys was not available, the following illustration is based on cryptsetup implementation.<br>
<br>
<h3>Location of keys in PBKDF2 derived key and in header stored keys</h3>
<img src='http://wiki.cryptsetup.googlecode.com/git/tcrypt-keys.png' />

<hr />
The TrueCryptOnDiskFormat page is written and maintained by Milan Broz.<br>Please use <a href='http://code.google.com/p/cryptsetup/issues/entry'>New issue</a> report if there is some bug or problem on this page, thank you.
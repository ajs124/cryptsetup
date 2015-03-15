### cryptsetup 1.2.0 ###
Released on 2010-12-20.

The stable cryptsetup 1.2.0 release is available at

> http://code.google.com/p/cryptsetup/

> Download [cryptsetup-1.2.0.tar.bz2](http://cryptsetup.googlecode.com/files/cryptsetup-1.2.0.tar.bz2)

Feedback and bug reports are welcomed. You can use  [New issue](http://code.google.com/p/cryptsetup/issues/entry) report page.

## Cryptsetup 1.2.0 Release Notes ##

### Changes since version 1.2.0-rc1 ###

  * Fix crypt\_activate\_by\_keyfile() to work with PLAIN devices.

  * Fix plain create command to properly handle keyfile size.

  * Update translations.

### Changes since version 1.1.3 ###

## Important changes ##

  * Add text version of **FAQ** (Frequently Asked Questions) to distribution.

  * Add selection of random/urandom number generator for luksFormat (option `--use-random` and `--use-urandom`).<br>(This affects only long term volume key in <b>luksFormat</b>, not RNG used for salt and AF splitter).</li></ul>

<blockquote>You can also set the default to `/dev/random` during compilation with `--enable-dev-random`.<br>(Compiled-in default is printed in <code>--help</code> output.</blockquote>

> Be very careful before changing default to blocking `/dev/random` use here.

  * Fix **luksRemoveKey** to not ask for remaining keyslot passphrase, only for removed one.

  * No longer support **luksDelKey** (replaced with luksKillSlot).
    * if you want to remove particular passphrase, use **luksKeyRemove**
    * if you want to remove particular keyslot, use **luksKillSlot**<br>Note that in batch mode <b>luksKillSlot</b> allows removing of any keyslot without question,<br>in normal mode requires passphrase or keyfile from other keyslot.</li></ul>

  * **Default alignment** for device (if not overridden by topology info) is now (multiple of) **1MiB**.<br>This reflects trends in storage technologies and aligns to the same defaults for partitions and volume management.</li></ul>

<ul><li>Allow explicit UUID setting in <b>luksFormat</b> and allow change it later in <b>luksUUID</b> (<code>--uuid</code> parameter).</li></ul>

<ul><li>All commands using key file now allows limited read from keyfile using <code>--keyfile-size</code> and <code>--new-keyfile-size</code> parameters (in bytes).<br>This change also disallows overloading of <code>--key-size</code> parameter which is now exclusively used for key size specification (in bits.)</li></ul>

  * **luksFormat** using pre-generated master key now properly allows using key file<br>(only passphrase was allowed prior to this update).</li></ul>

<ul><li>Add <code>--dump-master-key</code> option for <b>luksDump</b> to perform volume (master) key dump.<br>Note that printed information allows accessing device without passphrase so it must be stored encrypted.<br>
</li></ul>> This operation is useful for simple Key Escrow function (volume key and encryption parameters printed on paper on safe place).<br>This operation requires passphrase or key file.</li></ul>

<ul><li>The reload command is no longer supported.<br>(Use dmsetup reload instead if needed. There is no real use for this function except explicit data corruption:-)</li></ul>

  * Cryptsetup now properly checks if underlying device is in use and disallows **luksFormat**,<br><b>luksOpen</b> and <b>create</b> commands on open (e.g. already mapped or mounted) device.</li></ul>

<ul><li>Option <code>--non-exclusive</code> (already deprecated) is removed.</li></ul>

Libcryptsetup API additions:<br>
<br>
<ul><li>new functions<br>
<ul><li>crypt_get_type() - explicit query to crypt device context type<br>
</li><li>crypt_resize() - new resize command using context<br>
</li><li>crypt_keyslot_max() - helper to get number of supported keyslots<br>
</li><li>crypt_get_active_device() - get active device info<br>
</li><li>crypt_set/get_rng_type() - random/urandom RNG setting<br>
</li><li>crypt_set_uuid() - explicit UUID change of existing device<br>
</li><li>crypt_get_device_name() - get underlying device name</li></ul></li></ul>

<ul><li>Fix optional password callback handling.</li></ul>

<ul><li>Allow to activate by internally cached volume key immediately after crypt_format()<br>without active slot (for temporary devices with on-disk metadata)</li></ul>

  * libcryptsetup is binary compatible with 1.1.x release and still supports legacy API calls

  * cryptsetup binary now uses only new API calls.

  * Static compilation of both library (`--enable-static`) and cryptsetup binary (`--enable-static-cryptsetup`)<br>is now properly implemented by common libtool logic.<br>Prior to this it produced miscompiled dynamic cryptsetup binary with statically linked libcryptsetup.<br>
</li></ul>> The static binary is compiled as `src/cryptsetup.static` in parallel with dynamic build if requested.

## Other changes ##
  * Fix default plain password entry from terminal in activate\_by\_passphrase.
  * Initialize volume key from active device in crypt\_init\_by\_name()
  * Fix cryptsetup binary exit codes.<br>0 - success, otherwise fail<br>1 - wrong parameters<br>2 - no permission<br>3 - out of memory<br>4 - wrong device specified<br>5 - device already exists or device is busy<br>
<ul><li>Remove some obsolete info from man page.<br>
</li><li>Add more regression tests for commands.<br>
</li><li>Fix possible double free when handling master key file.<br>
</li><li>Fix pkg-config use in automake scripts.<br>
</li><li>Wipe iteration and salt after luksKillSlot in LUKS header.<br>
</li><li>Rewrite file differ test to C (and fix it to really work).<br>
</li><li>Do not query non-existent device twice (cryptsetup status /dev/nonexistent).<br>
</li><li>Check if requested hash is supported before writing LUKS header.<br>
</li><li>Fix problems reported by clang scan-build.
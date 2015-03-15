### cryptsetup 1.4.3 ###
Released on 2012-05-31.

The stable cryptsetup 1.4.3 release is available at

> http://code.google.com/p/cryptsetup/

> Download [cryptsetup-1.4.3.tar.bz2](http://cryptsetup.googlecode.com/files/cryptsetup-1.4.3.tar.bz2)

Feedback and bug reports are welcomed. You can use  [New issue](http://code.google.com/p/cryptsetup/issues/entry) report page.

## Cryptsetup 1.4.3 Release Notes ##

### Changes since version 1.4.2 ###

Cryptsetup 1.4.3 Release Notes


Changes since version 1.4.2

  * Man page rewrite and fixes by Arno Wagner.

  * Fix readonly activation if underlying device is readonly (1.4.0).

  * Fix loop mapping on readonly file.

  * Include stddef.h in libdevmapper.h (size\_t definition).

  * Fix keyslot removal for device with 4k hw block (1.4.0).<br>(Wipe keyslot failed in this case.)</li></ul>

<ul><li>Relax <code>--shared</code> flag to allow mapping even for overlapping segments.<br>The --shared flag (and API CRYPT_ACTIVATE_SHARED flag) is now able<br>to map arbitrary overlapping area.<br>From API it is even usable for LUKS devices.<br>It is user responsibility to not cause data corruption though.<br>This allows e.g. scubed to work again and also allows some tricky extensions later.</li></ul>

<ul><li>Allow empty cipher (cipher_null) for testing.<br>You can now use "null" (or directly cipher_null-ecb) in cryptsetup.<br>This means no encryption, useful for performance tests (measure dm-crypt layer overhead).</li></ul>

<ul><li>Switch on retry on device remove for libdevmapper.<br>Device-mapper now retry removal if device is busy.</li></ul>

<ul><li>Allow "private" activation (skip some udev global rules) flag.<br>Cryptsetup library API now allows to specify CRYPT_ACTIVATE_PRIVATE,<br>which means that some udev rules are not processed.<br>(Used for temporary devices, like internal keyslot mappings,<br>where it is not desirable to run any device scans.)</li></ul>

<ul><li>This release also includes some Red Hat/Fedora specific extensions<br>related to FIPS140-2 compliance.</li></ul>

<blockquote>In fact, all these patches are more formal changes and are just<br>subset of building blocks for FIPS certification.<br>See <a href='FrequentlyAskedQuestions.md'>FAQ</a> for more details about FIPS.</blockquote>

<blockquote>FIPS extensions are enabled by using --enable-fips configure switch.</blockquote>

<blockquote>In FIPS mode (kernel booted with fips=1 and gcrypt in FIPS mode).<br>
<ul><li>It provides library and binary integrity verification using libfipscheck<br>(requires pre-generated checksums).<br>
</li><li>It uses FIPS approved RNG for encryption key and salt generation<br>(note that using /dev/random is not formally FIPS compliant RNG).<br>
</li><li>Only gcrypt crypto backend is currently supported in FIPS mode.</li></ul></blockquote>

<blockquote>The FIPS RNG requirement for salt comes from NIST SP 800-132 recommendation.<br><a href='http://csrc.nist.gov/publications/nistpubs/800-132/nist-sp800-132.pdf'>Recommendation for Password-Based Key Derivation. Part 1: Storage Applications.</a><br>LUKS should be aligned to this recommendation otherwise.
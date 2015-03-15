Released on 2007/05/06

Changes:

  * From this release onwards, cryptsetup-luks becomes cryptsetup. Hence, we are replacing the original main branch (with it's only 0.1 release in 2004).
  * Fix segfault for >32 bytes keys
  * Allow hashing of keys passed through stdin via --key-file=-
  * Remove ancient header version conversion.
  * No password retry for I/O errors.
  * Fix hang on -i 0.
  * Fix password retrying.
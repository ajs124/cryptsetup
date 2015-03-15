# LUKS on-disk-format #

The LUKS on-disk-specification is available here:

[LUKS-on-disk-format.pdf](http://wiki.cryptsetup.googlecode.com/git/LUKS-standard/on-disk-format.pdf)


It standardizes the on-disk format of a LUKS partition, as well as the semantic use of all LUKS specific objects.

# Reference implementation #

cryptsetup-luks is the reference implementation for the specification. Your implementation should be able to detect and open a cryptsetup-luks generated partition, if the underlaying cipher system supports the hashes and ciphers given in the partition header.
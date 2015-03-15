Released on 2006/10/13.

Changes:

  * lib/setup.c: Added terminal timeout rewrite as forwarded by Jonas Meurer
  * Merged patch from Marc Merlin to allow user selection of key slot.
  * lib/setup.c (get\_key): Applied patch from David Härdeman for reading binary keys from stdin using the "-" as key file.
  * Applied patches from David Härdeman to fix 64 bit compiler warning issues.
  * src/cryptsetup.c (yesDialog): Fix getline problem for 64-bit archs.
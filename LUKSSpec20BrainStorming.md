# History #

The LUKS 1.0 on disk format specification was released on 2005/01/22. Several ideas have popped up how to improve it. This page should serve as repository of ideas to consider for the next major release.

# Meta-Data redundancy #

Make the LUKS header redundant by putting a copy at the end of the partition too.
Introduce luksResize that moves that header.
Introduce luksRepair that repairs either the front-header from the back or vice versa.
Introduce a switch to all LUKS commands to explicitly utilize one of these two headers.

Introduce size of the bulk data

TODO: Do the math what this means for effective key revocation.

### Please feel free to add your ideas and comments below. ###

### If you have a question, please check [FAQ](FrequentlyAskedQuestions.md) or use [dm-crypt@saout.de](mailto:dm-crypt@saout.de) mailing list. ###
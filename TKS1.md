**TKS1 - An anti-forensic, two level, and iterated key setup scheme**

This paper sketches the problems connected with usual hard disk encryption setups. It introduces the reader to PBKDF2, a password based key derive function, which provides better resistance against brute force attacks based on entropy weak user passwords. It proposes to use a two level hierarchy of cryptographic keys to provide the ability to change passwords and drafts solutions to the key storage problem arising when using two levels of cryptography due to the fact, that given the abilities of recent forensic data recovery methods, data can't be destroyed on magnetic storage media reliably.

See [TKS1 specification](http://wiki.cryptsetup.googlecode.com/git/TKS1-draft.pdf).

Also see http://clemens.endorphin.org/cryptography
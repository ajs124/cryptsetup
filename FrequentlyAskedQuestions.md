# Sections #
[1. General Questions](FrequentlyAskedQuestions#1._General_Questions.md)<br>
<a href='FrequentlyAskedQuestions#2._Setup.md'>2. Setup</a><br>
<a href='FrequentlyAskedQuestions#3._Common_Problems.md'>3. Common Problems</a><br>
<a href='FrequentlyAskedQuestions#4._Troubleshooting.md'>4. Troubleshooting</a><br>
<a href='FrequentlyAskedQuestions#5._Security_Aspects.md'>5. Security Aspects</a><br>
<a href='FrequentlyAskedQuestions#6._Backup_and_Data_Recovery.md'>6. Backup and Data Recovery</a><br>
<a href='FrequentlyAskedQuestions#7._Interoperability_with_other_Disk_Encryption_Tools.md'>7. Interoperability with other Disk Encryption Tools</a><br>
<a href='FrequentlyAskedQuestions#8._Issues_with_Specific_Versions_of_cryptsetup.md'>8. Issues with Specific Versions of cryptsetup</a><br>
<a href='FrequentlyAskedQuestions#9._References_and_Further_Reading.md'>9. References and Further Reading</a><br>
<a href='FrequentlyAskedQuestions#A._Contributors.md'>A. Contributors</a><br>

<h1>1. General Questions</h1>

<ul><li><b>1.1 What is this?</b></li></ul>

<blockquote>This is the FAQ (Frequently Asked Questions) for cryptsetup. It covers<br>
Linux disk encryption with plain dm-crypt (one passphrase, no management,<br>
no metadata on disk) and LUKS (multiple user keys with one master key,<br>
anti-forensic features, metadata block at start of device, ...).  The<br>
latest version of this FAQ should usually be available at<br>
<a href='http://code.google.com/p/cryptsetup/wiki/FrequentlyAskedQuestions'>http://code.google.com/p/cryptsetup/wiki/FrequentlyAskedQuestions</a></blockquote>


<ul><li><b>1.2 WARNINGS</b></li></ul>

<blockquote>ATTENTION: If you are going to read just one thing,<br>
make it the section on Backup and Data Recovery.<br>
By far the most questions on the cryptsetup mailing<br>
list are from people that managed to damage the<br>
start of their LUKS partitions, i.e. the LUKS header.<br>
In most cases, there is nothing that can be<br>
done to help these poor souls recover their data.<br>
Make sure you understand the problem and limitations<br>
imposed by the LUKS security model BEFORE you face<br>
such a disaster! In particular, make sure you have<br>
a current header backup before doing any potentially<br>
dangerous operations.</blockquote>

<blockquote>DEBUG COMMANDS: While the --debug option does not leak<br>
data, "strace" and the like can leak your full passphrase.<br>
Do not post an strace output with the correct passphrase<br>
to a mailing-list or online! See Item 4.5 for more explanation.</blockquote>

<blockquote>SSDs/FLASH DRIVES: SSDs and Flash are different. Currently<br>
it is unclear how to get LUKS or plain dm-crypt to run<br>
on them with the full set of security features intact.<br>
This may or may not be a problem, depending on the<br>
attacker model. See Section 5.19.</blockquote>

<blockquote>BACKUP: Yes, encrypted disks die, just as normal ones do.<br>
A full backup is mandatory, see Section "6. Backup and<br>
Data Recovery" on options for doing encrypted backup.</blockquote>

<blockquote>CLONING/IMAGING: If you clone or image a LUKS container,<br>
you make a copy of the LUKS header and the master key<br>
will stay the same! That means that if<br>
you distribute an image to several machines, the same<br>
master key will be used on all of them, regardless of<br>
whether you change the passphrases. Do NOT do this!<br>
If you do, a root-user on any of the machines with a<br>
mapped (decrypted) container or a passphrase on that<br>
machine can decrypt all other copies, breaking security.<br>
See also Item 6.15.</blockquote>

<blockquote>DISTRIBUTION INSTALLERS: Some distribution installers<br>
offer to create LUKS containers in a way that can<br>
be mistaken as activation of an existing container.<br>
Creating a new LUKS container on top of an existing<br>
one leads to permanent, complete and irreversible data<br>
loss. It is strongly recommended to only use distribution<br>
installers after a complete backup of all LUKS containers<br>
has been made.</blockquote>

<blockquote>UBUNTU INSTALLER: In particular the Ubuntu installer<br>
seems to be quite willing to kill LUKS containers<br>
in several different ways. Those responsible at Ubuntu<br>
seem not to care very much (it is very easy to recognize<br>
a LUKS container), so treat the process of installing<br>
Ubuntu as a severe hazard to any LUKS container you may<br>
have.</blockquote>

<blockquote>NO WARNING ON NON-INTERACTIVE FORMAT: If you feed cryptsetup<br>
from STDIN (e.g. via GnuPG) on LUKS format, it does not<br>
give you the warning that you are about to format (and e.g.<br>
will lose any pre-existing LUKS container on the target),<br>
as it assumes it is used from a script.<br>
In this scenario, the responsibility for warning the user<br>
and possibly checking for an existing LUKS header is shifted<br>
to the script. This is a more general form of the<br>
previous item.</blockquote>

<blockquote>LUKS PASSPHRASE IS NOT THE MASTER KEY: The LUKS<br>
passphrase is not used in deriving the master key. It is<br>
used in decrypting a master key that is randomly<br>
selected on header creation. This means that if you<br>
create a new LUKS header on top of an old one with<br>
exactly the same parameters and exactly the same<br>
passphrase as the old one, it will still have a<br>
different master key and your data will be permanently<br>
lost.</blockquote>

<blockquote>PASSPHRASE CHARACTER SET: Some people have had<br>
difficulties with this when upgrading distributions.<br>
It is highly advisable<br>
to only use the 95 printable characters from the<br>
first 128 characters of the ASCII table, as<br>
they will always have the same binary representation.<br>
Other characters may have different<br>
encoding depending on system configuration and your<br>
passphrase will not work with a different encoding.<br>
A table of the standardized first 128 ASCII characters<br>
can, e.g. be found on <a href='http://en.wikipedia.org/wiki/ASCII'>http://en.wikipedia.org/wiki/ASCII</a></blockquote>


<ul><li><b>1.3 System specific warnings</b></li></ul>

<blockquote>- Ubuntu as of 4/2011: It seems the installer offers to<br>
create LUKS partitions in a way that several people<br>
mistook for an offer to activate their existing LUKS<br>
partition. The installer gives no or an inadequate warning<br>
and will destroy your old LUKS header, causing permanent<br>
data loss. See also the section on Backup and Data Recovery.</blockquote>

<blockquote>This issue has been acknowledged by the Ubuntu dev team,<br>
see here: <a href='http://launchpad.net/bugs/420080'>http://launchpad.net/bugs/420080</a></blockquote>

<blockquote>Update 4/2013: I am still unsure whether this has been fixed<br>
by now, best be careful. They also seem to have added even<br>
more LUKS killer functionality to the Ubuntu installer. I can<br>
only strongly recommended to not install Ubuntu on a system<br>
with existing LUKS containers without complete backups.</blockquote>

<blockquote>Update 11/2014: There seem to be other problems withe existing LUKS<br>
containers and Ubuntu as well, be extra careful when using LUKS<br>
on Ubuntu in any way, but exactly as the Ubuntu installer does.</blockquote>

<ul><li><b>1.4 My LUKS-device is broken! Help!</b></li></ul>

<blockquote>First: Do not panic! In many cases the data is still recoverable.<br>
Do not do anything hasty! Steps:</blockquote>

<blockquote>- Take some deep breaths. Maybe add some relaxing music.<br>
This may sound funny, but I am completely serious.<br>
Often, critical damage is done only after the initial<br>
problem.</blockquote>

<blockquote>- Do not reboot. The keys may still be in the kernel if<br>
the device is mapped.</blockquote>

<blockquote>- Make sure others do not reboot the system.</blockquote>

<blockquote>- Do not write to your disk without a clear understanding<br>
why this will not make matters worse. Do a sector-level<br>
backup before any writes. Often you do not need to write<br>
at all to get enough access to make a backup of the data.</blockquote>

<blockquote>- Relax some more.</blockquote>

<blockquote>- Read section 6 of this FAQ.</blockquote>

<blockquote>- Ask on the mailing-list if you need more help.</blockquote>


<ul><li><b>1.5 Who wrote this?</b></li></ul>

<blockquote>Current FAQ maintainer is Arno Wagner <arno@wagner.name>. If you want to<br>
send me encrypted email, my current PGP key is DSA key CB5D9718,<br>
fingerprint 12D6 C03B 1B30 33BB 13CF  B774 E35C 5FA1 CB5D 9718.</blockquote>

<blockquote>Other contributors are listed at the end. If you want to contribute, send<br>
your article, including a descriptive headline, to the maintainer, or<br>
the dm-crypt mailing list with something like "FAQ ..." in the subject.<br>
You can also send more raw information and have me write the<br>
section. Please note that by contributing to this FAQ, you accept<br>
the license described below.</blockquote>

<blockquote>This work is<br>
under the "Attribution-Share Alike 3.0 Unported" license, which means<br>
distribution is unlimited, you may<br>
create derived works, but attributions to original authors and this<br>
license statement must be retained and the derived work must be under<br>
the same license.<br>
See <a href='http://creativecommons.org/licenses/by-sa/3.0/'>http://creativecommons.org/licenses/by-sa/3.0/</a> for more details<br>
of the license.</blockquote>

<blockquote>Side note: I did text license research some time ago and I think<br>
this license is best suited for the purpose at hand and creates<br>
the least problems.</blockquote>


<ul><li><b>1.6 Where is the project website?</b></li></ul>

<blockquote>There is the project website at <a href='http://code.google.com/p/cryptsetup/'>http://code.google.com/p/cryptsetup/</a>
Please do not post questions there, nobody will read them.  Use<br>
the mailing-list instead.</blockquote>


<ul><li><b>1.7 Is there a mailing-list?</b></li></ul>

<blockquote>Instructions on how to subscribe to the mailing-list are at<br>
on the project website. People are generally helpful<br>
and friendly on the list.</blockquote>

<blockquote>The question of how to unsubscribe from the list does crop up<br>
sometimes. For this you need your list management URL, which<br>
is sent to you initially and once at the start of each month.<br>
Go to the URL mentioned in the email and select "unsubscribe".<br>
This page also allows you to request a password reminder.</blockquote>

<blockquote>Alternatively, you can send an Email to dm-crypt-request@saout.de<br>
with just the word "help" in the subject or message body. Make<br>
sure to send it from your list address.</blockquote>

<blockquote>The mailing list archive is here:<br>
<a href='http://dir.gmane.org/gmane.linux.kernel.device-mapper.dm-crypt'>http://dir.gmane.org/gmane.linux.kernel.device-mapper.dm-crypt</a></blockquote>

<ul><li><b>1.8 Unsubscribe from the mailing-list</b></li></ul>

<blockquote>Send mail to dm-crypt-unsubscribe@saout.de from the subscribed<br>
account. You will get an email with instructions.</blockquote>

<blockquote>Basically, you just have to respond to it unmodified to get<br>
unsubscribed. The listserver admin functions are not very fast.<br>
It can take 15 minutes or longer for a reply to arrive (I suspect<br>
greylisting is in use), so be patient.</blockquote>

<blockquote>Also note that nobody on the list can unsubscribe you, sending<br>
demands to be unsubscribed to the list just annoys people that<br>
are entirely blameless for you being subscribed.</blockquote>

<blockquote>If you are subscribed, a subscription confirmation email<br>
was sent to your email account and it had to be answered<br>
before the subscription went active. The confirmation emails<br>
from the listserver have subjects like these (with other numbers):<br>
<pre><code>  Subject: confirm 9964cf10.....<br>
</code></pre>
and are sent from dm-crypt-request@saout.de. You should check<br>
whether you have anything like it in your sent email folder.<br>
If you find nothing and are sure you did not confirm, then you<br>
should look into a possible compromise of your email account.</blockquote>


<h1>2. Setup</h1>

<ul><li><b>2.1 LUKS Container Setup mini-HOWTO</b></li></ul>

<blockquote>This item tries to give you a very brief list of all the steps<br>
you should go though when creating a new LUKS encrypted container,<br>
i.e. encrypted disk, partition or loop-file.</blockquote>

<blockquote>01) All data will be lost, if there is data on the target, make a<br>
backup.</blockquote>

<blockquote>02) Make very sure you have the right target disk, partition or<br>
loop-file.</blockquote>

<blockquote>03) If the target was in use previously, it is a good idea to<br>
wipe it before creating the LUKS container in order to<br>
remove any trace of old file systems and data. For example, some<br>
users have managed to run e2fsck on a partition containing<br>
a LUKS container, possibly because of residual ext2<br>
superblocks from an earlier use. This can do arbitrary damage<br>
up to complete and permanent loss of all data in the LUKS<br>
container.</blockquote>

<blockquote>To just quickly wipe file systems (old data may remain), use<br>
<pre><code>     wipefs -a &lt;target device&gt;<br>
</code></pre></blockquote>

<blockquote>To wipe file system and data, use something like<br>
<pre><code>     cat /dev/zero &gt; &lt;target device&gt;<br>
</code></pre></blockquote>

<blockquote>This can take a while. To get a progress indicator, you can<br>
use the tool dd_rescue (->google) instead or use my stream<br>
meter "wcs" (source here: <a href='http://www.tansi.org/tools/index.html'>http://www.tansi.org/tools/index.html</a>)<br>
in the following fashion:<br>
<pre><code>     cat /dev/zero | wcs &gt; &lt;target device&gt;<br>
</code></pre></blockquote>

<blockquote>Be very sure you have the right target, all data will be lost!</blockquote>

<blockquote>Note that automatic wiping is on the TODO list for cryptsetup,<br>
so at some time in the future this will become unnecessary.</blockquote>

<blockquote>Alternatively, plain dm-crypt can be used for a very fast<br>
wipe with crypto-grade randomness, see Item 2.19</blockquote>

<blockquote>04) Create the LUKS container:<br>
<pre><code>     cryptsetup luksFormat &lt;target device&gt;<br>
</code></pre></blockquote>

<blockquote>Just follow the on-screen instructions.</blockquote>

<blockquote>Note: Passphrase iteration is determined by cryptsetup depending<br>
on CPU power. On a slow device, this may be lower than you want.<br>
I recently benchmarked this on a Raspberry Pi and<br>
it came out at about 1/15 of the iteration count for a typical PC.<br>
If security is paramount, you may want to increase the time spent<br>
in iteration, at the cost of a slower unlock later. For the<br>
Raspberry Pi, using<br>
<pre><code>   cryptsetup luksFormat -i 15000 &lt;target device&gt;<br>
</code></pre>
gives you an iteration count and security level equal to an average<br>
PC for passphrase iteration and master-key iteration. If in doubt,<br>
check the iteration counts with<br>
<pre><code>   cryptsetup luksDump &lt;target device&gt;<br>
</code></pre>
and adjust the iteration count accordingly by creating the container<br>
again with a different iteration time (the number after '-i' is the<br>
iteration time in milicesonds) until your requirements are met.</blockquote>

<blockquote>05) Map the container. Here it will be mapped to /dev/mapper/c1:<br>
<pre><code>     cryptsetup luksOpen &lt;target device&gt; c1 <br>
</code></pre></blockquote>

<blockquote>06) (Optionally) wipe the container (make sure you have the right target!):<br>
<pre><code>     cat /dev/zero &gt; /dev/mapper/c1<br>
</code></pre></blockquote>

<blockquote>Note that this creates a small information leak, as an attacker<br>
can determine whether a 512 byte block is zero if the attacker has<br>
access to the encrypted container multiple times. Typically<br>
a competent attacker that has access multiple times can<br>
install a passphrase sniffer anyways, so this leakage is not very<br>
significant. For getting a progress indicator, see step 03.</blockquote>

<blockquote>Note that at some time in the future, cryptsetup will do this<br>
for you, but currently it is a TODO list item.</blockquote>

<blockquote>07) Create a file system in the mapped container, for example an<br>
ext3 file system (any other file system is possible):<br>
<pre><code>     mke2fs -j /dev/mapper/c1<br>
</code></pre></blockquote>

<blockquote>08) Mount your encrypted file system, here on /mnt:<br>
<pre><code>     mount /dev/mapper/c1 /mnt <br>
</code></pre></blockquote>

<blockquote>Done. You can now use the encrypted file system to store data.<br>
Be sure to read though the rest of the FAQ, these are just the<br>
very basics. In particular, there are a number of mistakes that<br>
are easy to make, but will compromise your security.</blockquote>


<ul><li><b>2.2 LUKS on partitions or raw disks?</b></li></ul>

<blockquote>This is a complicated question, and made more so by the availability<br>
of RAID and LVM. I will try to give some scenarios and discuss<br>
advantages and disadvantages. Note that I say LUKS for simplicity,<br>
but you can do all the things described with plain dm-crypt as well.<br>
Also note that your specific scenario may be so special that most<br>
or even all things I say below do not apply.</blockquote>

<blockquote>Be aware that if you add LVM into the mix, things can get very<br>
complicated. Same with RAID but less so. In particular, data<br>
recovery can get exceedingly difficult. Only do so if you<br>
have a really good reason and always remember KISS is what<br>
separates an engineer from an amateur. Of course, if you really<br>
need the added complexity, KISS is satisfied. But be very sure<br>
as there is a price to pay for it. In engineering, complexity<br>
is always the enemy and needs to be fought without mercy when<br>
encountered.</blockquote>

<blockquote>Also consider using RAID instead of LVM, as at least with<br>
the old superblock format 0.90, the RAID superblock is in the<br>
place (end of disk) where the risk of it permanently<br>
damaging the LUKS header is smallest and you can have your<br>
array assembled by the RAID controller (i.e. the kernel),<br>
as it should be. Use partition type 0xfd for that. I recommend<br>
staying away from superblock formats 1.0, 1.1 and 1.2 unless you<br>
really need them. Be aware that you lose autodetection with them<br>
and have to fall back to some user-space script to do it.</blockquote>

<blockquote>Scenarios:</blockquote>

<blockquote>(1) Encrypted partition: Just make a partition to your liking,<br>
and put LUKS on top of it and a filesystem into the LUKS<br>
container. This gives you isolation of<br>
differently-tasked data areas, just as ordinary partitioning<br>
does. You can have confidential data, non-confidential data,<br>
data for some specific applications, user-homes, root, etc.<br>
Advantages are simplicity as there is a 1:1 mapping between<br>
partitions and filesystems, clear security functionality and<br>
the ability to separate data into different, independent (!)<br>
containers.</blockquote>

<blockquote>Note that you cannot do this for encrypted root, that requires an<br>
initrd. On the other hand, an initrd is about as vulnerable to<br>
a competent attacker as a non-encrypted root, so there really<br>
is no security advantage to doing it that way. An attacker that<br>
wants to compromise your system will just compromise the initrd<br>
or the kernel itself. The better way to deal with this is to<br>
make sure the root partition does not store any critical data and<br>
move that to additional encrypted partitions. If you really<br>
are concerned your root partition may be sabotaged by somebody<br>
with physical access (that would however strangely not, say,<br>
sabotage your BIOS, keyboard, etc.), protect it in some other way.<br>
The PC is just not set-up for a really secure boot-chain (whatever<br>
some people may claim).</blockquote>

<blockquote>(2) Fully encrypted raw block device: For this, put LUKS on the<br>
raw device (e.g. /dev/sdb) and put a filesystem into the<br>
LUKS container, no partitioning whatsoever involved. This is<br>
very suitable for things like external USB disks used for<br>
backups or offline data-storage.</blockquote>

<blockquote>(3) Encrypted RAID: Create your RAID from partitions and/or full<br>
devices. Put LUKS on top of the RAID device, just if it were<br>
an ordinary block device. Applications are just the same as<br>
above, but you get redundancy. (Side note as many people<br>
seem to be unaware of it: You can do RAID1 with an arbitrary<br>
number of components in Linux.) See also Item 2.8.</blockquote>

<blockquote>(4) Now, some people advocate doing the encryption below the RAID<br>
layer. That has several serious problems. One is that suddenly<br>
debugging RAID issues becomes much harder. You cannot do<br>
automatic RAID assembly anymore. You need to keep the<br>
encryption keys for the components in sync or manage them<br>
somehow. The only possible advantage is that things may run<br>
a little faster as more CPUs do the encryption, but if speed<br>
is a priority over security and simplicity, you are doing<br>
this wrong anyways. A good way to mitigate a speed issue<br>
is to get a CPU that does hardware AES.</blockquote>


<ul><li><b>2.3 How do I set up encrypted swap?</b></li></ul>

<blockquote>As things that are confidential can end up in swap (keys,<br>
passphrases, etc. are usually protected against being<br>
swapped to disk, but other things may not be), it may be<br>
advisable to do something about the issue.<br>
One option is to run without swap, which generally works well<br>
in a desktop-context. It may cause problems<br>
in a server-setting or under special circumstances. The solution<br>
to that is to encrypt swap with a random key at boot-time.</blockquote>

<blockquote>NOTE: This is for Debian, and should work for Debian-derived<br>
distributions. For others you may have to write your own startup<br>
script or use other mechanisms.</blockquote>

<blockquote>01) Add the swap partition to /etc/crypttab. A line like the following<br>
should do it:<br>
<pre><code>      swap  /dev/&lt;partition&gt;  /dev/urandom   swap,noearly<br>
</code></pre></blockquote>

<blockquote>Warning: While Debian refuses to overwrite partitions with a<br>
filesystem or RAID signature on it, if your disk IDs may<br>
change (adding or removing disks, failure of disk during boot, etc.),<br>
you may want to take additional precautions. Yes, this means that<br>
your kernel device names like sda, sdb, ... can change between reboots!<br>
This is not a concern if you have only one disk.<br>
One possibility is to make sure the partition number is not<br>
present on additional disks or also swap there. Another is to<br>
encapsulate the swap partition (by making it a 1-disk RAID1 or<br>
by using LVM), so that it gets a persistent identifier. Specifying<br>
it directly by UUID does not work, unfortunately, as the<br>
UUID is part of the swap signature and that is not visible from the<br>
outside due to the encryption and in addition changes on each<br>
reboot with this setup.</blockquote>

<blockquote>Note: Use /dev/random if you are paranoid or in a potential low-entropy<br>
situation (embedded system, etc.). This may cause the operation to<br>
take a long time during boot. If you are in a "no entropy" situation,<br>
you cannot encrypt swap securely. In this situation you should find<br>
some entropy, also because nothing else using crypto will be secure,<br>
like ssh, ssl or GnuPG.</blockquote>

<blockquote>Note: The "noearly" option makes sure things like LVM, RAID, etc.<br>
are running. As swap is non-critical for boot, it is fine to<br>
start it late.</blockquote>

<blockquote>02) Add the swap partition to /etc/fstab. A line like the following<br>
should do it:<br>
<pre><code>      /dev/mapper/swap none swap sw 0 0<br>
</code></pre></blockquote>

<blockquote>That is it. Reboot or start it manually to activate encrypted swap.<br>
Manual start would look like this:<br>
<pre><code>      /etc/init.d/crypdisks start<br>
      swapon /dev/mapper/swap<br>
</code></pre></blockquote>


<ul><li><b>2.4 What is the difference between "plain" and LUKS format?</b></li></ul>

<blockquote>First, unless you happen to understand the cryptographic<br>
background well, you should use LUKS. It does protect the<br>
user from a lot of common mistakes. Plain dm-crypt is<br>
for experts.</blockquote>

<blockquote>Plain format is just that: It has no metadata on disk,<br>
reads all parameters from the commandline (or the defaults),<br>
derives a master-key from the passphrase and then uses that<br>
to de-/encrypt the sectors of the device, with a direct 1:1<br>
mapping between encrypted and decrypted sectors.</blockquote>

<blockquote>Primary advantage is high resilience to damage, as one<br>
damaged encrypted sector results in exactly one damaged<br>
decrypted sector. Also, it is not readily apparent that<br>
there even is encrypted data on the device, as an overwrite<br>
with crypto-grade randomness (e.g. from /dev/urandom)<br>
looks exactly the same on disk.</blockquote>

<blockquote>Side-note: That has<br>
limited value against the authorities. In civilized<br>
countries, they cannot force you to give up a crypto-key<br>
anyways. In quite a few countries around the<br>
world, they can force you to give up the keys (using<br>
imprisonment or worse to pressure you, sometimes without<br>
due process), and in the worst case, they only<br>
need a nebulous "suspicion" about the presence of encrypted<br>
data. Sometimes this applies to everybody, sometimes<br>
only when you are suspected of having "illicit data" (definition<br>
subject to change) and sometimes specifically when crossing<br>
a border. Note that this is going on in countries like the<br>
US and the UK, to different degrees and sometimes with<br>
courts restricting what the authorities can actually demand.</blockquote>

<blockquote>My advice is to either be ready to give up the keys<br>
or to not have encrypted data when traveling to those<br>
countries, especially when crossing the borders. The latter also<br>
means not having any high-entropy (random) data areas on<br>
your disk, unless you can explain them and demonstrate that<br>
explanation. Hence doing a zero-wipe of all free space,<br>
including unused space, may be a good idea.</blockquote>

<blockquote>Disadvantages are that you do not have all the nice features<br>
that the LUKS metadata offers, like multiple passphrases<br>
that can be changed, the cipher being stored in the metadata,<br>
anti-forensic properties like key-slot diffusion and salts,<br>
etc..</blockquote>

<blockquote>LUKS format uses a metadata header and 8 key-slot areas<br>
that are being placed at the beginning of the disk,<br>
see below under "What does the LUKS on-disk format<br>
looks like?". The passphrases are used to decrypt a single<br>
master key that is stored in the anti-forensic stripes.</blockquote>

<blockquote>Advantages are a higher usability, automatic configuration<br>
of non-default crypto parameters, defenses against<br>
low-entropy passphrases like salting and iterated PBKDF2<br>
passphrase hashing, the ability to change passphrases,<br>
and others.</blockquote>

<blockquote>Disadvantages are that it is readily obvious there is encrypted<br>
data on disk (but see side note above) and that damage to<br>
the header or key-slots usually results in permanent data-loss.<br>
See below under "6. Backup and Data Recovery" on how to<br>
reduce that risk. Also the sector numbers get shifted<br>
by the length of the header and key-slots and there is a<br>
loss of that size in capacity (1MB+4096B for defaults and<br>
2MB for the most commonly used non-default XTS mode).</blockquote>


<ul><li><b>2.5 Can I encrypt an already existing, non-empty partition to use LUKS?</b></li></ul>

<blockquote>There is no converter, and it is not really needed. The way to do this<br>
is to make a backup of the device in question, securely wipe the device<br>
(as LUKS device initialization does not clear away old data),<br>
do a luksFormat, optionally overwrite the encrypted device, create<br>
a new filesystem and restore your backup on the now encrypted device.<br>
Also refer to sections "Security Aspects" and "Backup and Data Recovery".</blockquote>

<blockquote>For backup, plain GNU tar works well and backs up anything likely<br>
to be in a filesystem.</blockquote>


<ul><li><b>2.6 How do I use LUKS with a loop-device?</b></li></ul>

<blockquote>This can be very handy for experiments. Setup is just the same<br>
as with any block device. If you want, for example,<br>
to use a 100MiB file as LUKS container, do something like this:<br>
<pre><code>      head -c 100M /dev/zero &gt; luksfile  # create empty file<br>
      losetup /dev/loop0 luksfile        # map luksfile to /dev/loop0<br>
      cryptsetup luksFormat /dev/loop0   # create LUKS on loop device<br>
</code></pre>
Afterwards just use /dev/loop0 as a you would use a LUKS partition.<br>
To unmap the file when done, use "losetup -d /dev/loop0".</blockquote>


<ul><li><b>2.7 When I add a new key-slot to LUKS, it asks for a passphrase but then complains about there not being a key-slot with that passphrase?</b></li></ul>

<blockquote>That is as intended. You are asked a passphrase of an existing<br>
key-slot first, before you can enter the passphrase for the new<br>
key-slot. Otherwise you could break the encryption by just adding<br>
a new key-slot. This way, you have to know the passphrase of one<br>
of the already configured key-slots in order to be able to configure<br>
a new key-slot.</blockquote>


<ul><li><b>2.8 Encryption on top of RAID or the other way round?</b></li></ul>

<blockquote>Unless you have special needs, place encryption between<br>
RAID and filesystem, i.e. encryption on top of RAID.<br>
You can do it the other way round, but you have to be aware<br>
that you then need to give the passphrase for each individual<br>
disk and RAID autodetection will not work anymore.<br>
Therefore it is better to encrypt the RAID device,<br>
e.g. /dev/dm0 .</blockquote>

<blockquote>This means that the typical layering looks like this:<br>
<pre><code>  Filesystem     &lt;- top<br>
  |<br>
  Encryption<br>
  |<br>
  RAID<br>
  |<br>
  Raw partitions<br>
  |<br>
  Raw disks      &lt;- bottom<br>
</code></pre>
The big advantage is that you can manage the RAID container just like<br>
any RAID container, it does not care that what is in it is encrypted.</blockquote>


<ul><li><b>2.9 How do I read a dm-crypt key from file?</b></li></ul>

<blockquote>Use the --key-file option, like this:<br>
<pre><code>      cryptsetup create --key-file keyfile e1 /dev/loop0<br>
</code></pre>
This will read the binary key from file, i.e. no hashing or transformation<br>
will be applied to the keyfile before its bits are used as key.<br>
Extra bits (beyond the length of the key) at the end are ignored.<br>
Note that if you read from STDIN, the data will still be hashed,<br>
just as a key read interactively from the terminal. See the man-page<br>
sections "NOTES ON PASSPHRASE PROCESSING..." for more detail.</blockquote>

<ul><li><b>2.10 How do I read a LUKS slot key from file?</b></li></ul>

<blockquote>What you really do here is to read a passphrase from file, just as<br>
you would with manual entry of a passphrase for a key-slot. You can<br>
add a new passphrase to a free key-slot, set the passphrase of an<br>
specific key-slot or put an already configured passphrase into a file.<br>
In the last case make sure no trailing newline (0x0a) is contained in the<br>
key file, or the passphrase will not work because the whole file is<br>
used as input.</blockquote>

<blockquote>To add a new passphrase to a free key slot from file, use something<br>
like this:<br>
<pre><code>      cryptsetup luksAddKey /dev/loop0 keyfile<br>
</code></pre></blockquote>

<blockquote>To add a new passphrase to a specific key-slot, use something<br>
like this:<br>
<pre><code>      cryptsetup luksAddKey --key-slot 7 /dev/loop0 keyfile<br>
</code></pre></blockquote>

<blockquote>To supply a key from file to any LUKS command, use the --key-file<br>
option, e.g. like this:<br>
<pre><code>      cryptsetup luksOpen --key-file keyfile /dev/loop0 e1<br>
</code></pre></blockquote>


<ul><li><b>2.11 How do I read the LUKS master key from file?</b></li></ul>

<blockquote>The question you should ask yourself first is why you would want<br>
to do this. The only legitimate reason I can think of is if you<br>
want to have two LUKS devices with the same master key. Even then,<br>
I think it would be preferable to just use key-slots with the<br>
same passphrase, or to use plain dm-crypt instead. If you really<br>
have a good reason, please tell me. If I am convinced, I will add<br>
how to do this here.</blockquote>


<ul><li><b>2.12 What are the security requirements for a key read from file?</b></li></ul>

<blockquote>A file-stored key or passphrase has the same security requirements<br>
as one entered interactively, however you can use random bytes<br>
and thereby use bytes you cannot type on the keyboard. You can use<br>
any file you like as key file, for example a plain text file with<br>
a human readable passphrase. To generate a file with random bytes,<br>
use something like this:<br>
<pre><code>      head -c 256 /dev/random &gt; keyfile<br>
</code></pre></blockquote>


<ul><li><b>2.13 If I map a journaled file system using dm-crypt/LUKS, does it still provide its usual transactional guarantees?</b></li></ul>

<blockquote>Yes, it does, unless a very old kernel is used. The required<br>
flags come from the filesystem layer and are processed and<br>
passed onwards by dm-crypt. A bit more information on the process<br>
by which transactional guarantees are implemented can be found here:</blockquote>

<blockquote><a href='http://lwn.net/Articles/400541/'>http://lwn.net/Articles/400541/</a></blockquote>

<blockquote>Please note that these<br>
"guarantees" are weaker than they appear to be. One problem is that<br>
quite a few disks lie to the OS about having flushed their buffers.<br>
Some other things can go wrong as well. The filesystem developers<br>
are aware of these problems and typically can make it work anyways.<br>
That said, dm-crypt/LUKS will not make things worse.</blockquote>

<blockquote>One specific problem you can run into though is that you can get<br>
short freezes and other slowdowns due to the encryption layer.<br>
Encryption takes time and forced flushes will block for that time.<br>
For example, I did run into frequent small freezes (1-2 sec) when<br>
putting a vmware image on ext3 over dm-crypt. When I went back<br>
to ext2, the problem went away. This seems to have gotten<br>
better with kernel 2.6.36 and the reworking of filesystem flush<br>
locking mechanism (less blocking of CPU activity during flushes).<br>
It should improve further and eventually the problem should go<br>
away.</blockquote>


<ul><li><b>2.14 Can I use LUKS or cryptsetup with a more secure (external) medium for key storage, e.g. TPM or a smartcard?</b></li></ul>

<blockquote>Yes, see the answers on using a file-supplied key. You do have to write<br>
the glue-logic yourself though. Basically you can have cryptsetup read<br>
the key from STDIN and write it there with your own tool that in turn<br>
gets the key from the more secure key storage.</blockquote>

<blockquote>For TPM support, you may want to have a look at tpm-luks at<br>
<a href='https://github.com/shpedoikal/tpm-luks'>https://github.com/shpedoikal/tpm-luks</a>. Note that tpm-luks<br>
is not related to the cryptsetup project.</blockquote>


<ul><li><b>2.15 Can I resize a dm-crypt or LUKS partition?</b></li></ul>

<blockquote>Yes, you can, as neither dm-crypt nor LUKS stores partition size. Whether<br>
you should is a different question. Personally I recommend backup,<br>
recreation of the encrypted partition with new size, recreation of the<br>
filesystem and restore. This gets around the tricky business of resizing<br>
the filesystem. Resizing a dm-crypt or LUKS container does<br>
not resize the filesystem in it. The backup is really non-optional here, as a lot can go<br>
wrong, resulting in partial or complete data loss. Using something like<br>
gparted to resize an encrypted partition is slow, but typically<br>
works. This will not change the size of the filesystem hidden<br>
under the encryption though.</blockquote>

<blockquote>You also need to be aware of size-based limitations. The one currently<br>
relevant is that aes-xts-plain should not be used for encrypted container<br>
sizes larger than 2TiB. Use aes-xts-plain64 for that.</blockquote>

<ul><li><b>2.16 How do I Benchmark the Ciphers, Hashes and Modes?</b></li></ul>

<blockquote>Since version 1.60 cryptsetup supports the "benchmark" command.<br>
Simply run as root:<br>
<pre><code>   cryptsetup benchmark<br>
</code></pre>
It will output first iterations/second for the key-derivation function<br>
PBKDF2 parameterized with different hash-functions, and then the<br>
raw encryption speed of ciphers with different modes and key-sizes.<br>
You can get more than the default benchmarks, see the man-page for the<br>
relevant parameters. Note that XTS mode takes two keys, hence the<br>
listed key sizes are double that for other modes and half of it is the<br>
cipher key, the other half is the XTS key.</blockquote>

<ul><li><b>2.17 How do I Verify I have an Authentic cryptsetup Source Package?</b></li></ul>

<blockquote>Current maintainer is Milan Broz and he signs the release packages with<br>
his PGP key. The key he currently uses is the "RSA key ID D93E98FC",<br>
fingerprint  2A29 1824 3FDE 4664 8D06  86F9 D9B0 577B D93E 98FC.<br>
While I have every confidence this really is his key and that<br>
he is who he claims to be, don't depend on it if your life is<br>
at stake. For that matter, if your life is at stake, don't depend<br>
on me being who I claim to be either.</blockquote>

<blockquote>That said, as cryptsetup is under good version control, a malicious<br>
change should be noticed sooner or later, but it may take a while.<br>
Also, the attacker model makes compromising the sources in a<br>
non-obvious way pretty hard. Sure, you could put the master-key<br>
somewhere on disk, but that is rather obvious as soon as somebody<br>
looks as there would be data in an empty LUKS container in a place<br>
it should not be. Doing this in a more nefarious way, for<br>
example hiding the master-key in the salts, would need a look at the<br>
sources to be discovered, but I think that somebody would find that<br>
sooner or later as well.</blockquote>

<blockquote>That said, this discussion is really a lot more complicated and longer<br>
as an FAQ can sustain. If in doubt, ask on the mailing list.</blockquote>

<ul><li><b>2.18 Is there a concern with 4k Sectors?</b></li></ul>

<blockquote>Not from dm-crypt itself. Encryption will be done in 512B blocks,<br>
but if the partition and filesystem are aligned correctly and the<br>
filesystem uses multiples of 4kiB as block size, the<br>
dm-crypt layer will just process 8 x 512B = 4096B at a time with<br>
negligible overhead. LUKS does place data at an offset, which<br>
is 2MiB per default and will not break alignment. See also<br>
Item 6.12 of this FAQ for more details. Note that if your partition<br>
or filesystem is misaligned, dm-crypt can make the effect worse<br>
though.</blockquote>

<ul><li><b>2.19 How can I wipe a device with crypto-grade randomness?</b></li></ul>

<blockquote>The conventional recommendation if you want to not just do a<br>
zero-wipe is to use something like<br>
<pre><code>  cat /dev/urandom &gt;  &lt;taget-device&gt;<br>
</code></pre>
That is very slow and painful at 10-20MB/s on a fast computer.<br>
Using cryptsetup and a plain dm-crypt device with a random key,<br>
it is much faster and gives you the same level of security. The<br>
defaults are quite enough.</blockquote>

<blockquote>For device set-up, do the following:<br>
<pre><code>  cryptsetup open --type plain -d /dev/urandom /dev/&lt;block-device&gt;  to_be_wiped<br>
</code></pre>
Then you have several options. Simple wipe without progress-indicator:<br>
<pre><code>  cat /dev/zero &gt; /dev/mapper/to_be_wiped<br>
</code></pre>
Progress-indicator by dd_rescue:<br>
<pre><code>  dd_rescue -w /dev/zero /dev/mapper/to_be_wiped<br>
</code></pre>
Progress-indicator by my "wcs" stream meter (available<br>
from <a href='http://www.tansi.org/tools/index.html'>http://www.tansi.org/tools/index.html</a> ):<br>
<pre><code>  cat /dev/zero | wcs &gt; /dev/mapper/to_be_wiped<br>
</code></pre></blockquote>

<blockquote>Remove the mapping at the end and you are done.</blockquote>



<h1>3. Common Problems</h1>


<ul><li><b>3.1 My dm-crypt/LUKS mapping does not work! What general steps are there to investigate the problem?</b></li></ul>

<blockquote>If you get a specific error message, investigate what it claims first. If<br>
not, you may want to check the following things.</blockquote>

<blockquote>- Check that "/dev", including "/dev/mapper/control" is there. If it is<br>
missing, you may have a problem with the "/dev" tree itself or you may have<br>
broken udev rules.</blockquote>

<blockquote>- Check that you have the device mapper and the crypt target in your kernel.<br>
The output of "dmsetup targets" should list a "crypt" target. If it is not<br>
there or the command fails, add device mapper and crypt-target to the kernel.</blockquote>

<blockquote>- Check that the hash-functions and ciphers you want to use are in the kernel.<br>
The output of "cat /proc/crypto" needs to list them.</blockquote>


<ul><li><b>3.2 My dm-crypt mapping suddenly stopped when upgrading cryptsetup.</b></li></ul>

<blockquote>The default cipher, hash or mode may have changed (the mode changed<br>
from 1.0.x to 1.1.x). See under "Issues With Specific Versions of<br>
cryptsetup".</blockquote>


<ul><li><b>3.3 When I call cryptsetup from cron/CGI, I get errors about unknown features?</b></li></ul>

<blockquote>If you get errors about unknown parameters or the like that are not present<br>
when cryptsetup is called from the shell, make sure you have no older version<br>
of cryptsetup on your system that then gets called by cron/CGI. For example<br>
some distributions install cryptsetup into /usr/sbin, while a manual install<br>
could go to /usr/local/sbin. As a debugging aid, call "cryptsetup --version"<br>
from cron/CGI or the non-shell mechanism to be sure the right version gets called.</blockquote>


<ul><li><b>3.4 Unlocking a LUKS device takes very long. Why?</b></li></ul>

<blockquote>The iteration time for a key-slot (see Section 5 for an explanation<br>
what iteration does) is calculated when setting a passphrase. By<br>
default it is 1 second on the machine where the passphrase is set.<br>
If you set a passphrase on a fast machine and then unlock it on a<br>
slow machine, the unlocking time can be much longer. Also take into<br>
account that up to 8 key-slots have to be tried in order to find the<br>
right one.</blockquote>

<blockquote>If this is problem, you can add another key-slot using the slow machine<br>
with the same passphrase and then remove the old key-slot. The new key-slot<br>
will have an iteration count adjusted to 1 second on the slow machine.<br>
Use luksKeyAdd and then luksKillSlot or luksRemoveKey.</blockquote>

<blockquote>However, this operation will not change volume key iteration count<br>
(MK iterations in output of "cryptsetup luksDump").<br>
In order to change<br>
that, you will have to backup the data in the LUKS container<br>
(i.e. your encrypted data), luksFormat on the slow machine<br>
and restore the data. Note that in the original<br>
LUKS specification this value was fixed to 10, but it<br>
is now derived<br>
from the PBKDF2 benchmark as well and set to iterations<br>
in 0.125 sec<br>
or 1000, whichever is larger. Also note that MK<br>
iterations are not very security relevant. But as<br>
each key-slot already takes 1 second, spending the<br>
additional 0.125 seconds really does not matter.</blockquote>

<ul><li><b>3.5 "blkid" sees a LUKS UUID and an ext2/swap UUID on the same device. What is wrong?</b></li></ul>

<blockquote>Some old versions of cryptsetup have a bug where the<br>
header does not get completely wiped during LUKS format and an older<br>
ext2/swap signature remains on the device. This confuses blkid.</blockquote>

<blockquote>Fix: Wipe the unused header areas by doing a backup and restore of the<br>
header with cryptsetup 1.1.x:<br>
<pre><code>      cryptsetup luksHeaderBackup --header-backup-file &lt;file&gt; &lt;device&gt;<br>
      cryptsetup luksHeaderRestore --header-backup-file &lt;file&gt; &lt;device&gt;<br>
</code></pre></blockquote>

<ul><li><b>3.6 cryptsetup segfaults on Gentoo amd64 hardened ...</b></li></ul>

<blockquote>There seems to be some interference between the hardening and<br>
and the way cryptsetup benchmarks PBKDF2. The solution to this<br>
is currently not quite clear for an encrypted root filesystem.<br>
For other uses, you can apparently specify USE="dynamic" as<br>
compile flag, see<br>
<a href='http://bugs.gentoo.org/show_bug.cgi?id=283470'>http://bugs.gentoo.org/show_bug.cgi?id=283470</a></blockquote>

<h1>4. Troubleshooting</h1>

<ul><li><b>4.1 I get the error "LUKS keyslot x is invalid." What does that mean?</b></li></ul>

<blockquote>This means that the given keyslot has an offset that<br>
points outside the valid keyslot area. Typically, the reason<br>
is a corrupted LUKS header because something was written to<br>
the start of the device the LUKS container is on.<br>
Refer to Section "Backup and Data Recovery" and ask on the<br>
mailing list if you have trouble diagnosing and (if still<br>
possible) repairing this.</blockquote>

<ul><li><b>4.2 I cannot unlock my LUKS container! What could be the problem?</b></li></ul>

<blockquote>First, make sure you have a correct passphrase. Then make sure<br>
you have the correct key-map and correct keyboard. And then<br>
make sure you have the correct character set and encoding, see<br>
also "PASSPHRASE CHARACTER SET" under Section 1.2.</blockquote>

<blockquote>If you are sure you are entering the passphrase right, there is the<br>
possibility that the respective key-slot has been damaged. There<br>
is no way to recover a damaged key-slot, except from a header<br>
backup (see Section 6). For security reasons, there is also no<br>
checksum in the key-slots that could tell you whether a key-slot has<br>
been damaged. The only checksum present allows recognition of a<br>
correct passphrase, but that only works if the passphrase is<br>
correct and the respective key-slot is intact.</blockquote>

<blockquote>In order to find out whether a key-slot is damaged one has to look<br>
for "non-random looking" data in it. There is a tool that<br>
automatizes this in the cryptsetup distribution from version 1.6.0<br>
onwards. It is located in misc/keyslot_checker/. Instructions<br>
how to use and how to interpret results are in the README file.<br>
Note that this tool requires a libcryptsetup from<br>
cryptsetup 1.6.0 or later (which means libcryptsetup.so.4.5.0<br>
or later). If the tool complains about missing functions in<br>
libcryptsetup, you likely have an earlier version from your<br>
distribution still installed. You can either point the symbolic<br>
link(s) from  libcryptsetup.so.4 to the new version manually,<br>
or you can uninstall the distribution version of cryptsetup and<br>
re-install that from cryptsetup >= 1.6.0 again to fix this.</blockquote>


<ul><li><b>4.3 Can a bad RAM module cause problems?</b></li></ul>

<blockquote>LUKS and dm-crypt can give the RAM quite a workout, especially when combined<br>
with software RAID. In particular the combination RAID5 + LUKS + XFS seems<br>
to uncover RAM problems that never caused obvious problems before. Symptoms<br>
vary, but often the problem manifest itself when copying large amounts of<br>
data, typically several times larger than your main memory.</blockquote>

<blockquote>Side note: One thing you should always do on large data<br>
copy/movements is to<br>
run a verify, for example with the "-d" option of "tar" or by doing a<br>
set of MD5 checksums on the source or target with<br>
<pre><code>      find . -type f -exec md5sum \{\} \; &gt; checksum-file<br>
</code></pre>
and then a "md5sum -c checksum-file" on the other side. If you get mismatches<br>
here, RAM is the primary suspect. A lesser suspect is an overclocked CPU.<br>
I have found countless hardware problems in verify runs after copying or<br>
making backups. Bit errors are much more common than most people think.</blockquote>

<blockquote>Some RAM issues are even worse and corrupt structures in one of the<br>
layers. This typically results in lockups, CPU state dumps in the<br>
system<br>
logs, kernel panic or other things. It is quite possible to have the<br>
problem with an encrypted device, but not with an otherwise the same<br>
unencrypted device. The reason for that is that encryption has an<br>
error amplification property: You flip one bit in an encrypted data<br>
block, and the decrypted version has half of its bits flipped. This is<br>
an important security property for modern ciphers. With the usual<br>
modes in cryptsetup (CBC, ESSIV, XTS), you get up to a completely<br>
changed 512 byte block per bit error. A corrupt block causes a lot<br>
more havoc than the occasionally flipped single bit and can result<br>
in various obscure errors.</blockquote>

<blockquote>Note, that a verify run on copying between encrypted or<br>
unencrypted devices will reliably detect corruption, even<br>
when the copying<br>
itself did not report any problems. If you find defect RAM, assume<br>
all backups and copied data to be suspect, unless you did a verify.</blockquote>


<ul><li><b>4.4 How do I test RAM?</b></li></ul>

<blockquote>First you should know that overclocking often makes memory<br>
problems worse. So if you overclock (which I strongly<br>
recommend against in a system holding<br>
data that has some worth), run the tests with the<br>
overclocking active.</blockquote>

<blockquote>There are two good options. One is Memtest86+ and the other<br>
is "memtester"<br>
by Charles Cazabon. Memtest86+ requires a reboot and then<br>
takes over the<br>
machine, while memtester runs from a root-shell. Both use<br>
different<br>
testing methods and I have found problems fast with each one<br>
that the other<br>
needed long to find. I recommend running the following procedure<br>
until the first error is found:</blockquote>

<blockquote>- Run Memtest86+ for one cycle</blockquote>

<blockquote>- Run memtester for one cycle (shut down as many other applications as possible)</blockquote>

<blockquote>- Run Memtest86+ for 24h or more</blockquote>

<blockquote>- Run memtester for 24h or more</blockquote>

<blockquote>If all that does not produce error messages, your RAM may be sound, but<br>
I have had one weak bit that Memtest86+ needed around 60 hours to find.<br>
If you can reproduce the original problem reliably, a good additional<br>
test may be to remove half of the RAM (if you have more than one<br>
module) and try whether the problem is still there and if so, try with<br>
the other half. If you just have one module, get a different one and<br>
try with that. If you do overclocking, reduce the settings to the most<br>
conservative ones available and try with that.</blockquote>

<ul><li><b>4.5 Is there a risk using debugging tools like strace?</b></li></ul>

<blockquote>There most definitely is. An dump from strace and friends can<br>
contain all data entered, including the full passphrase. Example with<br>
strace and passphrase "test":<br>
<pre><code>      &gt; strace cryptsetup luksOpen /dev/sda10 c1<br>
      ...<br>
      read(6, "test\n", 512)                  = 5<br>
      ...<br>
</code></pre>
Depending on different factors and the tool used, the passphrase<br>
may also be encoded and not plainly visible.<br>
Hence it is never a good idea to give such a trace<br>
from a live container to anybody. Recreate the problem with<br>
a test container or set a temporary passphrase like "test"<br>
and use that for the trace generation. Item 2.6 explains how<br>
to create a loop-file backed LUKS container that may come in handy<br>
for this purpose.</blockquote>

<blockquote>See also Item 6.10 for another set of data you should not<br>
give to others.</blockquote>


<h1>5. Security Aspects</h1>

<ul><li><b>5.1 How long is a secure passphrase ?</b></li></ul>

<blockquote>This is just the short answer. For more info and explanation<br>
of some of the terms used in this item, read the rest of<br>
Section 5. The actual recommendation is at the end of this item.</blockquote>

<blockquote>First, passphrase length is not really the right measure,<br>
passphrase entropy is. For example, a random lowercase letter (a-z)<br>
gives you 4.7 bit of entropy, one element of a-z0-9 gives you 5.2<br>
bits of entropy, an element of a-zA-Z0-9 gives you 5.9 bits<br>
and a-zA-Z0-9!@#$%^&:-+ gives you 6.2 bits. On the other hand,<br>
a random English word only gives you 0.6...1.3 bits of entropy<br>
per character. Using sentences that make sense gives lower<br>
entropy, series of random words gives higher entropy. Do not<br>
use sentences that can be tied to you or found on your computer.<br>
This type of attack is done routinely today.</blockquote>

<blockquote>That said, it does not matter too much what scheme you use,<br>
but it does matter how much entropy your passphrase contains, because<br>
an attacker has to try on average<br>
<pre><code>      1/2 * 2^(bits of entropy in passphrase)    <br>
</code></pre>
different passphrases to guess correctly.</blockquote>

<blockquote>Historically, estimations tended to use computing time estimates,<br>
but more modern approaches try to estimate cost of guessing a passphrase.</blockquote>

<blockquote>As an example, I will try to get an estimate from<br>
the numbers in <a href='http://it.slashdot.org/story/12/12/05/0623215/new-25-gpu-monster-devours-strong-passwords-in-minutes'>http://it.slashdot.org/story/12/12/05/0623215/new-25-gpu-monster-devours-strong-passwords-in-minutes</a>
More references can be found a the end of this document. Note that<br>
these are estimates from the defender side, so assuming something<br>
is easier than it actually is is fine. An attacker may still<br>
have vastly higher cost than estimated here.</blockquote>

<blockquote>LUKS uses SHA1 for hashing per default. The claim in the reference is<br>
63 billion tries/second for SHA1. We will leave aside the check<br>
whether a try actually decrypts a key-slot. Now, the machine has<br>
25 GPUs, which I will estimate at an overall lifetime cost of<br>
USD/EUR 1000 each, and an useful lifetime of 2 years. (This is on<br>
the low side.) Disregarding downtime, the machine can then<br>
break<br>
<pre><code>     N = 63*10^9 * 3600 * 24 * 365 * 2 ~ 4*10^18     <br>
</code></pre>
passphrases for EUR/USD 25k. That is one 62 bit passphrase hashed once<br>
with SHA1 for EUR/USD 25k. Note that as this can be parallelized, it<br>
can be done faster than 2 years with several of these machines.</blockquote>

<blockquote>For plain dm-crypt (no hash iteration) this is it. This gives (with SHA1,<br>
plain dm-crypt default is ripemd160<br>
which seems to be slightly slower than SHA1):<br>
<pre><code>    Passphrase entropy  Cost to break  <br>
    60 bit              EUR/USD     6k  <br>
    65 bit              EUR/USD   200K<br>
    70 bit              EUR/USD     6M<br>
    75 bit              EUR/USD   200M<br>
    80 bit              EUR/USD     6B<br>
    85 bit              EUR/USD   200B<br>
    ...                      ...    <br>
</code></pre></blockquote>

<blockquote>For LUKS, you have to take into account hash iteration in PBKDF2.<br>
For a current CPU, there are about 100k iterations (as can be queried<br>
with ''cryptsetup luksDump''.</blockquote>

<blockquote>The table above then becomes:<br>
<pre><code>    Passphrase entropy  Cost to break <br>
    50 bit              EUR/USD   600k <br>
    55 bit              EUR/USD    20M<br>
    60 bit              EUR/USD   600M  <br>
    65 bit              EUR/USD    20B<br>
    70 bit              EUR/USD   600B<br>
    75 bit              EUR/USD    20T<br>
    ...                      ...    <br>
</code></pre></blockquote>

<blockquote>Recommendation:</blockquote>

<blockquote>To get reasonable security for the  next 10 years, it is a good idea<br>
to overestimate by a factor of at least 1000.</blockquote>

<blockquote>Then there is the question of how much the attacker is willing<br>
to spend. That is up to your own security evaluation.<br>
For general use, I will assume the attacker is willing to<br>
spend up to 1 million EUR/USD. Then we get the following<br>
recommendations:</blockquote>

<blockquote>Plain dm-crypt: Use > 80 bit. That is e.g. 17 random chars from a-z or<br>
a random English sentence of > 135 characters length.</blockquote>

<blockquote>LUKS: Use > 65 bit. That is e.g. 14 random chars from a-z or<br>
a random English sentence of > 108 characters length.</blockquote>

<blockquote>If paranoid, add at least 20 bit. That is roughly four additional<br>
characters for random passphrases and roughly 32 characters for<br>
a random English sentence.</blockquote>


<ul><li><b>5.2 Is LUKS insecure? Everybody can see I have encrypted data!</b></li></ul>

<blockquote>In practice it does not really matter. In most<br>
civilized countries you can just refuse to hand over the<br>
keys, no harm done. In some countries they can force<br>
you to hand over the keys, if they suspect encryption.<br>
However the suspicion is enough, they do not have to<br>
prove anything. This is for practical reasons, as even<br>
the presence of a header (like the LUKS header) is<br>
not enough to prove that you have any keys. It might<br>
have been an experiment, for example. Or it was<br>
used as encrypted swap with a key from /dev/random.<br>
So they make you prove you do not have encrypted<br>
data. Of course that is just as impossible as the<br>
other way round.</blockquote>

<blockquote>This means that if you have a large set of<br>
random-looking data, they can already lock you up.<br>
Hidden containers (encryption hidden within encryption),<br>
as possible with Truecrypt,<br>
do not help either. They will just assume the hidden<br>
container is there and unless you hand over the<br>
key, you will stay locked up. Don't have a hidden<br>
container? Though luck. Anybody could claim that.</blockquote>

<blockquote>Still, if you are concerned about the LUKS header,<br>
use plain dm-crypt with a good passphrase. See<br>
also Section 2, "What is the difference between<br>
"plain" and LUKS format?"</blockquote>


<ul><li><b>5.3 Should I initialize (overwrite) a new LUKS/dm-crypt partition?</b></li></ul>

<blockquote>If you just create a filesystem on it, most of the old data will<br>
still be there. If the old data is sensitive, you should overwrite it<br>
before encrypting. In any case, not initializing will leave the old<br>
data there until the specific sector gets written. That may enable<br>
an attacker to determine how much and where on the partition data was<br>
written. If you think this is a risk, you can prevent this by<br>
overwriting the encrypted device (here assumed to be named "e1")<br>
with zeros like this:<br>
<pre><code>      dd_rescue -w /dev/zero /dev/mapper/e1<br>
</code></pre>
or alternatively with one of the following more standard commands:<br>
<pre><code>      cat /dev/zero &gt; /dev/mapper/e1<br>
      dd if=/dev/zero of=/dev/mapper/e1<br>
</code></pre></blockquote>


<ul><li><b>5.4 How do I securely erase a LUKS (or other) partition?</b></li></ul>

<blockquote>For LUKS, if you are in a desperate hurry, overwrite<br>
the LUKS header and key-slot area. This means overwriting<br>
the first (keyslots x stripes x keysize) + offset bytes.<br>
For the default parameters, this is the 1'052'672 bytes,<br>
i.e. 1MiB + 4096 of the LUKS partition. For 512 bit<br>
key length (e.g. for aes-xts-plain with 512 bit key)<br>
this is 2MiB. (The different offset stems from differences in the<br>
sector alignment of the key-slots.) If in doubt, just be generous<br>
and overwrite the first 10MB or so, it will likely still<br>
be fast enough. A single overwrite with zeros should be enough.<br>
If you anticipate being in a desperate hurry, prepare the<br>
command beforehand. Example with /dev/sde1 as the LUKS<br>
partition and default parameters:<br>
<pre><code>      head -c 1052672 /dev/zero &gt; /dev/sde1; sync<br>
</code></pre>
A LUKS header backup or full backup will still grant<br>
access to most or all data, so make sure that an attacker does<br>
not have access to backups or destroy them as well.</blockquote>

<blockquote>If you have time, overwrite the whole LUKS partition with a<br>
single pass of zeros. This is enough for current HDDs. For<br>
SSDs or FLASH (USB sticks) you may want to overwrite the whole<br>
drive several times to be sure data is not retained by wear<br>
leveling. This is possibly still insecure as SSD technology is<br>
not fully understood in this regard. Still, due to the<br>
anti-forensic properties of the LUKS key-slots, a single<br>
overwrite of an SSD or FLASH drive could<br>
be enough. If in doubt, use physical destruction in addition.<br>
Here is a link to some current research results on erasing<br>
SSDs and FLASH drives:<br>
<a href='http://www.usenix.org/events/fast11/tech/full_papers/Wei.pdf'>http://www.usenix.org/events/fast11/tech/full_papers/Wei.pdf</a></blockquote>

<blockquote>Keep in mind to also erase all backups.</blockquote>

<blockquote>Example for a zero-overwrite erase of partition sde1 done<br>
with dd_rescue:<br>
<pre><code>      dd_rescue -w /dev/zero /dev/sde1   <br>
</code></pre></blockquote>


<ul><li><b>5.5 How do I securely erase a backup of a LUKS partition or header?</b></li></ul>

<blockquote>That depends on the medium it is stored on. For HDD and SSD, use<br>
overwrite with zeros. For an SSD or FLASH drive (USB stick), you<br>
may want to overwrite the<br>
complete SSD several times and use physical destruction in<br>
addition, see last item. For re-writable<br>
CD/DVD, a single overwrite should also be enough, due to the<br>
anti-forensic properties of the LUKS keyslots. For write-once<br>
media, use physical destruction. For low security requirements,<br>
just cut the CD/DVD into several parts. For high security needs,<br>
shred or burn the medium. If your backup is on magnetic tape, I<br>
advise physical destruction by shredding or burning, after<br>
overwriting . The problem with<br>
magnetic tape is that it has a higher dynamic range than HDDs and<br>
older data may well be recoverable after overwrites. Also write-head<br>
alignment issues can lead to data not actually being deleted at all<br>
during overwrites.</blockquote>


<ul><li><b>5.6 What about backup? Does it compromise security?</b></li></ul>

<blockquote>That depends. See item 6.7.</blockquote>


<ul><li><b>5.7 Why is all my data permanently gone if I overwrite the LUKS header?</b></li></ul>

<blockquote>Overwriting the LUKS header in part or in full is the most common<br>
reason why access to LUKS containers is lost permanently.<br>
Overwriting<br>
can be done in a number of fashions, like creating a new filesystem<br>
on the raw LUKS partition, making the raw partition part of<br>
a raid array and just writing to the raw partition.</blockquote>

<blockquote>The LUKS header contains a 256 bit "salt" per key-slot and without that<br>
no decryption is possible. While the salts are not secret, they<br>
are key-grade material and cannot be reconstructed. This is a<br>
cryptographically strong "cannot". From observations on the<br>
cryptsetup mailing-list, people typically go though the usual<br>
stages of grief (Denial, Anger, Bargaining, Depression, Acceptance)<br>
when this happens to them. Observed times vary between 1 day and 2<br>
weeks to complete the cycle. Seeking help on the mailing-list<br>
is fine. Even if we usually cannot help with getting back your data,<br>
most people found the feedback comforting.</blockquote>

<blockquote>If your header does not contain an intact key-slot salt, best go directly<br>
to the last stage ("Acceptance") and think about what to do now.<br>
There is one exception that I know of: If your LUKS container<br>
is still open, then it may be possible to extract the master<br>
key from the running system. See Item "How do I recover the<br>
master key from a mapped LUKS container?" in Section "Backup<br>
and Data Recovery".</blockquote>


<ul><li><b>5.8 What is a "salt"?</b></li></ul>

<blockquote>A salt is a random key-grade value added to the passphrase before<br>
it is processed. It is not kept secret. The reason for using<br>
salts is as follows: If an attacker wants to crack the password<br>
for a single LUKS container, then every possible passphrase has to<br>
be tried. Typically an attacker will not try every binary value,<br>
but will try words and sentences from a dictionary.</blockquote>

<blockquote>If an attacker wants to attack several LUKS containers with<br>
the same dictionary, then a different approach makes sense:<br>
Compute the resulting slot-key for each dictionary element<br>
and store it on disk. Then the test for each entry is<br>
just the slow unlocking with the slot key (say 0.00001 sec)<br>
instead of calculating the slot-key first (1 sec). For a<br>
single attack, this does not help. But if you have more than<br>
one container to attack, this helps tremendously, also because<br>
you can prepare your table before you even have the container<br>
to attack! The calculation is also very simple to parallelize.<br>
You could, for example, use the night-time unused CPU power<br>
of your desktop PCs for this.</blockquote>

<blockquote>This is where the salt comes in. If the salt is combined with<br>
the passphrase (in the simplest form, just appended to it),<br>
you suddenly need a separate table for each salt value.<br>
With a reasonably-sized salt value (256 bit, e.g.) this is<br>
quite infeasible.</blockquote>


<ul><li><b>5.9 Is LUKS secure with a low-entropy (bad) passphrase?</b></li></ul>

<blockquote>Note: You should only use the 94 printable characters<br>
from 7 bit ASCII code to prevent your passphrase from<br>
failing when the character encoding changes, e.g.<br>
because of a system upgrade, see also the note<br>
at the very start of this FAQ under "WARNINGS".</blockquote>

<blockquote>This needs a bit of theory. The quality of your passphrase<br>
is directly<br>
related to its entropy (information theoretic, not thermodynamic).<br>
The entropy says how many bits of "uncertainty" or "randomness" are<br>
in you passphrase. In other words, that is how difficult guessing the<br>
passphrase is.</blockquote>

<blockquote>Example: A random English sentence has about 1 bit of entropy per<br>
character. A random lowercase (or uppercase) character has about<br>
4.7 bit of entropy.</blockquote>

<blockquote>Now, if n is the number of bits of entropy in your passphrase<br>
and t is the time it takes to process a passphrase in order to open<br>
the LUKS container, then an attacker has to spend at maximum<br>
<pre><code>      attack_time_max = 2^n * t <br>
</code></pre>
time for a successful attack and on average half that. There is no<br>
way getting around that relationship. However, there is one thing<br>
that does help, namely increasing t, the time it takes to use a<br>
passphrase, see next FAQ item.</blockquote>

<blockquote>Still, if you want good security, a high-entropy passphrase is<br>
the only option. For example, a low-entropy passphrase can never<br>
be considered secure against a TLA-level (Three Letter Agency level,<br>
i.e. government-level) attacker, no matter what tricks are used in<br>
the key-derivation function. Use at least 64 bits for secret stuff.<br>
That is 64 characters of English text (but only if randomly chosen)<br>
or a combination of 12 truly random letters and digits.</blockquote>

<blockquote>For passphrase generation, do not use lines from very well-known<br>
texts (religious texts, Harry potter, etc.) as they are to easy<br>
to guess. For example, the total Harry Potter has about 1'500'000<br>
words (my estimation). Trying every 64 character sequence starting<br>
and ending at a word boundary would take only something like 20<br>
days on a single CPU and is entirely feasible. To put that<br>
into perspective, using a number of Amazon EC2 High-CPU<br>
Extra Large instances (each gives about 8 real cores), this<br>
test costs currently about 50USD/EUR, but can be made to run<br>
arbitrarily fast.</blockquote>

<blockquote>On the other hand, choosing 1.5 lines from, say, the Wheel of Time<br>
is in itself not more secure, but the book selection adds quite<br>
a bit of entropy. (Now that I have mentioned it here, don't use<br>
tWoT either!) If you add 2 or 3 typos or switch some words around,<br>
then this is good passphrase material.</blockquote>


<ul><li><b>5.10 What is "iteration count" and why is decreasing it a bad idea?</b></li></ul>

<blockquote>Iteration count is the number of PBKDF2 iterations a passphrase<br>
is put through before it is used to unlock a key-slot. Iterations are<br>
done with the explicit purpose to increase the time that it takes<br>
to unlock a key-slot. This provides some protection against use<br>
of low-entropy passphrases.</blockquote>

<blockquote>The idea is that an attacker has to try all possible passphrases.<br>
Even if the attacker knows the passphrase is low-entropy (see last<br>
item), it is possible to make each individual try take longer.<br>
The way to do this is to repeatedly hash the passphrase for a certain<br>
time. The attacker then has to spend the same time (given the same<br>
computing power) as the user per try. With LUKS, the default is 1<br>
second of PBKDF2 hashing.</blockquote>

<blockquote>Example 1: Lets assume we have a really bad passphrase (e.g. a<br>
girlfriends<br>
name) with 10 bits of entropy. With the same CPU, an attacker would<br>
need to spend around 500 seconds on average to break that passphrase.<br>
Without iteration, it would be more like 0.0001 seconds on a<br>
modern CPU.</blockquote>

<blockquote>Example 2: The user did a bit better and has 32 chars of English<br>
text. That would be about 32 bits of entropy. With 1 second<br>
iteration, that means an attacker on the same CPU needs<br>
around 136 years.<br>
That is pretty impressive for such a weak passphrase.<br>
Without the iterations, it would be more like 50 days on a modern<br>
CPU, and possibly far less.</blockquote>

<blockquote>In addition, the attacker can both parallelize and use special<br>
hardware like GPUs or FPGAs to speed up the attack. The attack can<br>
also happen quite some<br>
time after the luksFormat operation and CPUs can have become<br>
faster and<br>
cheaper. For that reason you want a bit of extra security.<br>
Anyways, in<br>
Example 1 your are screwed. In example 2, not necessarily. Even if<br>
the attack is faster, it still has a certain cost associated with it,<br>
say 10000 EUR/USD with iteration and 1 EUR/USD without iteration. The<br>
first can be prohibitively expensive, while the second is<br>
something you<br>
try even without solid proof that the decryption will yield<br>
something useful.</blockquote>

<blockquote>The numbers above are mostly made up, but show the idea.<br>
Of course the<br>
best thing is to have a high-entropy passphrase.</blockquote>

<blockquote>Would a 100 sec iteration time be even better? Yes and no.<br>
Cryptographically it would be a lot better, namely 100 times better.<br>
However, usability is a very important factor for security technology<br>
and one that gets overlooked surprisingly often. For LUKS, if you<br>
have to wait 2 minutes to unlock the LUKS container, most people<br>
will not bother and use less secure storage instead. It is better to<br>
have less protection against low-entropy passphrases and<br>
people actually<br>
use LUKS, than having them do without encryption altogether.</blockquote>

<blockquote>Now, what about decreasing the iteration time? This is generally a<br>
very bad idea, unless you know and can enforce that the users only<br>
use high-entropy passphrases. If you decrease the iteration time<br>
without ensuring that, then you put your users at increased risk, and<br>
considering how rarely LUKS containers are unlocked in a typical<br>
work-flow,<br>
you do so without a good reason. Don't do it. The iteration time is<br>
already low enough that users with<br>
entropy low passphrases are vulnerable. Lowering it even further<br>
increases this danger significantly.</blockquote>

<ul><li><b>5.11 Some people say PBKDF2 is insecure?</b></li></ul>

<blockquote>There is some discussion that a hash-function should have<br>
a "large memory" property, i.e. that it should require<br>
a lot of memory to be computed. This serves to prevent attacks<br>
using special programmable circuits, like FPGAs, and attacks<br>
using graphics cards. PBKDF2 does not need a lot of memory<br>
and is vulnerable to these attacks. However, the publication<br>
usually referred in these discussions is not very convincing in<br>
proving that the presented hash really is "large memory" (that<br>
may change, email the FAQ maintainer when it does) and it<br>
is of limited usefulness anyways. Attackers that use<br>
clusters of normal PCs will not be affected at all by a<br>
"large memory" property. For example the US Secret Service is<br>
known to use the off-hour time of all the office PCs of the<br>
Treasury for password breaking. The Treasury has<br>
about 110'000 employees. Assuming every one has an office<br>
PC, that is significant computing power, all of it with<br>
plenty of memory for computing "large memory" hashes.<br>
Bot-net operators also have all the memory they want.<br>
The only protection against a resourceful attacker is a<br>
high-entropy passphrase, see items 5.9 and 5.10.</blockquote>

<ul><li><b>5.12 What about iteration count with plain dm-crypt?</b></li></ul>

<blockquote>Simple: There is none. There is also no salting. If you<br>
use plain dm-crypt, the only way to be secure is to use<br>
a high entropy passphrase. If in doubt, use LUKS instead.</blockquote>


<ul><li><b>5.13 Is LUKS with default parameters less secure on a slow CPU?</b></li></ul>

<blockquote>Unfortunately, yes. However the only aspect affected is the<br>
protection for low-entropy passphrase or master-key. All other<br>
security aspects are independent of CPU speed.</blockquote>

<blockquote>The master key is less critical, as you really have to work at it<br>
to give it low entropy. One possibility is to supply the master<br>
key yourself. If that key is low-entropy, then you get what you<br>
deserve. The other known possibility is to use /dev/urandom for<br>
key generation in an entropy-starved situation (e.g. automatic<br>
installation on an embedded device without network and other entropy<br>
sources).</blockquote>

<blockquote>For the passphrase, don't use a low-entropy passphrase. If your<br>
passphrase is good, then a slow CPU will not matter. If you insist<br>
on a low-entropy passphrase on a slow CPU, use something like<br>
"--iter-time=10" or higher and wait a long time on each LUKS<br>
unlock and pray that the attacker does not find out in which<br>
way exactly your passphrase is low entropy. This also applies<br>
to low-entropy passphrases on fast CPUs. Technology can do only<br>
so much to compensate for problems in front of the keyboard.</blockquote>


<ul><li><b>5.14 Why was the default aes-cbc-plain replaced with aes-cbc-essiv?</b></li></ul>

<blockquote>Note: This item applies both to plain dm-crypt and to LUKS</blockquote>

<blockquote>The problem is that cbc-plain has a fingerprint vulnerability, where<br>
a specially crafted file placed into the crypto-container can be<br>
recognized from the outside. The issue here is that for cbc-plain<br>
the initialization vector (IV) is the sector number. The IV gets<br>
XORed to the first data chunk of the sector to be encrypted.<br>
If you make sure that the first data block to be stored in a sector<br>
contains the sector number as well, the first data block to be<br>
encrypted is all zeros and always encrypted to the same ciphertext.<br>
This also works if the first data chunk just has a constant XOR<br>
with the sector number. By having several shifted patterns you<br>
can take care of the case of a non-power-of-two start sector number<br>
of the file.</blockquote>

<blockquote>This mechanism allows you to create a pattern of sectors that have<br>
the same first ciphertext block and signal one bit per sector to the<br>
outside, allowing you to e.g. mark media files that way for<br>
recognition<br>
without decryption. For large files this is a practical attack. For<br>
small ones, you do not have enough blocks to signal and take care<br>
of different file starting offsets.</blockquote>

<blockquote>In order to prevent this attack, the default was changed to cbc-essiv.<br>
ESSIV uses a keyed hash of the sector number, with the encryption key<br>
as key. This makes the IV unpredictable without knowing the encryption<br>
key and the watermarking attack fails.</blockquote>


<ul><li><b>5.15 Are there any problems with "plain" IV? What is "plain64"?</b></li></ul>

<blockquote>First, "plain" and "plain64" are both not secure to use with CBC,<br>
see previous FAQ item.</blockquote>

<blockquote>However there are modes, like XTS, that are secure with "plain" IV.<br>
The next limit is that "plain" is 64 bit, with the upper 32 bit<br>
set to zero. This means that on volumes larger than 2TiB, the IV<br>
repeats, creating a vulnerability that potentially leaks some<br>
data. To avoid this, use "plain64", which uses the full<br>
sector number up to 64 bit. Note that "plain64" requires a kernel<br>
>= 2.6.33. Also note that "plain64" is backwards compatible for<br>
volume sizes <= 2TiB, but not for those > 2TiB. Finally, "plain64"<br>
does not cause any performance penalty compared to "plain".</blockquote>


<ul><li><b>5.16 What about XTS mode?</b></li></ul>

<blockquote>XTS mode is potentially even more secure than cbc-essiv (but only if<br>
cbc-essiv is insecure in your scenario). It is a NIST standard and<br>
used, e.g. in Truecrypt. From version 1.6.0 of cryptsetup<br>
onwards, aes-xts-plain64 is the default for LUKS.<br>
If you want to use it with a cryptsetup<br>
before version 1.6.0 or with plain dm-crypt, you<br>
have to specify it manually as "aes-xts-plain", i.e.<br>
<pre><code>      cryptsetup -c aes-xts-plain luksFormat &lt;device&gt;<br>
</code></pre>
For volumes >2TiB and kernels >= 2.6.33 use "plain64" (see<br>
FAQ item on "plain" and "plain64"):<br>
<pre><code>      cryptsetup -c aes-xts-plain64 luksFormat &lt;device&gt;<br>
</code></pre>
There is a potential security issue with XTS mode and large<br>
blocks. LUKS and dm-crypt always use 512B blocks and the<br>
issue does not apply.</blockquote>

<ul><li><b>5.17 Is LUKS FIPS-140-2 certified?</b></li></ul>

<blockquote>No. But that is more a problem of FIPS-140-2 than of LUKS.<br>
From a technical point-of-view, LUKS with the right<br>
parameters would be FIPS-140-2 compliant, but in order<br>
to make it certified, somebody has to pay real money for<br>
that. And then, whenever cryptsetup is changed or extended,<br>
the certification lapses and has to be obtained again.</blockquote>

<blockquote>From the aspect of actual security, LUKS with default<br>
parameters should be as good as most things that are<br>
FIPS-140-2 certified, although you may want to make sure<br>
to use /dev/random (by specifying --use-random on luksFormat)<br>
as randomness source for the master key to avoid being<br>
potentially insecure in an entropy-starved situation.</blockquote>


<ul><li><b>5.18 What about Plausible Deniability?</b></li></ul>

<blockquote>First let me attempt a definition for the case of encrypted<br>
filesystems: Plausible deniability is when you hide encrypted<br>
data inside an encrypted container and it is not possible to<br>
prove it is there. The idea is compelling and on first<br>
glance it seems possible to do it. And from a cryptographic<br>
point of view, it actually is possible.</blockquote>

<blockquote>So, does it work in practice? No, unfortunately. The reasoning<br>
used by its proponents is fundamentally flawed in several<br>
ways and the cryptographic properties fail fatally when<br>
colliding with the real world.</blockquote>

<blockquote>First, why should "I do not have a hidden partition"<br>
be any more plausible than "I forgot my crypto key" or<br>
"I wiped that partition with random data, nothing in<br>
there"? I do not see any reason.</blockquote>

<blockquote>Second, there are two types of situations: Either they<br>
cannot force you to give them the key (then you simply do<br>
not) or the can. In the second case, they can always do<br>
bad things to you, because they cannot prove that you<br>
have the key in the first place! This means they do not<br>
have to prove you have the key, or that this random<br>
looking data on your disk is actually encrypted data.<br>
So the situation will allow them to waterboard/lock-up/deport<br>
you anyways, regardless of how "plausible" your deniability<br>
is. Do not have a hidden partition you could show to them,<br>
but there are indications you may? Too bad for you.<br>
Unfortunately "plausible deniability" also means you<br>
cannot prove there is no hidden data.</blockquote>

<blockquote>Third, hidden partitions are not that hidden. There are<br>
basically just two possibilities: a) Make a large crypto<br>
container, but put a smaller filesystem in there and<br>
put the hidden partition into the free space. Unfortunately<br>
this is glaringly obvious and can be detected in an automated<br>
fashion. This means that the initial suspicion to put you<br>
under duress in order to make you reveal you hidden<br>
data is given. b) Make a filesystem that spans the<br>
whole encrypted partition, and put the hidden partition<br>
into space not currently used by that filesystem.<br>
Unfortunately that is also glaringly obvious, as you<br>
then cannot write to the filesystem without a high<br>
risk of destroying data in the hidden container.<br>
Have not written anything to the encrypted filesystem<br>
in a while? Too bad, they have the suspicion they need<br>
to do unpleasant things to you.</blockquote>

<blockquote>To be fair, if you prepare option b) carefully and<br>
directly before going into danger, it may work. But<br>
then, the mere presence of encrypted data may already<br>
be enough to get you into trouble in those places were<br>
they can demand encryption keys.</blockquote>

<blockquote>Here is an additional reference for some problems with plausible<br>
deniability: <a href='http://www.schneier.com/paper-truecrypt-dfs.pdf'>http://www.schneier.com/paper-truecrypt-dfs.pdf</a>
I strongly suggest you read it.</blockquote>

<blockquote>So, no, I will not provide any instructions on how to<br>
do it with plain dm-crypt or LUKS. If you insist on<br>
shooting yourself in the foot, you can figure out how<br>
to do it yourself.</blockquote>

<ul><li><b>5.19 What about SSDs, Flash and Hybrid Drives?</b></li></ul>

<blockquote>The problem is that you cannot reliably erase parts of these<br>
devices, mainly due to wear-leveling and possibly defect<br>
management.</blockquote>

<blockquote>Basically, when overwriting a sector (of 512B), what the device<br>
does is to move an internal sector (may be 128kB or even larger)<br>
to some pool of discarded, not-yet erased unused sectors, take a<br>
fresh empty sector from the empty-sector pool and copy the old<br>
sector over with the changes to the small part you wrote.<br>
This is done in some fashion so that larger writes do not<br>
cause a lot of small internal updates.</blockquote>

<blockquote>The thing is that the mappings between outside-addressable<br>
sectors and inside sectors is arbitrary (and the vendors are not<br>
talking). Also the discarded sectors are not necessarily erased<br>
immediately. They may linger a long time.</blockquote>

<blockquote>For plain dm-crypt, the consequences are that older encrypted<br>
data may be lying around in some internal pools of the device.<br>
Thus may or may not be a problem and depends on the application.<br>
Remember the same can happen with a filesystem if consecutive<br>
writes to the same area of a file can go to different sectors.</blockquote>

<blockquote>However, for LUKS, the worst case is that key-slots and LUKS<br>
header may end up in these internal pools. This means that<br>
password management functionality is compromised (the old<br>
passwords may still be around, potentially for a very long<br>
time) and that fast erase by overwriting the header and<br>
key-slot area is insecure.</blockquote>

<blockquote>Also keep in mind that the discarded/used pool may be large.<br>
For example, a 240GB SSD has about 16GB of spare area in the<br>
chips that it is free to do with as it likes. You would need to<br>
make each individual key-slot larger than that to allow<br>
reliable overwriting. And that assumes the disk thinks all<br>
other space is in use. Reading the internal pools using<br>
forensic tools is not that hard, but may involve some<br>
soldering.</blockquote>

<blockquote>What to do?</blockquote>

<blockquote>If you trust the device vendor (you probably should not...)<br>
you can try an ATA "secure erase" command for SSDs. That does<br>
not work for USB keys though and may or may not be secure<br>
for a hybrid drive. If it finishes on an SSD after a<br>
few seconds, it was possibly faked. Unfortunately, for hybrid<br>
drives that indicator does not work, as the drive may<br>
well take the time to truly erase the magnetic part, but<br>
only mark the SSD/Flash part as erased while data is still<br>
in there.</blockquote>

<blockquote>If you can do without password management and are fine with<br>
doing physical destruction for permanently deleting data<br>
(always after one or several full overwrites!), you can<br>
use plain dm-crypt or LUKS.</blockquote>

<blockquote>If you want or need all the original LUKS security features to work,<br>
you can use a detached LUKS header and put that on a conventional,<br>
magnetic disk. That leaves potentially old encrypted data in<br>
the pools on the disk, but otherwise you get LUKS with the<br>
same security as on a magnetic disk.</blockquote>

<blockquote>If you are concerned about your laptop being stolen, you are<br>
likely fine using LUKS on an SSD or hybrid drive. An attacker<br>
would need to have access to an old passphrase (and the key-slot<br>
for this old passphrase would actually need to still be somewhere in<br>
the SSD) for your data to be at risk. So unless you pasted<br>
your old passphrase all over the Internet or the attacker<br>
has knowledge of it from some other source and does a<br>
targeted laptop theft to get at your data, you should be<br>
fine.</blockquote>

<ul><li><b>5.20 LUKS is broken! It uses SHA-1!</b></li></ul>

<blockquote>No, it is not. SHA-1 is (academically) broken for finding collisions,<br>
but not for using it in a key-derivation function. And that collision<br>
vulnerability is for non-iterated use only. And you need the<br>
hash-value in verbatim.</blockquote>

<blockquote>This basically means that if you already have a slot-key, and you<br>
have set the PBKDF2 iteration count to 1 (it is > 10'000 normally),<br>
you could (maybe) derive a different passphrase that gives you the<br>
the same slot-key. But if you have the slot-key, you can already<br>
unlock the key-slot and get the master key, breaking everything.<br>
So basically, this SHA-1 vulnerability allows you to open<br>
a LUKS container with high effort when you already have it<br>
open.</blockquote>

<blockquote>The real problem here is people that do not understand crypto and<br>
claim things are broken just because some mechanism is used that<br>
has been broken for a specific different use. The way the mechanism<br>
is used matters very much. A hash that is broken for one use can be<br>
completely secure for other uses and here it is.</blockquote>

<ul><li><b>5.21 Why is there no "Nuke-Option"?</b></li></ul>

<blockquote>A "Nuke-Option" or "Kill-switch" is a password that when entered<br>
upon unlocking instead wipes the header and all passwords.<br>
So when somebody forces you to enter your password, you can destroy<br>
the data instead.</blockquote>

<blockquote>While this sounds attractive at first glance, it does not make sense<br>
once a real security analysis is done. One problem is that<br>
you have to have some kind of HSM (Hardware Security Module) in order<br>
to implement it securely. In the movies, a HSM starts to smoke and<br>
melt once the Nuke-Option has been activated. In reality, it<br>
just wipes some battery-backed RAM cells. A proper HSM<br>
costs something like 20'000...100'000 EUR/USD and there a Nuke-Option<br>
may make some sense. BTW, a chipcard or a TPM is not a HSM, although<br>
some vendors are promoting that myth.</blockquote>

<blockquote>Now, a proper HSMs will have a wipe option but not a Nuke-Option, i.e.<br>
you can explicitly wipe the HSM, but by a different process than<br>
unlocking it takes. Why is that? Simple: If somebody can force you to<br>
reveal passwords, then they can also do bad things to you if you do not<br>
or if you enter a nuke password instead. Think locking you up for a<br>
few years for "destroying evidence" or for far longer and without<br>
trial for being a "terrorist suspect". No HSM maker will want to<br>
expose its customers to that risk.</blockquote>

<blockquote>Now think of the typical LUKS application scenario, i.e. disk<br>
encryption. Usually the ones forcing you to hand over your password<br>
will have access to the disk as well, and, if they have any real<br>
suspicion, they will mirror your disk before entering anything<br>
supplied by you. This neatly negates any Nuke-Option. If they have no<br>
suspicion (just harassing people that cross some border for example),<br>
the Nuke-Option would work, but see above about likely negative<br>
consequences and remember that a Nuke-Option may not work reliably on<br>
SSD and hybrid drives anyways.</blockquote>

<blockquote>Hence my advice is to never take data that you do not want to reveal<br>
into any such situation in the first place. There is no need to transfer<br>
data on physical carriers today. The Internet makes it quite possible<br>
to transfer data between arbitrary places and modern encryption makes<br>
it secure. If you do it right, nobody will even be able to identify<br>
source or destination. (How to do that is out of scope of this document.<br>
It does require advanced skills in this age of pervasive surveillance.)</blockquote>

<blockquote>Hence, LUKS has not kill option because it would do much more harm<br>
than good.</blockquote>

<blockquote>Still, if you have a good use-case (i.e. non-abstract real-world<br>
situation) where a Nuke-Option would actually be beneficial, please<br>
let me know.</blockquote>

<ul><li><b>5.22 Does cryptsetup open network connections to websites, etc. ?</b></li></ul>

<blockquote>This question seems not to make much sense at first glance, but here<br>
is an example form the real world: The TrueCrypt GUI has a "Donation"<br>
button. Press it, and a web-connection to the TrueCrypt website is<br>
opened via the default browser, telling everybody that listens<br>
that you use TrueCrypt. In the worst case, things like this can get<br>
people tortured or killed.</blockquote>

<blockquote>So: Cryptsetup will never open any network connections except the<br>
local netlink socket it needs to talk to the kernel crypto API.</blockquote>

<blockquote>In addition, the installation package should contain all documentation,<br>
including this FAQ, so that you do not have to go to a web-site to read<br>
it. (If your distro cuts the docu, please complain to them.) In security<br>
software, any connection initiated to anywhere outside your machine<br>
should always be the result of an explicit request for such a connection<br>
by the user and cryptsetup will stay true to that principle.</blockquote>


<h1>6. Backup and Data Recovery</h1>


<ul><li><b>6.1 Why do I need Backup?</b></li></ul>

<blockquote>First, disks die. The rate for well-treated (!) disk is about 5%<br>
per year, which is high enough to worry about. There is<br>
some indication that this may be even worse for some SSDs.<br>
This applies both to LUKS and plain dm-crypt partitions.</blockquote>

<blockquote>Second, for LUKS, if anything damages the LUKS header or<br>
the key-stripe area then decrypting the LUKS device can<br>
become impossible. This is a frequent occurrence. For<br>
example an accidental format as FAT or some software<br>
overwriting the first sector where it suspects a partition<br>
boot sector typically makes a LUKS partition permanently<br>
inaccessible. See more below on LUKS header damage.</blockquote>

<blockquote>So, data-backup in some form is non-optional.<br>
For LUKS, you may also want to store<br>
a header backup in some secure location. This only needs<br>
an update if you change passphrases.</blockquote>


<ul><li><b>6.2 How do I backup a LUKS header?</b></li></ul>

<blockquote>While you could just copy the appropriate number of bytes from<br>
the start<br>
of the LUKS partition, the best way is to use command option<br>
"luksHeaderBackup" of cryptsetup. This protects also against<br>
errors when<br>
non-standard parameters have been used in LUKS partition creation.<br>
Example:<br>
<pre><code>     cryptsetup luksHeaderBackup --header-backup-file &lt;file&gt; &lt;device&gt;<br>
</code></pre>
To restore, use the inverse command, i.e.<br>
<pre><code>     cryptsetup luksHeaderRestore --header-backup-file &lt;file&gt; &lt;device&gt;<br>
</code></pre>
If you are unsure about a header to be restored, make a backup of the<br>
current one first! You can also test the header-file without restoring<br>
it by using the --header option for a detached header like this:<br>
<pre><code>     cryptsetup --header &lt;file&gt; luksOpen &lt;device&gt; &lt;/dev/mapper/ -name&gt;<br>
</code></pre>
If that unlocks your keys-lot, you are good. Do not forget to<br>
close the device again.</blockquote>

<blockquote>Under some circumstances (damaged header), this fails. Then use the<br>
following steps:</blockquote>

<blockquote>First determine the master-key size:<br>
<pre><code>     cryptsetup luksDump &lt;device&gt;<br>
</code></pre>
gives a line of the form<br>
<pre><code>     MK bits:        &lt;bits&gt;<br>
</code></pre>
with bits equal to 256 for the old defaults and 512 for the<br>
new defaults. 256 bits equals a total header size of 1'052'672 Bytes<br>
and 512 bits one of 2MiB. (See also Item 6.12)<br>
If luksDump fails, assume 2MiB, but<br>
be aware that if you restore that, you may also restore the<br>
first 1M or so of the filesystem. Do not change the filesystem if<br>
you were unable to determine the header size! With that, restoring<br>
a too-large header backup is still safe.</blockquote>

<blockquote>Second, dump the header to file. There are many ways to do it,<br>
I prefer the following:<br>
<pre><code>     head -c 1052672 &lt;device&gt;  &gt;  header_backup.dmp<br>
</code></pre>
or<br>
<pre><code>     head -c 2M &lt;device&gt;  &gt;  header_backup.dmp<br>
</code></pre>
for a 2MiB header. Verify the size of the dump-file to be sure.</blockquote>

<blockquote>To restore such a backup, you can try luksHeaderRestore<br>
or do a more basic<br>
<pre><code>     cat header_backup.dmp  &gt;  &lt;device&gt;<br>
</code></pre></blockquote>


<ul><li><b>6.3 How do I test a LUKS header?</b></li></ul>

<blockquote>Use<br>
<pre><code>     cryptsetup -v isLuks &lt;device&gt;<br>
</code></pre>
on the device. Without the "-v" it just signals its result<br>
via exit-status. You can also use the more general test<br>
<pre><code>      blkid -p &lt;device&gt;<br>
</code></pre>
which will also detect other types and give some more info.<br>
Omit "-p" for old versions of blkid that do not support it.</blockquote>


<ul><li><b>6.4 How do I backup a LUKS or dm-crypt partition?</b></li></ul>

<blockquote>There are two options, a sector-image and a plain file or<br>
filesystem backup of the contents of the partition.<br>
The sector image is already encrypted, but cannot be compressed<br>
and contains all empty space. The filesystem backup<br>
can be compressed, can contain only part of the<br>
encrypted device, but needs to be encrypted separately<br>
if so desired.</blockquote>

<blockquote>A sector-image will contain the whole partition in encrypted form,<br>
for LUKS the LUKS<br>
header, the keys-slots and the data area. It can be done under Linux<br>
e.g. with dd_rescue (for a direct image copy) and with "cat" or "dd".<br>
Example:<br>
<pre><code>      cat /dev/sda10 &gt; sda10.img<br>
      dd_rescue /dev/sda10 sda10.img <br>
</code></pre>
You can also use any other backup software that is capable of making a<br>
sector image of a partition.<br>
Note that compression is ineffective for encrypted data,<br>
hence it does not make sense to use it.</blockquote>

<blockquote>For a filesystem backup, you decrypt and mount the encrypted<br>
partition and back it up as you would a normal filesystem.<br>
In this case the backup is not encrypted, unless your<br>
encryption method does that. For example you can encrypt<br>
a backup with "tar" as follows with GnuPG:<br>
<pre><code>      tar cjf - &lt;path&gt; | gpg --cipher-algo AES -c - &gt; backup.tbz2.gpg<br>
</code></pre>
And verify the backup like this if you are at "path":<br>
<pre><code>      cat backup.tbz2.gpg | gpg - | tar djf - <br>
</code></pre>
Note: Always verify backups, especially encrypted ones!</blockquote>

<blockquote>There is one problem with verifying like this: The kernel<br>
may still have some files cached and in fact verify<br>
them against RAM or may even verify RAM against RAM,<br>
which defeats the purpose of the exercise. The following<br>
command empties the kernel caches:<br>
<pre><code>      echo 3 &gt; /proc/sys/vm/drop_caches<br>
</code></pre>
Run it after backup and before verify.</blockquote>

<blockquote>In both cases GnuPG will ask you interactively for your<br>
symmetric key. The verify will only output errors.<br>
Use "tar dvjf -" to get all comparison results.<br>
To make sure no data is written to disk unencrypted,<br>
turn off swap if it is not encrypted before doing the backup.</blockquote>

<blockquote>Restore works like certification with the 'd' ('difference')<br>
replaced by 'x' ('eXtract'). Refer to the man-page of tar<br>
for more explanations and instructions. Note that with default<br>
options tar will overwrite already existing files without warning.<br>
If you are unsure about how to use tar, experiment with it<br>
in a location where you cannot do damage.</blockquote>

<blockquote>You can of course use different or no<br>
compression and you can use an asymmetric key if you have one<br>
and have a backup of the secret key that belongs to it.</blockquote>

<blockquote>A second option for a filesystem-level backup that can be used<br>
when the backup is also on local disk (e.g. an external USB<br>
drive) is to use a LUKS container there and copy the files<br>
to be backed up between both mounted containers. Also see<br>
next item.</blockquote>


<ul><li><b>6.5 Do I need a backup of the full partition? Would the header and  key-slots not be enough?</b></li></ul>

<blockquote>Backup protects you against two things: Disk loss or corruption<br>
and user<br>
error. By far the most questions on the dm-crypt mailing list<br>
about how to<br>
recover a damaged LUKS partition are related to user error.<br>
For example,<br>
if you create a new filesystem on a LUKS partition, chances are<br>
good that<br>
all data is lost permanently.</blockquote>

<blockquote>For this case, a header+key-slot backup would often be enough.<br>
But keep in<br>
mind that a well-treated (!) HDD has roughly a failure risk<br>
of 5% per year. It is highly<br>
advisable to have a complete backup to protect against this case.</blockquote>


<ul><li><b>6.6 What do I need to backup if I use "decrypt_derived"?</li></ul></b>

<blockquote>This is a script in Debian, intended for mounting /tmp<br>
or swap with a key derived from the master key of an already<br>
decrypted device. If you use this for an device with<br>
data that should be persistent, you need to make sure you either<br>
do not lose access to that master key or have a backup of<br>
the data. If you derive from a LUKS<br>
device, a header backup of that device would cover<br>
backing up the master key. Keep in mind that this does<br>
not protect against disk loss.</blockquote>

<blockquote>Note: If you recreate the LUKS header of the device you<br>
derive from (using luksFormat), the master key changes<br>
even if you use the same passphrase(s) and you will not<br>
be able to decrypt the derived device with the new LUKS<br>
header.</blockquote>

<ul><li><b>6.7 Does a backup compromise security?</b></li></ul>

<blockquote>Depends on how you do it. However if you do not have one,<br>
you are going to eventually lose your encrypted data.</blockquote>

<blockquote>There are risks introduced by backups. For example if you<br>
change/disable a key-slot in LUKS, a binary backup of the partition<br>
will still have the old key-slot. To deal with this, you have to<br>
be able to change the key-slot on the backup as well, securely<br>
erase the backup or do a filesystem-level backup instead of a binary<br>
one.</blockquote>

<blockquote>If you use dm-crypt, backup is simpler: As there is no<br>
key management, the<br>
main risk is that you cannot wipe the backup when wiping the original.<br>
However wiping the original for dm-crypt should consist of<br>
forgetting the<br>
passphrase and that you can do without actual access to the backup.</blockquote>

<blockquote>In both cases, there is an additional (usually small) risk<br>
with binary backups:<br>
An attacker can<br>
see how many sectors and which ones have been changed since<br>
the backup. To prevent this, use a filesystem level backup<br>
method that encrypts the whole backup in one go, e.g.<br>
as described above with tar and GnuPG.</blockquote>

<blockquote>My personal advice is to use one USB disk (low value data) or<br>
three disks (high<br>
value data) in rotating order for backups, and either use<br>
independent LUKS partitions on them, or use encrypted<br>
backup with tar and GnuPG.</blockquote>

<blockquote>If you do network-backup or tape-backup, I strongly recommend to<br>
go the filesystem backup path with independent encryption,<br>
as you typically cannot reliably delete data<br>
in these scenarios, especially in a cloud setting.<br>
(Well, you can burn the tape if it is under your control...)</blockquote>


<ul><li><b>6.8 What happens if I overwrite the start of a LUKS partition or damage the LUKS header or key-slots?</b></li></ul>

<blockquote>There are two critical components for decryption: The salt values<br>
in the key-slot descriptors of the header and the key-slots. If the salt values<br>
are overwritten<br>
or changed, nothing (in the cryptographically strong sense) can<br>
be done to<br>
access the data, unless there is a backup of the LUKS header.<br>
If a key-slot<br>
is damaged, the data can still be read with a different key-slot,<br>
if there is<br>
a remaining undamaged and used key-slot. Note that in order to make a<br>
key-slot unrecoverable in a cryptographically strong sense, changing<br>
about 4-6 bits in random locations of its 128kiB size is quite enough.</blockquote>


<ul><li><b>6.9 What happens if I (quick) format a LUKS partition?</b></li></ul>

<blockquote>I have not tried the different ways to do this, but very likely you<br>
will have written a new boot-sector, which in turn overwrites<br>
the LUKS header, including the salts, making your data<br>
permanently irretrievable, unless you have a LUKS header<br>
backup. You may also damage the<br>
key-slots in part or in full. See also last item.</blockquote>


<ul><li><b>6.10 How do I recover the master key from a mapped LUKS container?</b></li></ul>

<blockquote>This is typically only needed if you managed to damage your<br>
LUKS header, but the container is still mapped, i.e.<br>
"luksOpen"ed. It also helps if you have a mapped container<br>
that you forgot or do not know a passphrase for (e.g.<br>
on a long running server.)</blockquote>

<blockquote>WARNING: Things go wrong, do a full backup before trying this!</blockquote>

<blockquote>WARNING: This exposes the master key of the LUKS container.<br>
Note that<br>
both ways to recreate a LUKS header with the old master<br>
key described below will write the master key to disk.<br>
Unless you are sure you have securely erased it<br>
afterwards, e.g. by writing it to an encrypted partition,<br>
RAM disk or by erasing the filesystem you wrote it to<br>
by a complete overwrite, you should change the master<br>
key afterwards.<br>
Changing the master key requires a full data backup,<br>
luksFormat and then restore of the backup.</blockquote>

<blockquote>First, there is a script by Milan that automates<br>
the whole process, except generating a new LUKS header<br>
with the old master key (it prints the command for that<br>
though):</blockquote>

<blockquote><a href='http://code.google.com/p/cryptsetup/source/browse/misc/luks-header-from-active'>http://code.google.com/p/cryptsetup/source/browse/misc/luks-header-from-active</a></blockquote>

<blockquote>You can also do this manually. Here is how:</blockquote>

<blockquote>- Get the master key from the device mapper.<br>
This is done<br>
by the following command. Substitute c5 for whatever you<br>
mapped to:<br>
<pre><code>      # dmsetup table --target crypt --showkey /dev/mapper/c5<br>
<br>
      Result:<br>
      0 200704 crypt aes-cbc-essiv:sha256 <br>
      a1704d9715f73a1bb4db581dcacadaf405e700d591e93e2eaade13ba653d0d09 <br>
      0 7:0 4096<br>
</code></pre>
The result is actually one line, wrapped here for clarity.<br>
The long hex string is the master key.</blockquote>

<blockquote>- Convert the master key to a binary file representation.<br>
You can do this manually, e.g. with hexedit. You can also<br>
use the tool "xxd" from vim like this:<br>
<pre><code>      echo "a1704d9....53d0d09" | xxd -r -p &gt; &lt;master-key-file&gt;<br>
</code></pre></blockquote>

<blockquote>- Do a luksFormat to create a new LUKS header.</blockquote>

<blockquote>NOTE: If your header is intact and you just forgot the<br>
passphrase, you can just set a new passphrase, see next<br>
sub-item.</blockquote>

<blockquote>Unmap the device before you do that (luksClose). Then do<br>
<pre><code>      cryptsetup luksFormat --master-key-file=&lt;master-key-file&gt; &lt;luks device&gt;<br>
</code></pre>
Note that if the container was created with other than the<br>
default settings of the cryptsetup version you are using,<br>
you need to give additional parameters specifying the<br>
deviations. If in doubt, try the script by Milan.<br>
It does recover the other parameters as well.</blockquote>

<blockquote>Side note: This is the way the decrypt_derived<br>
script gets at the master key. It just omits the<br>
conversion and hashes the master key string.</blockquote>

<blockquote>- If the header is intact and you just forgot the passphrase,<br>
just set a new passphrase like this:<br>
<pre><code>      cryptsetup luksAddKey --master-key-file=&lt;master-key-file&gt; &lt;luks device&gt;<br>
</code></pre>
You may want to disable the old one afterwards.</blockquote>

<ul><li><b>6.11 What does the on-disk structure of dm-crypt look like?</b></li></ul>

<blockquote>There is none. dm-crypt takes a block device and gives encrypted<br>
access to each of its blocks with a key derived from the passphrase<br>
given. If you use a cipher different than the default, you have<br>
to specify that as a parameter to cryptsetup too. If you want<br>
to change the password, you basically have to create a second<br>
encrypted device with the new passphrase and copy your data over.<br>
On the plus side, if you accidentally overwrite any part of a<br>
dm-crypt device, the damage will be limited to the area you overwrote.</blockquote>


<ul><li><b>6.12 What does the on-disk structure of LUKS look like?</b></li></ul>

<blockquote>A LUKS partition consists of a header, followed by 8<br>
key-slot descriptors,<br>
followed by 8 key slots, followed by the encrypted data area.</blockquote>

<blockquote>Header and key-slot descriptors fill the first 592 bytes. The<br>
key-slot size depends on the creation parameters, namely on the<br>
number of anti-forensic stripes, key material offset and<br>
master key size.</blockquote>

<blockquote>With the default parameters, each key-slot is a bit less<br>
than 128kiB<br>
in size. Due to sector alignment of the key-slot start, that<br>
means the key<br>
block 0 is at offset 0x1000-0x20400, key block 1 at<br>
offset 0x21000-0x40400,<br>
and key block 7 at offset 0xc1000-0xe0400. The space to the next full<br>
sector address is padded with zeros. Never used key-slots are filled<br>
with what the disk originally contained there, a key-slot removed<br>
with<br>
"luksRemoveKey" or "luksKillSlot" gets filled with 0xff.<br>
Due to 2MiB default alignment, start of the data area for<br>
cryptsetup 1.3 and later is at 2MiB, i.e. at 0x200000.<br>
For older versions, it is at 0x101000,<br>
i.e.<br>
at 1'052'672 bytes, i.e. at 1MiB + 4096 bytes from the start of<br>
the partition.<br>
Incidentally, "luksHeaderBackup" for a LUKS container created<br>
with default parameters<br>
dumps exactly the first 2MiB (or 1'052'672 bytes for<br>
headers created with cryptsetup versions < 1.3) to file and<br>
"luksHeaderRestore" restores them.</blockquote>

<blockquote>For non-default parameters, you have to figure out placement<br>
yourself. "luksDump" helps. See also next item.<br>
For the most common non-default<br>
settings, namely aes-xts-plain with 512 bit key, the offsets<br>
are: 1st keyslot 0x1000-0x3f800, 2nd keyslot 0x40000-0x7e000,<br>
3rd keyslot 0x7e000-0xbd800, ..., and start of bulk data<br>
at 0x200000.</blockquote>

<blockquote>The exact specification of the format is here:<br>
<blockquote><a href='http://code.google.com/p/cryptsetup/wiki/Specification'>http://code.google.com/p/cryptsetup/wiki/Specification</a></blockquote></blockquote>

<blockquote>For your convenience, here is the LUKS header with hex offsets.<br>
NOTE: The spec counts key-slots from 1 to 8, but the cryptsetup<br>
tool counts from 0 to 7. The numbers here refer to the<br>
cryptsetup numbers.</blockquote>

<pre><code>Refers to LUKS On-Disk Format Specification Version 1.2.1<br>
<br>
LUKS header:<br>
<br>
offset  length  name             data type  description<br>
-----------------------------------------------------------------------<br>
0x0000   0x06   magic            byte[]     'L','U','K','S', 0xba, 0xbe<br>
     0      6<br>
0x0006   0x02   version          uint16_t   LUKS version<br>
     6      3<br>
0x0008   0x20   cipher-name      char[]     cipher name spec.<br>
     8     32<br>
0x0028   0x20   cipher-mode      char[]     cipher mode spec.<br>
    40     32<br>
0x0048   0x20   hash-spec        char[]     hash spec.<br>
    72     32<br>
0x0068   0x04   payload-offset   uint32_t   bulk data offset in sectors<br>
   104      4                               (512 bytes per sector)<br>
0x006c   0x04   key-bytes        uint32_t   number of bytes in key<br>
   108      4<br>
0x0070   0x14   mk-digest        byte[]     master key checksum<br>
   112     20                               calculated with PBKDF2<br>
0x0084   0x20   mk-digest-salt   byte[]     salt for PBKDF2 when<br>
   132     32                               calculating mk-digest<br>
0x00a4   0x04   mk-digest-iter   uint32_t   iteration count for PBKDF2<br>
   164      4                               when calculating mk-digest<br>
0x00a8   0x28   uuid             char[]     partition UUID<br>
   168     40<br>
0x00d0   0x30   key-slot-0       key slot   key slot 0<br>
   208     48<br>
0x0100   0x30   key-slot-1       key slot   key slot 1<br>
   256     48<br>
0x0130   0x30   key-slot-2       key slot   key slot 2<br>
   304     48<br>
0x0160   0x30   key-slot-3       key slot   key slot 3<br>
   352     48<br>
0x0190   0x30   key-slot-4       key slot   key slot 4<br>
   400     48<br>
0x01c0   0x30   key-slot-5       key slot   key slot 5<br>
   448     48<br>
0x01f0   0x30   key-slot-6       key slot   key slot 6<br>
   496     48<br>
0x0220   0x30   key-slot-7       key slot   key slot 7<br>
   544     48<br>
<br>
<br>
Key slot:<br>
<br>
offset  length  name                  data type  description<br>
-------------------------------------------------------------------------<br>
0x0000   0x04   active                uint32_t   key slot enabled/disabled<br>
     0      4<br>
0x0004   0x04   iterations            uint32_t   PBKDF2 iteration count<br>
     4      4<br>
0x0008   0x20   salt                  byte[]     PBKDF2 salt<br>
     8     32<br>
0x0028   0x04   key-material-offset   uint32_t   key start sector<br>
    40      4                                    (512 bytes/sector)<br>
0x002c   0x04   stripes               uint32_t   number of anti-forensic<br>
    44      4                                    stripes<br>
</code></pre>

<ul><li><b>6.13 What is the smallest possible LUKS container?</b></li></ul>

<blockquote>Note: From cryptsetup 1.3 onwards, alignment is<br>
set to 1MB. With modern Linux partitioning tools that also<br>
align to 1MB, this will result in alignment to 2k sectors and<br>
typical Flash/SSD sectors, which is highly desirable for<br>
a number of reasons. Changing the alignment is not recommended.</blockquote>

<blockquote>That said, with default parameters, the data area starts at<br>
exactly 2MB offset (at 0x101000 for cryptsetup versions<br>
before 1.3). The smallest data area you can have is<br>
one sector of 512 bytes. Data areas of 0 bytes can be<br>
created, but fail on mapping.</blockquote>

<blockquote>While you cannot put a filesystem into<br>
something this small, it may still be used to contain, for<br>
example, key. Note that with current formatting tools,<br>
a partition for a container this size will be 3MiB anyways.<br>
If you put the LUKS container into a file (via losetup and<br>
a loopback device), the file needs to be 2097664 bytes<br>
in size, i.e. 2MiB + 512B.</blockquote>

<blockquote>There two ways to influence the start of the data area<br>
are key-size and alignment.</blockquote>

<blockquote>For alignment, you can go down to 1 on the parameter. This<br>
will still leave you with a data-area starting at 0x101000,<br>
i.e. 1MiB+4096B (default parameters) as alignment will be rounded<br>
up to the next multiple of 8 (i.e. 4096 bytes)<br>
If in doubt, do a dry-run on a larger file and dump the<br>
LUKS header to get actual information.</blockquote>

<blockquote>For key-size, you can use 128 bit (e.g. AES-128 with CBC),<br>
256 bit (e.g. AES-256 with CBC) or 512 bit (e.g. AES-256 with XTS<br>
mode). You can do 64 bit (e.g. blowfish-64 with CBC),<br>
but anything below 128 bit has to be considered insecure today.</blockquote>

<blockquote>Example 1 - AES 128 bit with CBC:<br>
<pre><code>      cryptsetup luksFormat -s 128 --align-payload=8 &lt;device&gt;<br>
</code></pre>
This results in a data offset of 0x81000, i.e. 516KiB or<br>
528384 bytes. Add one 512 byte sector and the smallest<br>
LUKS container size with these parameters is 516KiB + 512B<br>
or 528896 bytes.</blockquote>

<blockquote>Example 2 - Blowfish 64 bit with CBC (WARNING: insecure):<br>
<pre><code>      cryptsetup luksFormat -c blowfish -s 64 --align-payload=8 /dev/loop0<br>
</code></pre>
This results in a data offset of 0x41000, i.e. 260kiB<br>
or 266240 bytes, with a minimal LUKS container size of<br>
260kiB + 512B or 266752 bytes.</blockquote>


<ul><li><b>6.14 I think this is overly complicated. Is there an alternative?</b></li></ul>

<blockquote>Not really. Encryption comes at a price. You can use plain<br>
dm-crypt to simplify things a bit. It does not allow<br>
multiple passphrases,<br>
but on the plus side, it has zero on disk description and if you<br>
overwrite some part of a plain dm-crypt partition, exactly the<br>
overwritten parts are lost (rounded up to sector borders).</blockquote>

<ul><li><b>6.15 Can I clone a LUKS container?</b></li></ul>

<blockquote>You can, but it breaks security, because the cloned container<br>
has the same header and hence the same master key. You cannot<br>
change the master key on a LUKS container, even if you change<br>
the passphrase(s), the master key stays the same. That means<br>
whoever has access to one of the clones can decrypt them all,<br>
completely bypassing the passphrases.</blockquote>

<blockquote>The right way to do this is to first luksFormat the target<br>
container, then to clone the contents of the source container,<br>
with both containers mapped, i.e. decrypted. You can clone the<br>
decrypted contents of a LUKS container in binary mode, although<br>
you may run into secondary issues with GUIDs in filesystems,<br>
partition tables, RAID-components and the like. These are<br>
just the normal problems binary cloning causes.</blockquote>

<blockquote>Note that if you need to ship (e.g.) cloned LUKS containers<br>
with a default passphrase, that is fine as long as each<br>
container was individually created (and hence has its own<br>
master key). In this case, changing the default passphrase<br>
will make it secure again.</blockquote>


<h1>7. Interoperability with other Disk Encryption Tools</h1>

<ul><li><b>7.1 What is this section about?</b></li></ul>

<blockquote>Cryptsetup for plain dm-crypt can be used to access a number<br>
of on-disk formats created by tools like loop-aes patched<br>
into losetup. This sometimes works and sometimes does not.<br>
This section collects insights into what works, what does<br>
not and where more information is required.</blockquote>

<blockquote>Additional information may be found in the mailing-list<br>
archives, mentioned at the start of this FAQ document. If<br>
you have a solution working that is not yet documented here<br>
and think a wider audience may be interested, please<br>
email the FAQ maintainer.</blockquote>

<ul><li><b>7.2 loop-aes: General observations.</b></li></ul>

<blockquote>One problem is that there are different versions of losetup<br>
around. loop-aes is a patch for losetup. Possible problems<br>
and deviations from cryptsetup option syntax include:</blockquote>

<blockquote>- Offsets specified in bytes (cryptsetup: 512 byte sectors)</blockquote>

<blockquote>- The need to specify an IV offset</blockquote>

<blockquote>- Encryption mode needs specifying (e.g. "-c twofish-cbc-plain")</blockquote>

<blockquote>- Key size needs specifying (e.g. "-s 128" for 128 bit keys)</blockquote>

<blockquote>- Passphrase hash algorithm needs specifying</blockquote>

<blockquote>Also note that because plain dm-crypt and loop-aes format does<br>
not have metadata, and while the loopAES extension for<br>
cryptsetup tries autodetection (see command loopaesOpen), it<br>
may not always work. If you still have the old set-up, using<br>
a verbosity  option (-v) on mapping with the old tool or having a<br>
look into the system logs after setup could give you the<br>
information you need. Below, there are also some things that worked<br>
for somebody.</blockquote>


<ul><li><b>7.3 loop-aes patched into losetup on Debian 5.x, kernel 2.6.32</b></li></ul>

<blockquote>In this case, the main problem seems to be that this variant<br>
of losetup takes the offset (-o option) in bytes, while<br>
cryptsetup takes it in sectors of 512 bytes each.<br>
Example: The losetup command<br>
<pre><code>  losetup -e twofish -o 2560 /dev/loop0 /dev/sdb1 <br>
  mount /dev/loop0 mount-point<br>
</code></pre>
translates to<br>
<pre><code>  cryptsetup create -c twofish -o 5 --skip 5 e1 /dev/sdb1<br>
  mount /dev/mapper/e1 mount-point<br>
</code></pre></blockquote>

<ul><li><b>7.4 loop-aes with 160 bit key</b></li></ul>

<blockquote>This seems to be sometimes used with twofish and blowfish<br>
and represents a 160 bit ripemed160 hash output padded<br>
to 196 bit key length. It seems the corresponding options<br>
for cryptsetup are<br>
<pre><code>  --cipher twofish-cbc-null -s 192 -h ripemd160:20<br>
</code></pre></blockquote>

<ul><li><b>7.5 loop-aes v1 format OpenSUSE</b></li></ul>

<blockquote>Apparently this is done by older OpenSUSE distros and<br>
stopped working from OpenSUSE 12.1 to 12.2. One user<br>
had success with the following:<br>
<pre><code>  cryptsetup create &lt;target&gt; &lt;device&gt; -c aes -s 128 -h sha256<br>
</code></pre></blockquote>

<ul><li><b>7.6 Kernel encrypted loop device (cryptoloop)</b></li></ul>

<blockquote>There are a number of different losetup implementations for using<br>
encrypted loop devices so getting this to work may need a bit<br>
of experimentation.</blockquote>

<blockquote>NOTE: Do NOT use this for new containers! Some of the existing<br>
implementations are insecure and future support is uncertain.</blockquote>

<blockquote>Example for a compatible mapping:<br>
<pre><code>    losetup -e twofish -N /dev/loop0 /image.img<br>
</code></pre>
translates to<br>
<pre><code>    cryptsetup create image_plain /image.img -c twofish-cbc-plain -H plain<br>
</code></pre>
with the mapping being done to /dev/mapper/image_plain instead of<br>
to /dev/loop0.</blockquote>

<blockquote>More details:</blockquote>

<blockquote>Cipher, mode and pasword hash (or no hash):<br>
<pre><code>  -e cipher [-N]        =&gt; -c cipher-cbc-plain -H plain [-s 256]<br>
  -e cipher             =&gt; -c cipher-cbc-plain -H ripemd160 [-s 256]<br>
</code></pre></blockquote>

<blockquote>Key size and offsets (losetup: bytes, cryptsetuop: sectors of 512 bytes):<br>
<pre><code>  -k 128                 =&gt; -s 128<br>
  -o 2560                =&gt; -o 5 -p 5       # 2560/512 = 5<br>
</code></pre></blockquote>

<blockquote>There is no replacement for --pass-fd, it has to be emulated using<br>
keyfiles, see the cryptsetup man-page.</blockquote>

<h1>8. Issues with Specific Versions of cryptsetup</h1>


<ul><li><b>8.1 When using the create command for plain dm-crypt with cryptsetup 1.1.x, the mapping is incompatible and my data is not accessible anymore!</b></li></ul>

<blockquote>With cryptsetup 1.1.x, the distro maintainer can define<br>
different default encryption modes.  You can check  the compiled-in<br>
defaults using "cryptsetup --help". Moreover, the plain<br>
device default changed because the old IV mode was vulnerable to a<br>
watermarking attack.</blockquote>

<blockquote>If you are using a plain device and you need a compatible mode, just<br>
specify cipher, key size and hash algorithm explicitly. For compatibility<br>
with cryptsetup 1.0.x defaults, simple use the following:<br>
<pre><code>    cryptsetup create -c aes-cbc-plain -s 256 -h ripemd160 &lt;name&gt; &lt;dev&gt;<br>
</code></pre></blockquote>

<blockquote>LUKS stores cipher and mode in the metadata on disk, avoiding<br>
this problem.</blockquote>


<ul><li><b>8.2 cryptsetup on SLED 10 has problems...</b></li></ul>

<blockquote>SLED 10 is missing an essential kernel patch for<br>
dm-crypt, which is broken in its kernel as a result. There may<br>
be a very old version of cryptsetup (1.0.x) provided by SLED,<br>
which should also not be used anymore as well. My advice would<br>
be to drop SLED 10.</blockquote>

<ul><li><b>8.3 Gcrypt 1.6.x and later break Whirlpool</b></li></ul>

<blockquote>It is the other way round: In gcrypt 1.5.x, Whirlpool<br>
is broken and it was fixed in 1.6.0 and later. If you<br>
selected whirlpool as hash on creation of a LUKS container,<br>
it does not work anymore with the fixed library. This shows<br>
one serious risk of using rarely used settings.</blockquote>

<blockquote>Note that at the time this FAQ item was written, 1.5.4 was<br>
the latest 1.5.x version and it has the flaw, i.e. works<br>
with the old Whirlpool version. Possibly later 1.5.x<br>
versions will work as well. If not, please let me know.</blockquote>

<blockquote>The only two ways to access older LUKS containers created with<br>
Whirlpool are to either decrypt with<br>
an old gcrypt version that has the flaw or to use a compatibility<br>
feature introduced in cryptsetup 1.6.4 and gcrypt 1.6.1 or<br>
later. Version 1.6.0 cannot be used.</blockquote>

<blockquote>Steps:</blockquote>

<blockquote>- Make at least a header backup or better, refresh your full<br>
backup. (You have a full backup, right? See Item 6.1<br>
and following.)</blockquote>

<blockquote>- Make sure you have cryptsetup 1.6.4 or later and check the gcrypt version:</blockquote>

<pre><code>     cryptsetup luksDump &lt;your luks device&gt; --debug | grep backend<br>
</code></pre>

<blockquote>If gcrypt is at version 1.5.x or before:</blockquote>

<blockquote>- Reencrypt the LUKS header with a different hash. (Requires entering<br>
all keyslot passphrases. If you do not have all, remove the ones<br>
you do not have before.):</blockquote>

<pre><code>     cryptsetup-reencrypt --keep-key --hash sha256 &lt;your luks device&gt;<br>
</code></pre>

<blockquote>If gcrypt is at version 1.6.1 or later:</blockquote>

<blockquote>- Patch the hash name in the LUKS header from "whirlpool"<br>
to "whirlpool_gcryptbug". This activates the broken<br>
implementation. The detailed header layout is in Item 6.12 of this<br>
FAQ and in the LUKS on-disk format specification.<br>
One way to change the hash is with the following command:</blockquote>

<pre><code>     echo -n -e 'whirlpool_gcryptbug\0' | dd of=&lt;luks device&gt; bs=1 seek=72 conv=notrunc<br>
</code></pre>

<blockquote>- You can now open the device again. It is highly advisable to change<br>
the hash now with cryptsetup-reencrypt as described above. While you<br>
can reencrypt to use the fixed whirlpool, that may not be a good idea<br>
as almost nobody seems to use it and hence the long time until the<br>
bug was discovered.</blockquote>


<h1>9. References and Further Reading</h1>

<ul><li><b>Purpose of this Section</b></li></ul>

<blockquote>The purpose of this section is to collect references to<br>
all materials that do not fit the FAQ but are relevant in<br>
some fashion. This can be core topics like the LUKS<br>
spec or disk encryption, but it can also be more<br>
tangential, like secure storage management or cryptography<br>
used in LUKS. It should still have relevance to<br>
cryptsetup and its applications.</blockquote>

<blockquote>If you wan to see something added here, send email to<br>
the maintainer (or the cryptsetup mailing list) giving<br>
an URL, a description (1-3 lines preferred) and a<br>
section to put it in. You can also propose new sections.</blockquote>

<blockquote>At this time I would like to limit the references to<br>
things that are available on the web.</blockquote>

<ul><li><b>Specifications</b></li></ul>

<blockquote>- LUKS on-disk format spec:<br>
<blockquote><a href='http://code.google.com/p/cryptsetup/wiki/Specification'>http://code.google.com/p/cryptsetup/wiki/Specification</a></blockquote></blockquote>

<ul><li><b>Code Examples</b></li></ul>

<blockquote>- Some code examples are in the source package under docs/examples</blockquote>

<blockquote>- LUKS AF Splitter in Ruby by John Lane: <a href='https://rubygems.org/gems/afsplitter'>https://rubygems.org/gems/afsplitter</a></blockquote>

<ul><li><b>Brute-forcing passphrases</b></li></ul>

<blockquote>- <a href='http://news.electricalchemy.net/2009/10/password-cracking-in-cloud-part-5.html'>http://news.electricalchemy.net/2009/10/password-cracking-in-cloud-part-5.html</a></blockquote>

<blockquote>- <a href='http://it.slashdot.org/story/12/12/05/0623215/new-25-gpu-monster-devours-strong-passwords-in-minutes'>http://it.slashdot.org/story/12/12/05/0623215/new-25-gpu-monster-devours-strong-passwords-in-minutes</a></blockquote>

<ul><li><b>Tools</b></li></ul>

<ul><li><b>SSD and Flash Disk Related</b></li></ul>

<ul><li><b>Disk Encryption</b></li></ul>

<ul><li><b>Attacks Against Disk Encryption</b></li></ul>

<ul><li><b>Risk Management as Relevant for Disk Encryption</b></li></ul>

<ul><li><b>Cryptography</b></li></ul>

<ul><li><b>Secure Storage</b></li></ul>


<h1>A. Contributors</h1>
In no particular order:<br>
<br>
<blockquote>- Arno Wagner</blockquote>

<blockquote>- Milan Broz</blockquote>


<hr />
<hr />
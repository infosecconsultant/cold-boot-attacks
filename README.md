# Cold Boot Attack Tools for Linux

## Notes
I've duplicated this research to make it easier to find for me and in case it gets lost. Source is: https://citp.princeton.edu/our-work/memory/

## LEST WE REMEMBER: COLD BOOT ATTACKS ON ENCRYPTION KEYS

J. Alex Halderman, Seth D. Schoen, Nadia Heninger, William Clarkson, William Paul, Joseph A. Calandrino, Ariel J. Feldman, Jacob Appelbaum, and Edward W. Felten.

Abstract Contrary to popular assumption, DRAMs used in most modern computers retain their contents for seconds to minutes after power is lost, even at operating temperatures and even if removed from a motherboard. Although DRAMs become less reliable when they are not refreshed, they are not immediately erased, and their contents persist sufficiently for malicious (or forensic) acquisition of usable full-system memory images. We show that this phenomenon limits the ability of an operating system to protect cryptographic key material from an attacker with physical access. We use cold reboots to mount attacks on popular disk encryption systems — BitLocker, FileVault, dm-crypt, and TrueCrypt — using no special devices or materials. We experimentally characterize the extent and predictability of memory remanence and report that remanence times can be increased dramatically with simple techniques. We offer new algorithms for finding cryptographic keys in memory images and for correcting errors caused by bit decay. Though we discuss several strategies for partially mitigating these risks, we know of no simple remedy that would eliminate them.

Full research paper http://citpsite.s3.amazonaws.com/wp-content/uploads/2019/01/23195456/halderman.pdf
Blog Post: https://freedom-to-tinker.com/2008/02/21/new-research-result-cold-boot-attacks-disk-encryption/

Appeared in Proc. 17th USENIX Security Symposium (Sec ’08), San Jose, CA, July 2008.

## EXPERIMENTING WITH MEMORY REMANENCE

Advanced users can try to observe memory remanence effects on their own systems by performing this simple experiment. (These instructions are written for Linux machines, but they can be adapted for other operating systems.)

Create a Python program with the following code:
#!/usr/bin/env python


# a pirate's favorite chemical element

a = ""

while 1: a += "ARGON"

This program will fill memory with copies of the word “ARGON”.

Run the sync command to flush any cached data to the hard disk.
Start the Python program, and allow it to run for several minutes. It won’t display anything on the screen, but after a while you should see hard drive activity as the memory fills and data gets swapped to disk.
Deliberately crash the system by turning the power off and on again or briefly removing the battery and power cord.
After the system reboots, look for the “ARGON” pattern in memory. You can use the following command to print strings of text contained in RAM:
sudo strings /dev/mem | less

If you see copies of the string “ARGON”, some of the contents of memory survived the reboot. You’ll see many other strings that were loaded into memory when the system restarted, and possibly other data left over from before it rebooted.

If you don’t see any copies of the pattern, possible explanations include (1) you have ECC (error-correcting) RAM, which the BIOS clears at boot; (2) your BIOS clears RAM at boot for another reason (try disabling the memory test or enabling “Quick Boot” mode); (3) your RAM’s retention time is too short to be noticeable at normal temperatures. In any case, your computer might still be vulnerable — an attacker could cool the RAM so that the data takes longer to decay and/or transfer the memory modules to a computer that doesn’t clear RAM at boot and read them there.




## FAQ

Q. Who was responsible for this research?

A. This project is joint work by J. Alex Halderman (Princeton), Seth D. Schoen (Electronic Frontier Foundation), Nadia Heninger (Princeton), William Clarkson (Princeton), William Paul (Wind River Systems), Joseph A. Calandrino (Princeton), Ariel J. Feldman (Princeton), Jacob Appelbaum, and Edward W. Felten (Princeton).

Q. What encryption software is vulnerable to these attacks?

A. We have demonstrated practical attacks against several popular disk encryption systems: BitLocker (a feature of Windows Vista), FileVault (a feature of Mac OS X), dm-crypt (a feature of Linux), and TrueCrypt (a third-party application for Windows, Linux, and Mac OS X). Since these problems result from common design limitations of these systems rather than specific bugs, most similar disk encryption applications, including many running on servers, are probably also vulnerable.

Q. What can users do to protect themselves?

A. The most effective way for users to protect themselves is to fully shut down their computers several minutes before any situation in which the computers’ physical security could be compromised. On most systems, locking the screen or switching to “suspend” or “hibernate” mode does not provide adequate protection. (Exceptions exist; some systems may not be protected even when powered off. Check with the developer of your disk encryption software for further guidance.)

Q. Don’t we already know that someone with physical access to my computer can steal my data?

A. The main purpose of disk encryption is to prevent someone who has physical access to your computer from accessing your data without your key or password. People commonly use these tools with the assumption that they provide substantial protection should their computers be lost or stolen. Unfortunately, we demonstrate that existing disk encryption systems rely on assumptions about computer memory that make them vulnerable to attack under certain common circumstances.

Q. Isn’t this the same as burn-in effects noticed by Gutmann? Can’t encryption programs rotate keys to get around this?

A. Gutmann notes that data written to RAM for extended periods may become “burned in,” allowing it to be easily recovered later. We describe a different effect: data written even momentarily to RAM persists for a non-trivial period of time. We exclusively rely on the latter effect to recover data. This allows us to recover keys even if, following Gutmann’s advice, those keys are stored only briefly at any single location within RAM.

Q. Isn’t your attack difficult to carry out? Don’t you need materials like liquid nitrogen?

A. We found that information in most computers’ RAMs will persist from several seconds to a minute even at room temperature. We also found a cheap and widely available product — “canned air” spray dusters — can be used to produce temperatures cold enough to make RAM contents last for a long time even when the memory chips are physically removed from the computer. The other components of our attack are easy to automate and require nothing more unusual than a laptop and an Ethernet cable, or a USB Flash drive. With only these supplies, someone could carry out our attacks against a target computer in a matter of minutes.

Q. You show specific brands of computers in your video. Why did you pick these brands? Are these brands especially vulnerable?

A. We studied and filmed the computers we had available to us. They were available to us because we previously decided to buy them for our own use. There is no reason to think these brands of computers are more or less vulnerable than any other brand.

Q. What can vendors do to protect against these attacks?

A. We discuss several potential mitigation strategies in our research paper, though many of these would require hardware modifications or substantial changes to the way disk encryption software is designed and used. The best software-only solution would be to encrypt the disk key with a password whenever the computer enters an inactive state, so that it will not be useful to an attacker even if it is copied from RAM. Unfortunately, this means the computer itself would not be able to access the disk until the user enters the password, so this approach might not be practical when the computer is in certain states, such as at a locked screen saver. (Some disk encryption products, including Microsoft’s BitLocker in “advanced mode,” implement a form of this protection when the computer is powered off or hibernating.)

Q. Have you notified vendors about these problems?

A. We provided advance notice of our findings to several affected vendors.

Q. Where does the name “Lest We Remember” come from?

A. This is the name of a short story by Isaac Asimov published in The Winds of Change and Other Stories.

Q. I don’t use disk encryption. Why should I care?

A. Even if you don’t use disk encryption, various parties may possess your sensitive data and use disk encryption to protect that data. In addition, while disk encryption software is our primary focus, our techniques are useful for recovering other data from your computer, such as sensitive data used to perform online transactions.

Q. Why isn’t screen-locking, suspending, or hibernating the computer good enough?

A. Our attacks rely on the ability to learn the contents of RAM, which contains the key used to encrypt your data. When you lock, suspend, or hibernate your computer, the contents of RAM may be preserved–either in RAM itself or elsewhere–and, if necessary, be made accessible from RAM later without a password or other authentication. Therefore, none of these modes prevent us from recovering the desired contents of RAM. (Exceptions exist; check with the developer of your disk encryption software for further guidance.)


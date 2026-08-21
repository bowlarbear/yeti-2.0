# Frequently Asked Questions

## Q: What is the purpose of this guide?

A: The main purpose of this guide is to provide users with a complete, opinionted and well reasoned start to finish process on setting up a secure Bitcoin Multisig vault that is easy for users with little experience to follow. 

The second purpose of this guide is to educate users on what a secure & well-designed key management system looks like and how all of the design tradeoffs were made. 

## Q: Why Bitcoin Core?

A: Bitcoin Core is the reference implementation for Bitcoin. It is one of the most conservative and highly scrutinized pieces of software in the world. It is the foundational pillar upon which the entire network rests. Linux is also one of the most conservative and highly scrutinized pieces of software in the world. It is the foundational pillar of safe and reliable Open Source Software. By using self installed Linux + Bitcoin Core as our foundation we are striving to create the smallest possible attack surface for secure Bitcoin Storage.

By relying on highly trusted & highly scrutinized software like Bitcoin Core to perform all security critical functions, we can avoid introducing bugs (or attacks) that might slip through less conservative change review processes for software that rewrites critical core functions of a Bitcoin wallet or potentially brings in other unvetted 3rd party libraries and dependencies. 

## Q: Why not use electrum or `<insert software stack>`

A: This guide strives to minimize outside dependencies wherever possible. It is in the professional opinion of the authors that every new outside dependency introduced to a Bitcoin self custody stack significantly increases the attack surface of that solution. 

Thus, while it might make for a more convenient or pretty user experience to introduce other software apps or hardware devices, we are quite confident that it is considerably more secure to avoid them whenever possible. Less is more in this case. 

## Q: Is it possible for Bitcoin Core or Linux to have unknown vulnerabilities?

A: Yes, of course. Software is written by humans and humans are prone to make mistakes. Some humans even do bad things on purpose. This is the entire premise behind our design philosophy. Minimize trusted third parties, minimize dependecies, lean heavily on the most trusted and heavily scrutinized software with the best change control processes. Keep it lean and keep it tight.


## Q: Why not use hardware wallets?

A: The answer to this question is largely the same as above. We do not support hardware wallets when making self custody recommendations because they introduce a significant software & hardware supply chain attack surface when compared to self installed Linux + Bitcoin Core.

The fewer trusted 3rd parties you put between yourself and your Bitcoin, the better.

## Q: Why don't you have users write down seed phrases?

A: You are likely thinking of human readable 12 or 24 word seed phrases (AKA BIP 39 seed phrases). Many Bitcoin wallets use these and instruct people to write down the words on paper or even steel. 

We do not use these seed phrases because Bitcoin Core does not support them.

Bitcoin native multisig on Bitcoin Core makes use of Bitcoin script embedded in the wallet descriptors and WIF keys. The logic embedded in wallet descriptors for a multsig vault is insperable from the keys and not compatible with something like the non standard BIP 39 seed phrase stamped into steel.

Beyond this we believe that from a risk analysis perspective, considering the lindy and network effects of systems, CD/DVD form factor (on the proper medium which for us is Millenniata M-disc) and wallet backups in the standardized Bitcoin Core WIF & wallet descriptor format is far less likely to become deprecated over the coming decades than something like BIP 39 seeds etched into steel.

## Q: Why M-discs specifically?

A: All of the backups created with yeti-2.0 are written to Millenniata M-discs, which are carbon coated, resistant to oxidation, and heat resistant. These discs are specially designed for long term, archival data storage. Under the proper storage conditions the manufacturer claims they will last up to 1,000 years. Verbatim brand Ultralife Gold Archival grade DVDs 4.7GB are also a good option, Verbatim claims these discs will last up to 100 years under proper storage conditions. 

You should still check your backups periodically and redo them probably at least once every 10 years or so to be safe, this would be a good strategy with any key management stack. If the storage conditions for some of your backups are less ideal (heat or direct sunlight or moisture) you should check those backups more frequently.

## Q: If you don't write down seed words, how can you ensure key backups are safe?

A: This is one of the main reasons why we use multisig. Multisig provides all of the benefits of having multiple backups of a single sig seed phrase. If an attacker gets a hold of a single copy of your seed phrase backup, they will be able to steal all of your Bitcoin, but with multisig this is not the case. All key backups create with this guide are backed up on M-discs, which are sufficient given the inherent redundancy of a proper multisig vault. 

You CAN write down your wallet descriptor and all of your xprivs and xpubs by hand if you really want to, but we don't reccomend it in the guide becuase it's painful and prone to human error and entirely unecessary if you follow the guide as written.

A much better way to add redundancy rather than hand writing wallet descriptors would be to buy both Millenniata M-discs and Verbatim gold DVDs. These are on a similar tier of archival grade storage quality as M-disc, then you could make two backups of each key. For example, key_1 & multisig watch wallet would get backed up on 1 M-disc and 1 Gold DVD, both would be marked with the number 1, and both would then be placed into the same envelope.

## Q: Why can't I provide my own entropy with dice rolls or coin flips?

A: Because you don't need to do this, Linux + Bitcoin Core are much better at generating secure random entropy than humans. If you do not trust the software stack to do it properly, then you should also not trust the software stack to properly use the entropy you provide it. 

Well-designed free and open source software like Linux, Bitcoin Core, OpenSSH, OpenSSL, GPG/PGP etc. use OS level entropy from several independent sources including the Operating System CSPRNG, Hardware RNG from the CPU, and dynamic environment data and user activity. This is the sotware industry standard for creating secure private keys.

If you feel that you cannot trust these things then you should know you also cannot trust this guide, the website it is on, or any software you download via the internet. Web pages, signed software binaries like Bitcoin core, GitHub commits are all examples of things that are signed with computer generated private keys. 

## Q: Why is the vault a 3 of 7? Why not a 2 of 5 or an M of N?

A: We feel that 7 backups is a good number. It's enough to provide plenty of redundancy in most conceivable SHTF scenarios. With 7 keys in the multisig you can lose 4 and still retain access to your money. We have more than double the amount of keys in the qourum than are required to reach signing threshold. 

We also feel that 3 is an appropriate signer threshold because an attacker could theoretically get access to 1 key, perhaps with concerted effort even 2, but 3 is going to be considerably more difficult. 3 of 7 is sufficiently difficult and expensive for a bad actor to retrieve.

## Q: How can I be sure that all keys were deleted off of the offline computer?

A: The offline computer runs off the Ubuntu Live System on the Linux USB, this process happens entirely within RAM which is volatile memory. Additionally the guide ensures we disable any swap partitions.

If you want to ensure beyond reasonable doubt that everything on the offline computer is destroyed, the best thing to do is destroy the computer after you test your backups, but this is unnecessary.

If you would prefer to avoid destroying the computer but still want more assurance, then before beginning step A5, first remove any internal storage drives from the offline computer. This process will vary depending on the model, but you should remove both the internal SATA drive and the NVMe drive (some computers will only come with one or the other, some will come with both).

After you've removed all internal storage drives, put the computer back together and then continue with step A5, but this is also unnecessary.

## Q: What should I do if I need to store more than $5M in Bitcoin?

A: As the amount of value you are securing in a Bitcoin multisig vault grows, it would be wise to take additional steps to enhance security and privacy. At present we are not comfortable recommending this guide for amounts beyond $5M, higher amounts will require a more scrutinized solution such as encrypted wallet descriptors and keys generated on multiple different computers. We do not presently have a guide published for this. If this is you, yeti-2.0 is not the right solution.

## Q: What is the biggest weakness of Yeti-2.0?

A: We believe the biggest weakness of this guide is that the wallet descriptors are not encrypted. This means that anyone who gets access to one of your wallet backups and knows how to load a descriptor, will be able to see your wallet balance. There are ways to mitigate this risk, but it is outside the scope of this guide. The privacy & security that you get from this set up is still an order of magnitude better than what you get from most competing bitcoin self custody stacks.

## Q: Why not encrypt the backup discs?

A: You absolutely CAN do this but it's tricky because when you encrypt private keys you've effectively recreated the key management problem for yourself. There is a proper way to do this and it requires careful execution. It is outside the scope of this guide. 

The proper way to encrypt backup discs such that no snoop could ever get ahold of just one of your backups and learn your entire wallet history, would be to use layered multisig, where the wallet itself lives behind another 3 of 7 key encryption scheme. However setting this up introduces a lot more complexity and careful design consideration, thus it's out of scope.  

## Q: Why not generate all of the keys on different computers?

A: You absolutely can do this too, although its also outside the scope of this guide. Generating each key on a different computer would make the vault more secure, but it adds considerably to the complexity of setup and to the time and hardware requirement. Doing this would require the user have 6 additional computers, and constructing the multisig vault and then properly backing up the wallet descriptor would require atleast 15 data transfers across all of these devices. There is a middle ground where you can just use 1 or 2 additional computers but it still adds logistic complexities.

This would be more appropriate in very high security scenarios (vaults designed for storing >$5M), but we think generating all 7 keys on one machine is good enough for the purposes of this guide, because all of modern computing is built upon the assumption that computers can generate sufficiently random (and private) secret keys with the use of good cryptography, which we get with the dedicated laptop + Linux + Bitcoin Core.

## Q: Are there any other ways to improve this that are out of scope?

A: There is always room for improvement. The biggest one that comes to mind outside of the two optimizations discussed above would be, when signing a PSBT, only loading 1 key per boot session, and transerring the PSBT to a new boot session after signing with 1 key. This would ensure that after key creation, a signing threshold of keys would never exist all at once on a single signing device's boot session. 

However, this adds logistical complexity to the user experience because it requires more boot sessions and more data transfers when spending from the vault. Nothing is stopping you from doing this but it would require deviating from the instructions and terminal commands in the guide slightly.

Another idea that comes to mind would be improving the privacy of the onchain footprint with tools like tapscript.

## Q: What should I do if one of my backups gets lost/broken/stolen/tampered with?

A: If you suspect that someone has tampered with one of your backups or if one of your backups get damaged or stops working the best course of action would be to set up a new multisig vault and move all funds into the new vault. Be very cautious that you do not mix up old keys with new keys when doing this. It would be best to mark all of the old keys in some way first, before setting up the new ones.

If you have reason to believe that one of your backups has been tampered with, you should avoid using that particular backup disc when sweeping the funds into a new multisig vault unless there are no other options.

One optional measure to help protect backups from physical damage is to keep each one in it's own CD jewel case, and keeping each case inside of a padded envelope, for most situations this is likely unnecessary.

## Q: Can I set up this vault with my own M of N scheme?

A: This guide is free and open source. You can do whatever you want, and we can't stop you. Our recommendations are what they are, because our goal was to deliver an opinionated guide that doesn't shift the burden of considering key management design tradeoffs onto end users. As written, the guide instructs the user explicitly how to create and use a 3 of 7 scheme only.

## Q: Can I set this up just to test it and then set it up a second time to use it for real?

A: Yes, if you follow this guide carefully there is no reason why you couldn't just run through the setup twice, the first time as a test and then the second time for real. The advantage to doing this is not having to set up the computer infrastructure a second time, so it will be shorter and easier.

We recommend testing with CD-R instead of M-disc if you are playing around because M-discs can be more expensive. DO NOT use CD-R for a serious attempt at storing money.

After you complete your test run, simply delete your "multisig_watch_wallet" from `/.bitcoin/wallets` folder on your online machine. Then reboot the offline machine to clear out its memory and run through the guide a second time but with M-Discs. 

## Q: What happens if I lost my node or if I need to set up a new one?

A: This is not a problem with our setup. You can easily follow steps A1-A4 to set up a new node. You will find that your watch only wallet descriptor is backed up on each of the 7 M-discs.

To load this watch only wallet descriptor back into your node, boot into your offline machine like you normally would, insert one of your key backup M-discs and copy the "multisig_watch_wallet" onto your transer USB, then transfer this wallet onto your node and load it into Bitcoin Core as explained in step B5. 

## Q: Can I use Tor for this? Can I use TAILS for the offline signer OS?

A: You certainly could do these things. For the sake of keeping the main guide tight we opted not to include TOR as this will considerably slow down the Initial Block Download. Tails was considered for the offline signer but we ultimately determined the time spent creating a second bootable live system wasn't worth it when we already had an ubuntu live system which meets our needs sufficiently. 

These would be potential subguide ideas if you are interested in contributing. Obviously any reccomendations to users need to be thorough & properly end to end tested within the context of the main guide, see the [contribution guide](contributions.md) for more information.

## Q: Can I use assumeutxo to speed up the IBD on the full node?

A: Yes you can do this, however, if you follow the guide as written this will not actually help you. The reason for this is because even with assumeutxo you cannot actually use the loadwallet command until the full background validation completes, which is the equivalent to full IBD anyway. We create our multisig descriptor on the offline machine in order to avoid making the experience more cumbersome, this means you need a fully synced node in order to import that descriptor. 

In the mean time you could export the xpubs on the transfer USB, and reconstruct the multisig wallet on the offline machine using the createwallet command to benefit from the speed up of assumeutxo if you wanted to, but that's out of scope for this guide.

## Q: What if I do not want to use a pruned node?

A: You do not need to use a pruned node. Pruning is included in the guide to keep equipment costs low for users, as a full archival node is necessary for our purposes.

If wish to avoid pruning, your full node (the online computer) must have atleast 2TB of internal storage space. You can either buy a computer with sufficient storage included or you can purchase a 2TB 2.5 inch SATA SSD for around $250 (as of this post) and replace the internal storage drive on the full node computer. 2TB of internal storage will last about 10 years (as of this post) before it needs to be upgraded to acccomdate the size of the blockchain.

Upgrading your internal storage drive usually requires removing the screw on the bottom of the laptop, removing the old drive and then inserting the new one and then replacing the cover. This is not a particularly difficult process, however, the exact steps will depend upon your computer model.

If you are upgrading the storage on an existing full node you will have to reinstall ubuntu and then resync the bitcoin blockchain from scratch (unless you have a way to copy the block data you've already downloaded to the new drive).

You can also use a 2TB NVME drive if you prefer this over SATA. The easiest thing to do, if your computer supports both form factors, would be to pick either SATA or NVME and ensure there is only 1 storage drive inside the computer. 

If your online computer contains both a SATA drive and an NVME drive and if you install ubuntu on the drive you are not intending to use for storage, you may need to manually mount the second storage drive from within linux. You will then need to configure your data directory within either the `~.bitcoin/Bitcoin.conf` or using bitcoin-cli flags (`-datadir=/path/to/storage`) in order to use it for your block data. If you install Ubuntu on the same drive you intend to use for storage this should not be an issue.


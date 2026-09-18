# Frequently Asked Questions

## Q: What is the purpose of this guide?

A: The main purpose of this guide is to provide users with a complete, opinionated and well-reasoned start-to-finish process on setting up a secure Bitcoin Multisig vault that is easy for users with little experience to follow. 

The second purpose of this guide is to educate users on what a secure & well-designed key management system looks like and how all of the design tradeoffs were made. 

## Q: Why Bitcoin Core?

A: Bitcoin Core is the reference implementation for Bitcoin. It is one of the most conservative and highly scrutinized pieces of software in the world. It is the foundational pillar upon which the entire network rests. Linux is also one of the most conservative and highly scrutinized pieces of software in the world. It is the foundational pillar of safe and reliable Open Source Software. By using self-installed Linux + Bitcoin Core as our foundation we are striving to create the smallest possible attack surface for secure Bitcoin Storage.

By relying on highly trusted, highly scrutinized, & highly audited software like Bitcoin Core to perform all security critical functions, we can avoid introducing bugs (or attacks) that might slip through less conservative change review processes for software that rewrites critical core functions of a Bitcoin wallet or potentially brings in other unvetted 3rd party libraries and dependencies. 

## Q: Why not use electrum or `<insert software stack>`

A: This guide strives to minimize outside dependencies wherever possible. It is in the professional opinion of the authors that every new outside dependency introduced to a Bitcoin self custody stack significantly increases the attack surface of that solution. 

Thus, while it might make for a more convenient or pretty user experience to introduce other software apps or hardware devices, we are quite confident that it is considerably more secure to avoid them whenever possible. Less is more in this case. 

## Q: Is it possible for Bitcoin Core or Linux to have unknown vulnerabilities?

A: Yes, of course. Software is written by humans and humans are prone to make mistakes. Some humans even do bad things on purpose. This is the entire premise behind our design philosophy. Minimize trusted third parties, minimize dependencies, lean heavily on the most trusted and heavily scrutinized software with the best change control processes and independent build audits. Keep it lean and keep it tight.


## Q: Why not use hardware wallets?

A: The answer to this question is largely the same as above. We do not support hardware wallets when making self custody recommendations because they introduce a significant software & hardware supply chain attack surface when compared to self-installed Linux + Bitcoin Core.

The fewer trusted 3rd parties you put between yourself and your Bitcoin, the better.

## Q: Don't generic laptops also have some supply chain risk?

A: Yes, they do. The more appropriate question is what are the range of options available to us and how do they compare? All else being equal, specialty hardware designed and sold for use with Bitcoin is significantly easier and cheaper for attackers to target across the supply chain than generic hardware sold for general purpose use.  

This does not mean there can never be firmware or hardware bugs in generic computers or evil mail men delivering laptops, but it does mean these bugs and hazards are far less likely to result in the loss of Bitcoin. We cannot be absolutely certain (without expensive specialized equipment and expert knowledge) that computer chip foundries haven't backdoored every computer, but generic hardware is the most reasonable solution we have.

The metadata collection in the vendor distribution of specialty hardware is also a privacy & security risk. If your email or name and address end up in a database leak associated with specialty Bitcoin hardware, you make yourself a desirable target. 

## Q: What happens if I mess something up while following this guide?

A: If you follow this guide from start to finish, setup mistakes will not cost your real funds. An error usually means you stop, go back a few steps and retry. The worst case during setup is lost time, a few wasted discs, or a small test deposit stuck in an unfinished vault. That is why this guide has you test end to end with a small amount before moving anything that matters into it. 

Once that test spend and recovery have been properly completed, you are using the fully configured vault. Any risk from that point forward is operational: keep the keys and verify what you sign.

## Q: Why don't you have users write down seed phrases?

A: You are likely thinking of human readable 12 or 24 word seed phrases (AKA BIP 39 seed phrases). Many Bitcoin wallets use these and instruct people to write down the words on paper or even steel. 

We do not use these seed phrases because Bitcoin Core does not support them.

Bitcoin native multisig on Bitcoin Core makes use of Bitcoin script embedded in the wallet descriptors and WIF keys. The logic embedded in wallet descriptors for a multisig vault is inseparable from the keys and not compatible with something like the non standard BIP 39 seed phrase stamped into steel.

Beyond this, we back up in Bitcoin Core's WIF and wallet descriptor formats so recovery does not depend on a mnemonic standard Core does not implement. We use archival grade optical discs and refresh them every 7-10 years so the copy itself does not rot. We use the CD/DVD form factor because it is a long-stable spec and is readable with cheap, generic drives. This means recovery does not depend on Bitcoin-specific hardware devices.

## Q: Why M-Discs specifically?

A: All of the backups created with yeti-2.0 are written to Millenniata M-Discs, which are carbon coated, resistant to oxidation, and heat resistant. These discs are specially designed for long term, archival data storage. Under the proper storage conditions the manufacturer claims they will last up to 1,000 years. Verbatim brand Ultralife Gold Archival grade DVDs 4.7GB are also a good option, Verbatim claims these discs will last up to 100 years under proper storage conditions. 

You should still check your backups periodically and refresh the backups at least once every 7-10 years to be safe, this would be a good strategy with any key management stack. If the storage conditions for some of your backups are less ideal (heat or direct sunlight or moisture) you should check those backups more frequently.

## Q: If you don't write down seed words, how can you ensure key backups are safe?

A: This is one of the main reasons why we use multisig. Multisig provides all of the benefits of having multiple backups of a single sig seed phrase. If an attacker gets a hold of a single copy of your seed phrase backup, they will be able to steal all of your Bitcoin, but with multisig this is not the case. All key backups created with this guide are backed up on M-Discs, which are sufficient given the inherent redundancy of a proper multisig vault. 

You CAN write down your wallet descriptor and all of your xprivs and xpubs by hand if you really want to, but we don't recommend it in the guide because it's painful and prone to human error and entirely unnecessary if you follow the guide as written.

A much better way to add redundancy rather than hand writing wallet descriptors would be to buy both Millenniata M-Discs and Verbatim gold DVDs. These are on a similar tier of archival grade storage quality as M-Disc, then you could make two backups of each key. For example, key_1 & multisig watch wallet would get backed up on 1 M-Disc and 1 Gold DVD, both would be marked with the number 1, and both would then be placed into the same envelope.

## Q: Why can't I provide my own entropy with dice rolls or coin flips?

A: You can. Well-designed free and open source software like Linux, Bitcoin Core, OpenSSH, OpenSSL, GPG/PGP etc. uses OS level entropy from several independent sources including the Operating System CSPRNG, Hardware RNG from the CPU, dynamic environment data and user activity. This is the software industry standard for creating secure private keys.

If you wish to add your own entropy to this entropy pool, say the result of 100 dice rolls, you can simply type the result of each dice roll onto your keyboard before using Bitcoin Core to generate your keys. This activity is captured by the operating system and added to your entropy pool.

There is no need to do this, Linux & Bitcoin Core are better at generating secure random entropy than humans, but you cannot hurt your entropy pool by adding to it in this way.

If you feel that you cannot trust these things then you should know you also cannot trust this guide, the website it is on, or any software you download via the internet. Web pages, signed software binaries like Bitcoin Core, and GitHub commits are all examples of things we trust because they are signed with computer generated private keys. 

## Q: Why is the vault a 3-of-7? Why not a 2-of-5 or an M-of-N?

A: We feel that 7 backups is a good number. It's enough to provide plenty of redundancy in most conceivable SHTF scenarios. With 7 keys in the multisig you can lose 4 and still retain access to your money. We have more than double the amount of keys in the quorum than are required to reach signing threshold. 

We also feel that 3 is an appropriate signer threshold because an attacker could theoretically get access to 1 key, perhaps with concerted effort even 2, but 3 is going to be considerably more difficult. 3-of-7 is sufficiently difficult and expensive for a bad actor to retrieve.

## Q: How can I be sure that all keys were deleted off of the offline computer?

A: The offline computer runs off the Ubuntu Live System on the Linux USB, this process happens entirely within RAM which is volatile memory. Additionally the guide ensures we disable any swap partitions.

If you want to ensure beyond reasonable doubt that everything on the offline computer is destroyed, the best thing to do is destroy the computer after you test your backups, but this is unnecessary.

If you would prefer to avoid destroying the computer but still want more assurance, then before beginning step A5, first remove any internal storage drives from the offline computer. This process will vary depending on the model, but you should remove both the internal SATA drive and the NVMe drive (some computers will only come with one or the other, some will come with both).

After you've removed all internal storage drives, put the computer back together and then continue with step A5, but this is also unnecessary.

## Q: What should I do if I need to store more than $5M in Bitcoin?

A: As the amount of value you are securing in a Bitcoin multisig vault grows, it would be wise to take additional steps to enhance security and privacy. At present we are not comfortable recommending this guide for amounts beyond $5M, higher amounts will require a more scrutinized solution such as encrypted wallet descriptors and keys generated on multiple different computers. We do not presently have a guide published for this. If this is you, yeti-2.0 is not the right solution.

## Q: What is the biggest weakness of Yeti-2.0?

A: We believe the biggest weakness of this guide is that the wallet descriptors are not encrypted. This means that anyone who gets access to one of your wallet backups and knows how to load a descriptor, will be able to see your wallet balance. There are ways to mitigate this risk, but it is outside the scope of this guide. The privacy & security that you get from this setup is still considerably better than stacks that add Bitcoin-specific vendor hardware and extra wallet software.

## Q: Why not encrypt the backup discs?

A: You absolutely CAN do this but it's tricky because when you encrypt private keys you've effectively recreated the key management problem for yourself. There is a proper way to do this and it requires careful execution. It is outside the scope of this guide. 

The proper way to encrypt backup discs such that no snoop could ever get ahold of just one of your backups and learn your entire wallet history, would be to use layered multisig, where the wallet itself lives behind another 3-of-7 key encryption scheme. However setting this up introduces a lot more complexity and careful design consideration, thus it's out of scope.  

## Q: Why not generate all of the keys on different computers?

A: You can generate each key on a different computer, but that is outside the scope of this guide. It would make the vault more secure, and it would also add a lot of set up cost: as many as six extra machines and at least fifteen data transfers to build the multisig and back up the descriptor. Using one or two extra computers is a middle ground, and it still adds logistical complexity. 

This would be more appropriate in very high security scenarios (vaults designed for storing >$5M). This guide generates all seven keys on one dedicated offline machine. That machine is a generic computer with a self-installed, verified copy of Linux, used only for this process and kept offline. Key generation is done by Linux and Bitcoin Core, not by extra Bitcoin-specific firmware or libraries. 

Modern computing is built on the assumption that computers can generate secrets that are random and private enough to trust. That job is done by a CSPRNG. People often distrust it because they have seen the results of poorly reviewed wallet software. This is a good reason to avoid less scrutinized hardware devices and wallet software. It is not a good reason to treat a verified Linux + Bitcoin Core setup as unable to safely generate keys.

We accept one dedicated offline machine to generate our keys because of how we configure the machine for that task in the guide. 

## Q: Are there any other ways to improve this that are out of scope?

A: There is always room for improvement. The normal spending path now keeps the signing quorum geographically separated: sign with one key per boot session and move the partially signed PSBT to the next key location. Separate signer devices or independent people provide stronger isolation still.

Another idea that comes to mind would be improving the privacy of the onchain footprint with tools like tapscript or FROST. Presently the wallet script yeti-2.0 uses gets revealed on chain when you spend coins from the multisig wallet. Our script is unique enough that it does mean casual observers can track how much money moves out of yeti-2.0 wallets on chain. 

We use what is called a wrapped script hash, each address has its own unique script hash, and a single spend only publishes the child keys at that one index. Nobody can derive other addresses for your wallet from one revealed script. In plain english that means someone can only see that you were using yeti scripts onchain after you spend. 

The first and most important step to protect your privacy under these constraints is to never re-use a Bitcoin address.

Considering yeti-2.0 does not and can not collect any information about its users, unlike hardware vendors and collaborative-custody services that take shipping, billing, or identity data, there is no trusted third party here to receive it and associate you as a Yeti user. We believe these are acceptable tradeoffs in their present form for the security model this guide was built around.

## Q: What should I do if one of my backups gets lost/broken/stolen/tampered with?

A: If you suspect that someone has tampered with one of your backups or if one of your backups gets damaged or stops working the best course of action would be to set up a new multisig vault and move all funds into the new vault. Be very cautious that you do not mix up old keys with new keys when doing this. It would be best to mark all of the old keys in some way first, before setting up the new ones.

If you have reason to believe that one of your backups has been tampered with, you should avoid using that particular backup disc when sweeping the funds into a new multisig vault unless there are no other options.

One optional measure to help protect backups from physical damage is to keep each one in its own CD jewel case, and keeping each case inside of a padded envelope, for most situations this is likely unnecessary.

## Q: Can I set up this vault with my own M of N scheme?

A: This guide is free and open source. You can do whatever you want, and we can't stop you. Our recommendations are what they are, because our goal was to deliver an opinionated guide that doesn't shift the burden of considering key management design tradeoffs onto end users. As written, the guide instructs the user explicitly how to create and use a 3-of-7 scheme only.

## Q: Can I set this up just to test it and then set it up a second time to use it for real?

A: Yes, if you follow this guide carefully there is no reason why you couldn't just run through the setup twice, the first time as a test and then the second time for real. The advantage to doing this is not having to set up the computer infrastructure a second time, so it will be shorter and easier.

We recommend testing with CD-R instead of M-Disc if you are playing around because M-Discs can be more expensive. DO NOT use CD-R for a serious attempt at storing money.

After you complete your test run, simply delete your "multisig_watch_wallet" from `~/.bitcoin/wallets` folder on your online machine. Then reboot the offline machine to clear out its memory and run through the guide a second time but with M-Discs. 

## Q: What happens if I lost my node or if I need to set up a new one?

A: This is not a problem with our setup. You can easily follow steps A1-A4 to set up a new node. You will find that your watch-only wallet descriptor is backed up on each of the 7 M-Discs.

To load this watch-only wallet descriptor back into your node, boot into your offline machine like you normally would, insert one of your key backup M-Discs and copy the "multisig_watch_wallet" onto your transfer USB, then transfer this wallet onto your node and load it into Bitcoin Core as explained in step B5. 

## Q: What would happen to my Bitcoin if this guide disappears tomorrow?

A: Nothing would happen to your Bitcoin. The advantage of Yeti-2.0 is that you do not need this guide to spend or receive with the wallet. The only things you absolutely need to spend your Bitcoin are the official Bitcoin reference client software, Bitcoin Core, and an Optical Media drive capable of reading DVDs. 

It's still a good idea to keep a copy of the README on each key backup disc for reference, but this guide is not a single point of failure. A reasonably competent person could spend a few hours reading the Bitcoin Core API documentation and figure out how to send and receive Bitcoin from the multisig wallet configured with this guide all on their own.

## Q: Can I use Tor for this? Can I use TAILS for the offline signer OS?

A: You certainly could do these things. For the sake of keeping the main guide tight we opted not to include Tor as this will considerably slow down the Initial Block Download. Tails was considered for the offline signer but we ultimately determined the time spent creating a second bootable live system wasn't worth it when we already had an Ubuntu live system which meets our needs sufficiently. 

These would be potential subguide ideas if you are interested in contributing. Obviously any recommendations to users need to be thorough & properly end to end tested within the context of the main guide, see the [contribution guide](contributions.md) for more information.

## Q: Why not keep the offline signer permanently offline? 

A: You can do this, you could keep a copy of the Bitcoin software on a USB stick and import it to your offline computer each time you boot it so as to avoid temporarily connecting to a network to redownload Bitcoin Core.

There are several reasons we opted not to do this. The first reason is because it's good practice to always use the latest version of the software. If a user has an older version of the software stored on a USB stick, they may not update the software before using their signer. 

The second reason is related to the first, in that the scripts provided in this guide assume the user is running the latest release of Bitcoin Core. If the user has Bitcoin-31.1 on their USB stick and the scripts in the guide are updated to Bitcoin-31.2, suddenly those scripts will no longer work for the user, and it may be unclear why. 

The third reason is because permissions conflicts can occur on Linux when you import software like Bitcoin Core rather than downloading it directly, accounting for these potential permissions conflicts was deemed undesirable.

We believe these tradeoffs are acceptable. Key material never touches the device while it is network enabled. Creating your offline signer from scratch each time instead of keeping it for later reduces attack surface by ensuring key material never persists. It also reduces complexity by reducing the amount of materials users need to track in between uses. 

However, if you prefer to keep your offline signer always offline after initial keygen and backup, this would not require much additional work. Keep a copy of Bitcoin Core on a USB stick and import it to your offline machine, be prepared to troubleshoot any issues that might arise with scripts or permissions from doing so, these issues are not insurmountable, and a complete process for doing so could be added as a subguide.

## Q: Can I use assumeutxo to speed up the IBD on the full node?

A: Yes you can do this, however, if you follow the guide as written this will not actually help you. The reason for this is because even with assumeutxo you cannot actually use the loadwallet command until the full background validation completes, which is the equivalent to full IBD anyway. We create our multisig descriptor on the offline machine in order to avoid making the experience more cumbersome, this means you need a fully synced node in order to import that descriptor. 

In the meantime you could export the xpubs on the transfer USB, and reconstruct the multisig wallet on the offline machine using the createwallet command to benefit from the speed up of assumeutxo if you wanted to, but that's out of scope for this guide.

## Q: Can I use a pruned node?

A: You can use a pruned node but it is not recommended. Pruning will help lower equipment costs for this guide, and a full archival node is not technically a requirement for our purposes. Pruning is perfectly safe from a security perspective, however it can lead to annoying circumstances where you may have to resync the bitcoin blockchain from scratch when loading a wallet. The authors of this guide prefer not to use pruned nodes for this reason.

For more information see the [pruning subguide](pruning.md).

## Q: What to do about this error on my pruned node: "wallet loading failed. Prune: last wallet synchronization goes beyond pruned data." when importing the "multisig_watch_wallet" in step B5?

A: See [this solution](pruning.md#error-wallet-loading-failed-prune-last-wallet-synchronization-goes-beyond-pruned-data) in the pruning subguide.

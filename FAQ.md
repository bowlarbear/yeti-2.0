# Frequently Asked Questions

## Q: Why Bitcoin Core?

A: Bitcoin Core is one of the most conservative and highly scrutinized pieces of software in the world. Linux is also one of the most conservative and highly scrutinized pieces of software in the world. By using self installed Linux + Bitcoin Core as our foundation we are striving to create the smallest possible attack surface for secure Bitcoin Storage.

By relying on highly trusted & highly scrutinized software like Bitcoin Core to perform all security critical functions, we can avoid introducing bugs (or attacks) that might slip through less conservative change review processes for software that rewrites critical core functions of a Bitcoin wallet.

## Q: why not use electrum or `<insert software stack>`

A: This guide strives to minimize outside dependencies wherever possible. It is in the professional opinion of the authors that any outside dependencies introduced to a self custody solution significantly increases the attack surface of that self custody solution. 

Thus, while it might make for a more convenient or pretty user experience to introduce other software apps, we are quite confident that it is considerably more secure to avoid them.

## Q: Why not use hardware wallets?

A: The answer to this question is largely the same as above. We do not support hardware wallets when making self custody recommendations because they introduce a significant software & hardware supply chain attack surface when compared to self installed Linux + Bitcoin Core.

The fewer trusted 3rd parties you put between yourself and your Bitcoin, the better.

## Q: Why don't you have users write down seed phrases?

A: You are likely thinking of human readable 12 or 24 word seed phrases (AKA BIP 39 seed phrases). Many Bitcoin wallets use these and instruct people to write down the words on paper or even steel. 

We do not use these seed phrases because Bitcoin Core does not support them.

## Q: If you don't write down seed words, how can you ensure key backups are safe?

A: This is one of the main reasons why we use multisig. Multisig provides all of the benefits of having multiple backups of a single sig seed phrase. If an attacker gets a hold of a single copy of your seed phrase backup, they will be able to steal all of your Bitcoin, but with multisig this is not the case.

## Q: Why M-discs specifically?

A: All of the backups created with yeti-2.0 are written to Milleniata M-discs, which are carbon coated, resistant to oxidation, and heat resistant. These discs are specially designed for long term data archival storage, and under proper storage conditions they are rated by the manufacturer to last up to 1,000 years.

Although you should still check your backups periodically and redo them probably atleast once every 10 years or so to be safe. 

## Q: What should I do if I need to store more than $5M in Bitcoin?

A: As the amount of value you are securing in a Bitcoin multisig wallet grows, it would be wise to take additional steps to enhance security and privacy. At present we are not comfortable recommending this guide for amounts beyond $5M, higher amounts will require a more scrutinized solution such as encrypted wallet descriptors and keys generated on multiple different computers. We do not presently have a guide published for this. If this is you, yeti-2.0 is not the right solution.

## Q: How can I be sure that all keys were deleted off of the offline computer?

A: The offline computer runs off the Ubuntu Live System on the Linux USB, this process lives entirely within RAM.

If you want to ensure beyond reasonable doubt that everything on the offline computer is destroyed, the best thing to do is destroy the computer after you test your backups.

If you would prefer to avoid destroying the computer, then before beginnin step A5, first remove any internal storage drives from the offline computer. This will vary depending on the model, but you should remove both the internal SATA drive and the NVME drive (some computers will only come with one or the other, some will come with both).

After you've removed all internal storage drives, put the computer back together and then continue with step A5. 

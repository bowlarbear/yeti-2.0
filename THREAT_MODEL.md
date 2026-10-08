# Threat model

This vault reduces the common ways people lose bitcoin in
self-custody: remote compromise of keys (including keys that
never touched the internet), physical theft of a single backup,
loss of a single backup, an heir who cannot reconstruct the
wallet, and a vendor layer the reference client does not speak.

It is not a hardware-wallet product. It is not a regulated custodian.
It is Bitcoin Core on dedicated computers, with keys on archival discs.

The risks this vault is for are in “What the design is trying to stop.” 
Operator effort is not one of those risks. Friction is accepted. 
Operator error is a different claim: a skipped check can lose coins. 
That fact is not unique to this README, and it is not a reason to 
pick a vendor stack. Advisor context is in [CONTEXT_FOR_ADVISORS.md](CONTEXT_FOR_ADVISORS.md).

Read this with the [FAQ](FAQ.md). The [README](README.md) is the
procedure. The assurances below apply to that procedure as written.

## Assets

- The 3-of-7 multisig coins
- Seven key backups on archival discs
- The watch-only descriptor
- The online node and the offline signer

## What the design is trying to stop

**Remote theft of keys.**
Keys can be stolen without anyone touching a backup and without the
owner sending a transaction. A wallet is not “cold” if the software
in the stack that created or used the keys was wrong.

This guide treats an unverifiable blob in the key-generation or
signing chain as malware. That includes vendor firmware, vendor
apps, coordinators, and libraries the owner cannot inspect or rebuild
in practice. Any one of those binaries can steal. A coordinator can
build a bad PSBT or serve a malicious descriptor. This guide's 
coordinator is Core, and the nonce is Core's. A bias in that nonce 
is a break in the binary already chosen. It is not a path a third party 
opens. A vendor signer is not that binary. A third-party coordinator 
is not that coordinator. Adding either one is more surface. On that
surface a coordinator can bias the nonce a vendor device uses and read 
the key from the chain. Two vendor keys by that path, and one Core key 
back over the pipe, is a 3-of-7. An attacker is not limited to one path. 
Owning the coordinator is the seat. Given time, the cheaper keys are
combined until the count is three. A Core majority makes the pipe the 
cheap Core key. A vendor majority makes the vendor key the cheap one. 
Neither choice removes the coordinator that made the mix possible. 
More surface is the vector. The pipe is not hardened against that return. 
The Core key still needs code on the signer. The vendor keys do not.
Applying the Core nonce fact to that stack is a category error. A single bad
crypto library can steal on its own. Vendors and coordinators already
ship as pairs. The model assumes they can act as a pair.

Any Bitcoin-specific signer can ship that class of failure. The 2026
Coldcard default-seed incident is the public case, not a unique one:
guessable keys from the device’s normal new-seed path, coins swept
from the public chain, no phishing, no stolen device, and a firmware
update that did not repair old seeds. Public source did not help if
the path that actually ran was not the path people thought they had
audited.

A nation-state is not required for this class, and a state is not
excluded. It is what happens when a product built to hold bearer 
bitcoin ships software nobody sufficiently reviewed. The owner has 
no recourse. “It was a bug” is enough cover whether the failure was 
sloppy or not. Shipping and support databases leak. This class of 
failure is not reserved for rare attackers.

This vault creates and uses keys on a dedicated offline computer
running a clean Ubuntu install and Bitcoin Core. Keys are not stored
on the online node. Extra wallet apps and vendor firmware are out of
the stack on purpose.

Industry copy overweights physical extraction and underweights
remote theft. A well-funded attacker can eventually break an
extraction barrier. Remote malware is cheaper and scales across
many devices at once. The 2026 sweeps did not need the device.

Dice kits and offline entropy pages are not a competing vault.
They sit on top of some other stack. The operator still needs a
machine, an import path, backups, and a signer. The mapping code is
extra software with less review than Core. Watching rolls does
not attest that program. Dice-first is not a smaller key-birth
surface than Guix-attested Core. It is Core’s surface plus the
converter. A person cannot be the whole entropy pool and cannot
audit Core’s generator with a calculator.

Roll-your-own entropy makes the operator the single point of
failure for that execution. Paper is not in Core’s test set.
The sheet cannot be published and the secret kept. Libsecp and
Core’s generator are the reviewed function, shipped as an
attested binary. A side calculator is not that function. An
xpub match against Core does not bind the rolls.

This guide does not mix them in. Keys are generated by Bitcoin
Core on the clean offline machine.

Extra tools sold as a check of Core are the same class. A
coordinator, a seed utility, or a dice page is not a second
audit of Core. Checks of Core happen in Core.
A mapper is another program that can steal or leak.
A pinned page still computes after the last roll. That is a
second hidden draw, not a smaller one.
A hashed HTML or WASM file still runs under a browser engine.
Signatures do not attest that engine.
"One HTML file" is the delivery format. Compiled code
embedded in that file is still a binary. A hash of the
file hashes the wrapper and the blob together. It does
not review the blob, and it does not attest the browser
that runs it. No network call at use does not remove
the engine, or the build that produced the embedded
binary. A signature on the container is not an
attestation of the bits that touch the key. This guide does 
not treat Core’s generator as a hole for a kit to close.

The root of trust is the software that runs, not the dice. A kit
that maps rolls is an unauditable root unless it clears Core’s
review. Watching rolls does not make it one.

**Physical theft of one or two backups.**
Spending needs any 3 of 7 geographically split discs. One
stolen disc cannot spend. It can reveal the watch-only
descriptor. That is a balance oracle, not a spend.

Tamper-evident stickers and envelopes are not a control. They can be
copied and replaced. A disc can also be swapped for a copy that carries
malware, and a swap good enough to do that is good enough to replace
the seal. Tamper evidence does not catch the undetectable case, so this
design does not rely on it. A copied disc with no payload still cannot
spend below the threshold. A disc that carries a payload is not yet a
loss. The session that loads it is built with the network off, and it
holds no keys at rest. Funds leave only if that payload gets out. A
kernel exploit on the radio is the long path. Exfil on the return stick,
then ownership of the node, is the short one. That ordering is already
the transfer-channel residual. A seal does not change it.

A spend gathers a threshold in one place. Theft of those three
discs is not an instant loss. Four discs remain. Sweep with any three
of those before the thief spends. A stranger who does not know what
the discs are has to learn the scheme and spend before the owner
sweeps. An attacker who already knows the scheme and is waiting for
the trip can. That race is why the quorum is 3-of-7 and not 2-of-3.
A sweep after theft of the signing set exists only if the remainder
is still a threshold. A 2-of-3 or a 3-of-5 robbed of its signing set
has nothing left. A 2-of-5 or a 3-of-6 still has a sweep. 3-of-7
still has a sweep and a spare.

3-of-7 is the savings quorum: three discs to spend, four can be
lost. 2-of-3 fails if two keys are gone, and an attacker with
one key needs one more. 2-of-5 still spends on two keys.

Total coercion-proof is an impossibility. You can only make the theft
more expensive. A brokerage account, a ROTH IRA, a single-sig, and a
multi-vendor multisig are not coercion-proof either. Duress that ends
in a theft is considerably more expensive here than in most other
models. No firm holds a key, ships the signer, or runs a support desk
that can be ordered to move or freeze the coins. A physical attacker
who has only the operator does not yet have a signature. Spending still
takes three geographically split discs. The operator has to be moved to
those sites, or the locations have to be extracted and then reached,
before a quorum exists. The cheaper physical path is to take the
operator at the moment a spend threshold is already gathered. That
window is the spend itself. A single-sig, a co-located set, a phone
wallet, a multi-vendor multisig whose keys sit together, or a scheme
with fewer than three distinct recovery points spends as soon as the
operator, or one place, is under control. This one does not, except
inside that window. That is a cost claim, not a claim that duress
cannot collect a quorum.

A hardware wallet plus the paper slip is one key 
copied twice, not a quorum. A seed plus a separately stored 
passphrase is a 2-of-2 with no spare. Loss of the word is loss of 
the coins. There is no remaining disc that still spends. The word 
is also typed at restore, by whoever holds it then, so the phishing 
surface the mnemonic already has is still open. It is not a third 
key and it is not a spare.

**Inheritance.**
Each disc is an independent key plus what is needed to rebuild
the wallet, including a copy of the procedure. An heir who
finds one disc can read the threshold and the restore steps.
The only objects with no substitute are any three discs. A
mnemonic or a share that does not carry its own instructions
does not have that property.

A mnemonic is designed to be typed. That invites phishing and
insecure copies. Death does not retire that path. A typed
phrase is when it is used, by someone who did not build the
vault. This vault recovers by reading a disc on the offline
machine. That is the accepted path. It shrinks that input
surface. It does not make physical copies of a disc impossible.

A word list picked without a mapper is still not a Core
wallet. Bitcoin Core does not restore BIP39. Checksum and
later import are another program, or a private procedure
that is not on the disc. The birth step does not close the
spend path. An unpublished workflow is not a simpler
recovery until that procedure is written down. A BIP39
spend also needs a computer and a signer, and a cold one
also stays offline. Two dedicated machines, disc handling,
and Core commands are this procedure. They are not a reason
to take the other stack. The online node may already be
there. A fresh Ubuntu install is a wizard. The commands are
on the disc. An heir needs to read, not to be comfortable
with Linux. The restore steps are on the disc. There is no 
clock on that reading. The coins stay while the heir parses 
the procedure. Day-one comprehension is not required. Counsel 
who follows the disc is still this vault. A shorter
vendor recovery is not a smaller residual. The steps it
skips are the review and the procedure on the disc. Ease is
the product sold, not a measurement of the path.

Steel is not the more robust backup. An engraving is a
transcription. A mistake is silent unless the plate is read
back after the full engraving. A vendor stack does not have
to require that check. These stacks do not ship a
start-to-finish procedure that would. A seed phrase in steel
is a secret only. It is not a descriptor, and it does not
carry the procedure. Analog encoding and the plate's surface
are the limit. A disc is a byte copy. The machine can read
it back. Programmable money is digital. It does not fit an
analog inscription. A plate that outlasts a disc is still 
one secret. Survival of the medium is not a threshold. One 
plate does not spend a 3-of-7, and a plate that is the 
only backup is the loss the quorum was built to absorb. 
The disc refresh every 7–10 years is a media cycle. It is 
not a second key-management problem.

**Loss or destruction of backups.**
Four discs can fail and the vault still spends. That is the point of
3-of-7.

**A supply chain aimed at Bitcoin-specific devices.**
The computers are generic. The signer is Bitcoin Core.

A mailed gadget whose only job is holding bitcoin is a richer
backdoor target than commodity hardware. The device can only
enforce the code it actually runs.

This GitHub repo is not that target. A bad README is a visible
diff. The signer is attested Core. Quiet theft at scale prefers
a vendor updater and a “bug” story. That is the 2026 pattern.

Todd (2018): a mailed Bitcoin gadget advertises guaranteed coins to anyone who
backdoors the package. A small computer used only for Bitcoin is less obvious.
Hardware wallets are still software running on a computer.

Attackers who want coins know exactly what they are looking at. That
supply chain is cheaper to hit than the commodity PC market. The firmware
on those devices is usually shipped by a small team.

**The security standard is Bitcoin Core with independent Guix
attestations.**

Bitcoin Core’s release is a full, reproducible build. Multiple
independent builders reproduce that binary and sign the result.
That is the review this guide treats as acceptable for software
that can touch keys.

A competitor that ships a non-reproducible application binary, or
a non-reproducible blob anywhere in its dependency chain, fails
that test. This guide classes those blobs as malware in
security-critical infrastructure.

Reproducible source is not enough either. If almost no independent
builders attest the actual bits people run, the attestation is
negligible. “We published the repo” is not Guix.

Diversifying across more Bitcoin implementations is not more
review. It is more code and fewer eyeballs on each program.
This guide concentrates review on Core. That is the assurance.
Adding wallets and firmwares spends that assurance down.

The problem is verification. A device can lie about its firmware.
A reproducible wallet app can still depend on an upstream blob that
cannot be checked. Vendor stacks also lack independent attestation
of the full build chain.

The device can only enforce the code it actually runs.

## What you are trusting

- Ubuntu and Bitcoin Core, installed and verified as the README says
- One offline machine as the key-generation environment
- Handling of the seven discs after setup
- The public Bitcoin ledger

Software is written by humans. Bitcoin Core and Linux are used
because they are the most reviewed tools available for this job,
not because they are incapable of bugs.

This guide uses Bitcoin Core’s wallet, descriptors, and PSBT
in the same attested binary as the node. That code is in the
Core tree. It is not an unreviewed extra app. Consensus
review is stricter. That is not a reason to generate keys
somewhere else.

Less review than consensus is not less review than a
vendor wallet. The wallet, the descriptors, and the PSBT
code ship in the same attested binary. The alternative
being sold is a different binary, with no equivalent
attestations. A bug in Core is a finding about that
binary. It is not a reason to move key birth onto the
other one. The v30 migration bug deleted files in a
wallet directory when an unnamed legacy wallet.dat failed
to migrate under pruning, and no external backup existed.
This procedure does not run that path. It creates fresh
descriptor wallets and writes the keys to discs before
funds move. A bug that does not run is not caution in
favor of a vendor stack.

Core has had consensus defects. CVE-2018-17144 is the example:
found, patched, not known to have been exploited on mainnet.
It has not had a published key-generation entropy wipe of the
2026 vendor class. That class is why vendor firmware is out of
this stack.

The review standard is Bitcoin Core with independent Guix
attestations of a bootstrappable full build. Vendor firmware
does not provide that path. Vendor “reproducible firmware” is
usually an unsigned application image inside Docker. That is
not the same scoreboard.

A bug in the generator or the signer can leak keys from
signatures on the public chain. It can also hand the operator an
address that is not the operator’s. Physical split and an air gap do
not contain that. A wrong Core binary is total compromise of every
key loaded into that session. Nonce bias, a bad address, or key
material on the return stick are results of that break, not separate
channels. Guix attestation is the control on the binary. It is not a
per-signature proof. External nonce randomness is not part of this
procedure. Adding it would be a different design.

The question is what makes each signing stack hard to
backdoor. Open source is not enough. XZ was caught by an
unrelated observer with a different incentive. Core and Linux
have that kind of crowd. A small firmware tree generally does
not. Insertion is not impossible. It is expensive here.

One dedicated offline machine running Ubuntu and Bitcoin Core
is a chosen tradeoff. More offline Core machines for key generation would
remove a “this one box was wrong” failure. That would be an
improvement. This guide treats one inspected Core box as sufficient
inside the README’s $10k–$5M comfort zone. The upgrade path is more
Core boxes, not more vendors.

Each extra vendor in the stack usually means an extra coordinator,
extra libraries, and extra firmware. Each of those is more attack
surface.

Multi-vendor hardware multisig cannot run on Bitcoin Core without
extra libraries and a non-Core coordinator. A coordinator can
serve a malicious descriptor. It can bias nonces or other signing
input and exfiltrate key material. It can conspire with a vendor.

A coordinator or signer that imports coincurve, embit, or
another unreproducible binding has added an unattested blob
to the key path. This stack does not.

A coordinator that also holds a hot key is a signer. USB
hardware on that computer can extract that key and the
descriptor. In a 2-of-3, vendor firmware plus the hot key is
enough to spend. This guide does not put a key in the online
coordinator.

A device can lie about its firmware. A reproducible app can still
depend on an uncheckable blob. Independent attestation of that full
build chain is missing.

The common line is that generating keys across several vendors makes
the vault safer. This guide assumes the opposite. “One vendor bug
only burns one key” only holds if every other binary is honest and
is the binary the user thinks it is. This guide does not assume that.
The coordinator and the libraries are part of the quorum in practice,
even when they are not a key on chain.

“Survives if the threshold is not met by that vendor” is the same
slogan. It fails as soon as a second vendor, a coordinator, or a
shared library is in on it. Collusion across the stack is in
scope here. Vendor count is not a substitute for that.

A device is not a silo. One malicious signer does not need a
second vendor. It already has a pipe to the networked host.
It can return data on that pipe across sessions until the
leak is enough to spend, or bias nonces so signatures on
the chain leak the key. The other devices do not inspect
its firmware. The pipe is how an air-gapped signer reaches
a networked node. This stack has one too. The coordinator
and the extra libraries are more unaudited code on that
pipe. A bad descriptor is one use of the pipe, not the class.
USB, QR, or SD is the medium on that path. Swapping it
does not remove the path. The Signing section is that
argument.

A secure element is not a wall around that pipe. It is an
unauditable blob, and not every signer has one. "The key never
leaves" is the blob's claim about itself. This model does not
count it. A recovery service that exports the seed by design 
is that claim failing in public. The vendor built the path out. 
A key used on the host in a mixed stack can cross the
pipe. A vendor's exfil defense is not a control because the
host is not supposed to win. This guide exports the descriptor
onto the discs. A vendor stack does not have to. The ismine
check is an extra check on the node's output, not the close.

A bad descriptor is not the coordinator's scope, and bad entropy
is not the vendor's scope. Each has a seat on the pipe. That seat
is the trust. A coordinator can leave something that stays on the
host and hunts across spends until it has a threshold. A hardware
wallet can do the same from its end of the pipe. Either piece is
load-bearing. One of them, given time, can spend the vault. The
examples are instances. They are not the boundary.

There is one acceptable key-generation path here: Bitcoin Core.
Dice, coin flips, and entropy-lab pages are not a second path.
They are extra software. Cryptographic functions that touch 
keys, the descriptor, or signing stay inside Bitcoin Core. 
That binary is the one with independent Guix attestations 
low on the trust chain. The OS CSPRNG feeds Core. It does 
not replace it. Another library can implement a known scheme 
and still be a second root.

Shamir secret sharing is the case. Encrypting the descriptor
alone leaves the key material in the clear. The coherent
addition encrypts the secrets on the disc, the key and the
descriptor, and leaves the procedure. A found disc still
names the scheme. Opening the secrets takes the same 3-of-7
the spend takes. That is a second vault around this one.
Shipped Core has no Shamir split and no encryption of a
descriptor that follows the spend policy. SLIP-39 and
descriptor-encrypt are other software. Codex32 is a proposal,
not this binary. Wallet encryption in Core is a passphrase on
the wallet file. That recreates the secret this guide already
refuses. The privacy gain is real. The model does not inherit
the layer until that path is Core.

Multi-vendor multisig is not the reference implementation with more
brands. It is a different program.

That risk is the vendor-firmware model, not one brand. In the Coldcard
case, the library on the failing path was written under a pseudonym
later tied by GPG signatures to the vendor’s own CTO, and release
notes thanked that handle as an outside contributor. That is evidence
about who shipped the code. The same shape is available to any small 
team that writes the generator, signs the firmware, and tells the 
market to trust the device.

The bad generator sat in a public repository from 2021 until the
2026 thefts. Public source is not public review. Coldcard was, for
most of that period, the vendor product this market trusted most
for cold storage. A widely recommended device still ran the wrong
code for years. That is the review standard this guide is unwilling
to accept for key generation.

A decade without a loss is not a measurement of the
residual. The users who were swept are not in the sample.
The network is a richer target as it grows, and the profit
for an attacker grows with it. A quiet decade can be the
wait. An attacker who has a flaw, or a vendor who can ship
one, is paid to hold it. The pool gets larger, and the same
coins are worth more later. Silence is not soundness. A
stack can have been sound at genesis and still ship a later
update that takes the coins. That is trust in the vendor's
change control, not a claim about a zero-day. That process
is not Core's, and it is not Linux's. Dice on the device is
the same sample. The device can ignore the rolls. A vendor 
advisory that dice survived the bug is not a control. This
model does not score a stack by who has not been hit yet.

## What this does not try to hide

Bitcoin amounts on chain are public. When a spend happens, the
network can see the script type. That is true of every wallet,
not only a 3-of-7 and not only this guide.

On-chain privacy is out of scope for this procedure. Amounts, the
script type, and the full 3-of-7 witness are visible once a coin is
spent. Address reuse is discouraged because it links receipts. It is
not a privacy scheme. CoinJoin and payjoin are not steps in this
guide. The FAQ names them as further work outside the procedure, not
as part of the vault this model describes. What this design does claim
is the absence of a vendor or custody firm that holds identity, xpubs,
or a support channel.

An unencrypted disc that includes the descriptor lets whoever
holds it watch the wallet if they know what they are looking
at.

That leak is not unique to this guide. Any multisig that can be
restored needs a descriptor backup. Encrypting the descriptor
alone leaves the key material in the clear. The coherent fix
encrypts the secrets on the disc, the key and the descriptor,
and leaves the procedure. A found disc still names the scheme,
the threshold, and the restore steps. Opening the secrets takes
the same 3-of-7 the spend takes. That is a second vault around
this one. Shipped Core has no Shamir split and no encryption of
a descriptor that follows the spend policy. SLIP-39 and
descriptor-encrypt are other software. Codex32 is a proposal,
not this binary. Wallet encryption in Core is a passphrase on
the wallet file, which recreates a secret this guide already
refuses. The privacy gain is real. The model does not inherit
the layer until that path is Core. The FAQ covers it.

None of those, by themselves, move coins. They are accepted in scope
for this design.

3-of-7 is not weakened because the seven keys were born on one
Core machine. The quorum is for loss and theft of discs. Key
generation is a separate choice: Core, not seven vendor RNGs.

## Signing

The online computer builds the PSBT. The offline computer signs.

The online machine is a dedicated clean box. It is trusted as part
of this stack. It is not the signer. Keys are not stored on it.

The signer and the node are both trusted, but not equally. The signer
earns more trust because its job is narrower: it never runs a networked
daemon, it only operates offline, it holds no keys at rest, and each
session is amnesic. The node carries the broader surface, a networked
daemon, downloads, the PSBT it builds, so the design leans on the signer,
not the node for anything key touching.

The online machine can propose a bad PSBT. It cannot meet the
threshold. For a spend that matters, the offline session checks
destination, amount, fee, and change, including `"ismine": true` on
change, before signing. Skipping that check is outside the procedure.
The README makes the check optional only for the small test
configuration spends. The air gap keeps keys off the node. It does
not tell the signer what the operator meant to pay.

The offline signer is rebuilt each spend so it does not have
to be kept secure between uses. A stored machine holds no
keys. The download happens before the signer exists. It is a
download of Bitcoin Core and a hash check, then the network
is disabled, then a disc is loaded. The session that connects 
is a fresh Ubuntu instance. It is not a signer. It becomes a 
signer only after the network is off and a disc is loaded. 
In that working capacity it is never online. It is built 
for this spend and gone at power-off.

The session is amnesic. A previous boot is gone. An internal 
disk is not a second channel. The session is RAM, and swap is 
off. Removing the drive is the same class of step as pulling 
the radio. It does not close the transfer path. A payload would 
have to arrive in this boot, in the live image or the download. 
The hash check is what stops the download. Pulling the current 
hash-checked Core on each spend is the point. A pinned tarball 
goes stale, or it is refreshed through the same online step. 
Pinning it does not remove a link from the trust chain. A payload 
that missed that window has to exfiltrate with no network, before 
power-off, or it is gone.

A prebuilt image does not close that step. Genesis is the
same path: stock image, Core download, hash check, network
off, then discs. If that path is sound once, it is sound on
the next rebuild. Skipping the rebuild is not safer. The
signer has to be constructed. A stateful image is another
object that has to stay honest between spends. It can be
altered or swapped while it sits. The rebuilt session has
nothing to alter. A previous boot is gone.

The boot stick is not that image. It is stock Ubuntu media.
It holds no Core and no keys. The hash check is in the
connected session, then the network goes off. An evil maid
can swap the stick, and a swapped image can lie about that
check. Recreate it from a verified ISO if that is plausible.
A safe is not the control. Tamper at rest is not special to
this stick. Any signer that persists can be altered while
it sits, including a hardware wallet's firmware or hardware.
A hardware wallet at rest also holds a key, and that key can
be extracted. This signer holds no key at rest. There is no
exfil from it while it sits. This guide recreates the signer
from scratch each use. Guarding the stick is not the same job 
as guarding a stateful laptop that is the signer, and it is 
not the same threat model with a storage edge. The stick is 
a generic object in a box. A node, and a hardware 
wallet, are Bitcoin-specific objects at rest, and the 
hardware wallet is a key at rest.

A machine that never connects does not end the trust.
Someone built the image, the ISO, and the hardware. That
build used a network, a pipe to a network, or a third party
who used one. That is trusting trust. It is irreducible.
Hiding the connection does not make it smaller. This guide
puts the connection on a hash-checked Core download, then
turns the network off before a disc is read. That closes
the low path. It is not a claim the turtle ends.

A second machine does not end it either. Downloading Core
on two machines and comparing the hashes by eye does not
require the hash to cross a pipe. That check closes a
single lying display, if the second machine is honest and
the two fetches are independent. It does not close both.
Each hash tool can print the expected digest for a file it
did not hash, and the comparison cannot tell two lies
apart. The same OS, the same tool, two independent
compromises, or one attestation page both of them trusted
are all enough. GPG is the same chain: the builder key,
the GPG binary, the OS, and the hardware are prior builds.
No finite check ends that chain. More Core boxes shrink
the chance that one box was the targeted payload. They
stay on the same reviewed floor. The floor this model
accepts is multi-builder Guix attestations of Core, checked
on a clean live boot, then the network off. A vendor blob
is not a shorter regress. It is a less reviewed one.

Every signer still needs a transfer channel to a networked
node. That channel is the cheaper exfil path. A radio exploit
needs a payload already on the signer. The way onto an
amnesic boot is that channel, or a download that failed the
hash check. If the attacker owns the channel, the radio is
the longer path.

Pulling the radio is a peace of mind ritual. It does not
hurt. It also does not close the transfer channel. It is
not this threat model. The signer is stateless because the
rebuild is the control: hash-checked Core, then the network
off, then a disc. A machine that cannot connect cannot run
that step. Skipping it means a pinned tarball or a prebuilt
image, which is the object the rebuild exists to avoid.
Once the network is off, the radio is not the channel. The
transfer channel is USB. A step that can be seen is not the
control. A management engine below the OS is trusting-trust.
It is irreducible. A missing radio does not close it, and a
vendor signer does not either.

This guide's channel is USB mass storage, which is already in
the OS. An animated QR decoder is not in this stack. Adding
one does not close the channel, and it does not tighten it.
It adds a parser with less review.

A QR rate limit is a camera spec, not a control. Slower exfil
after a foothold is not a safer signer. The payload still had
to arrive in this boot, by the download or by the channel.
Animated QR does not close that arrival. A SeedSigner-style
pipe is not this stack with a tighter channel. It is other
firmware plus that decoder.

Throughput and frames on a screen are not the residual. A key is 
small. The payload that reads it and puts it on the return transfer 
is small. KB/s versus MB/s is not two costs of that exploit. The 
operator already watches the spend, and the animation is the normal 
path. Ownership of the host or the signer removes the intended rate 
and the intended visibility. A quantified gap between those rates is 
not a finding. Animated QR is not a mitigator of key exfil or of 
payload delivery.

A cost is cumulative. A vacuum rate, all else equal, is not a term in 
that sum. The rate exists only if the host and the signer both enforce 
it. A PSBT spend is two legs, host to signer and signer to host. If 
either end is not honest, that end can put a payload on the leg it sends. 
The camera spec does not survive that.

A missing extra is not a hole. There is no finite list of rituals a 
critic can demand: a pulled radio, a second decoder, a firmware disable, 
another machine, another brand.

The test is the path, the cost to run it, and what the added
step closes. If the add does not close the commodity path, or
costs more than the residual, it is a peace of mind ritual.
This design does not owe one.

## Day-to-day vs catastrophe

Normal spends use sneakernet between the two computers. 
Recovery does not depend on sneakernet, on a particular disc 
drive, or on this repository. Setup uses an optical drive. If 
that drive is lost or broken later, another drive is enough. 
Anyone who can read the discs and run Bitcoin Core can 
reconstruct the wallet and spend.

This vault has no timelock, no presigned package, and no competing
recovery path. Fee estimation, RBF, CPFP, package relay, pinning, and
reorg reassessment are ordinary confirmation risks, the same as a
holder-controlled Core single-sig. A stuck transaction can be remade
with any three discs. The online node is the usual broadcast path.
Any other relay that accepts a valid transaction works. Confirmation
depth is an operator choice, not a custody-policy parameter.

## Other products

These fail differently. They are not one ranking. Bitcoin 
is a bearer instrument on a push network with final settlement. 
Anyone who holds keys accepts operational risk. This guide does 
not create that fact. Hardware wallets and collaborative custody 
do not erase it. A brokerage is a claim on an institution, not 
the absence of operations. A claim on an issuer can have redemption 
suspended. That is ordinary fiscal practice. A company that ships the
signer, or holds a key, can be ordered to stop. The reference 
implementation has no desk. Its backend is the node the holder runs. 
There is no firm-operated service to hold.

**Hardware wallets** concentrate key generation, display, and 
often the coordinator relationship in vendor software and a 
Bitcoin-specific supply chain. Users who followed default setup 
instructions have lost funds when that software was wrong. A 
screen does not help if the generator that created the seed was 
weak, and it does not help if another binary in the stack is the 
thief. The failure is the model. Coldcard 2026 is the exhibit. The 
weak path entered through a dependency committed under the 
pseudonym switck. Those commits were signed with the Coinkite CTO’s 
GPG key. The company called it an integration accident. The class 
is not unique to that brand.

A weak generator is the exhibit, not the class. The device
can ignore entropy the user added, or substitute its own seed
for the one the user supplied. A screen does not show that.
The lesson is not to generate your own entropy. The device
still runs the unauditable code. The class is no review, no
reproducible build, and no independent attestation of the
bits that touched the key.

The screen renders what that firmware decides to render.
Address, amount, fee, and change are in that set. A match
between the device and the host is a match between two
displays. It does not read the generator, and it does not
read a second binary on the pipe. The 2026 path showed
addresses for keys the chain could sweep. A displayed
field is the device's claim about a session.

The architectural case is older than Coldcard 2026. Maxwell (2020) 
called the devices opaque, hard to review, and a supply-chain target, 
and would not recommend them for serious amounts. Spigler (2020) is 
the long form.

Multi-coin firmware adds networks and libraries on the same device 
that holds the Bitcoin key. That is more attack surface and less review. 
Bitcoin-only vendor firmware is smaller. It is still not Core.

**Multi-vendor hardware multisig** adds more of that stack, then a 
non-Core coordinator. It cannot be built from the reference implementation. 
Vendor diversity does not create Guix attestations and does not stop a 
device from lying about its firmware.

Both stacks end at silicon and a boot path. No further
check ends that, and that regress is shared. The device
is not. This design's object is a generic laptop. The
other stack's object is a Bitcoin-specific device, bought
because it will hold keys. The buyer set is the holder
set. A generic laptop's path is not aimed at the coins.
One vendor compromise does not fail the threshold. That
signer already has the pipe, and the coordinator is the
seat that mixed the vendors. The layers this model
refuses are the unattested firmware, the secure element,
and that coordinator. The live boot, the air gap, and
the 3-of-7 are the design. They are not a cost stack
that wins the layers in between. The pipe remains. A
skipped check remains.

**Collaborative custody** is usually a 2-of-3 sold as self-custody. The 
user holds one key. The company holds one. A third key is picked by the 
company. The company picks the software. Those two keys can move or 
freeze coins. A terms-of-use page is not a map of that relationship.

A company in the policy is also a name attackers can impersonate: fake 
support, fake recovery, compromised help channels. That surface does not 
exist when every key is a disc the operator placed.

Two hardware wallets plus a BitGo-style HSM (Swan and similar) is the 
same class, not a Core vault with a helper. The HSM is vendor firmware 
that cannot be attested. It does not become Core because it sits in a trust.

A brokerage, ETF, or trust is a legal claim on bitcoin or a bitcoin-linked 
product. The account holder is trusting that institution’s people, its 
software, and the law around the account. That software is not Bitcoin Core, 
and the account holder cannot inspect it. Recourse is the product. It does not 
give the account holder bearer coins, and it does not remove operational risk. 
It relocates the risk into a named custodian.

A trusted third party is a firm that can be ordered to
change the product. The infrastructure the holder uses to
speak that product can be changed overnight. A backend can
be shut down, a model discontinued, an app update forced.
The reference implementation has no desk. Its backend is
the node the holder runs. That pressure is not required for the 2026
class, and it does not make the 2026 class rare.

A claim on an issuer can have redemption suspended. That
is ordinary fiscal practice. A company that ships the
signer, or holds a key, can be required to change the
product. Disappearance and collusion are the same class 
as the order. A format the reference client does not speak
leaves the holder depending on that layer to re-enter.
Soundness under the layer's own spec does not close
that. The infrastructure the holder uses to speak that product
can be changed overnight. A backend can be shut down, a
model discontinued, an app update forced. The service can
be held until a control is in place. Stopping service is
one case, not the class. The reference implementation has
no desk. Its backend is the node the holder runs. A format
the reference client does not speak leaves the holder
depending on that layer to re-enter. BIP39 is one case.

Against a vendor signer or a multi-vendor quorum, that contract is the fallback 
inside the $10k–$5M band. A vendor signer is not a place to wait inside the band. 
Waiting, until the README can be followed, is inside the regulated product. 
Below the band, a small vendor stack or a phone wallet is outside this model. It 
is not a reason to sell spot today. It is a reason not to keep adding spot on that
stack. As the amount approaches the lower bound, the regulated product is the 
alternative if the README will not be completed.

That contract fits a person who wants someone to call, a regulator, an estate 
process. That is not self-custody, and it is not this guide.

## Operator duty

The steps are the procedure. Improvising the vault is outside this model. The 
procedure is closed because the audience is not meant to design it. Some changes 
can stay inside this threat model or improve it. They are still a different design, 
and the assurances here apply to the README as written.

Historical self-custody losses often came from novel setups nobody else reviewed. 
This guide makes those choices in the open so the operator does not invent them.

The load-bearing steps are a clean dedicated pair of machines, Guix-attested Bitcoin 
Core, the air gap for keys, seven discs, and the test spends.

## Amount

The README’s $10k–$5M range is a design comfort zone, not a law. Vendor products do 
not publish an equivalent ceiling. Naming the band is not a concession that brands 
are stronger inside it, and especially not above it. Above the band the upgrade this 
guide names is more Core boxes, not more logos.

Design disagreements belong in the FAQ or a public issue, not in a private 
vulnerability report. See [SECURITY.md](SECURITY.md).

# How to Set up Secure, Airgapped Bitcoin Multisig with Bitcoin Core

## Introduction

Guide Version: 1.09

This guide was created using [Bitcoin Core's official multisig-tutorial](https://github.com/bitcoin/bitcoin/blob/master/doc/multisig-tutorial.md) as a reference. 

Users can verify the scripts found here by comparing them to the scripts provided in the official multisig tutorial.

This guide aims to improve the usability of the official multisig guide, as well as provide setup instructions for a secure node & 3 of 7 multisig vault, full wallet & key backups, and an easy to use airgapped signing device. This guide functions both as an educational tool and alternative to [YetiCold Level 3](https://github.com/jwweatherman/yeticold) for users who prefer to do things manually, verify every step of the process, and minimize dependencies.

See the [FAQ](FAQ.md) for answers to common questions about the design.

This multisig vault is only appropriate for storing between $10k-$5M in Bitcoin.

## You will need:

- 2 Dedicated Laptops (8GB RAM minimum, 64GB storage minimum) (try refurbished thinkpads) (Chromebooks will not work)

- 2 Fresh USB sticks (16GB minimum) (try kingston datatravelers 64GB)

- 7 Millenniata brand M-disc DVDs 4.7GB or 7 Verbatim brand Ultralife Gold Archival grade DVDs 4.7GB

- 1 USB powered disc drive capable of writing M-Disc DVDs (try ASUS zendrive)

estimated total cost (using amazon for reference as of 11 Aug 2026):
~$500

# A. Initial Set Up

These two laptops should be dedicated for use with Bitcoin Core ONLY. DO NOT use these two dedicated laptops for any other purpose or software than what is described in this guide. 

## Step A1. [online computer] Install Ubuntu

Pick one laptop to be the online computer, this will be the Bitcoin node. We need to install Ubuntu on this computer.

[Download the latest version of Ubuntu here](https://ubuntu.com/download/desktop)

You will most likely need the Intel or AMD 64-bit architecture, download the latest version (26.04 as of this post).

You should [verify the Ubuntu download](verify_ubuntu.md) before creating the installer.

Grab one of the fresh USB sticks and mark it with a sticker or a piece of tape. This will be the Linux USB, and it needs to be flashed with the ubuntu installer.

If you are on Windows you can download an app called [Rufus](https://rufus.ie) and use that to create the live installer. MacOS users can download an app called [balenaEtcher](https://github.com/balena-io/etcher/releases). 

If you know how to use dd and you already have access to a terminal this is best way because it does not rely on a third party dependency. Be careful you flash the correct drive if you use dd, it's colloquially called "disk destroyer" for a reason.

Once you have the Linux USB ready, the next step will be to install Linux on the online computer. Doing this requires turning off the computer, inserting the Linux USB (USB with tape) into the online computer and turning the power back on. If this doesn't work on the first try, you might need to change the boot order within the BIOS.

After the Ubuntu splash screen select `Install Ubuntu`.

During installation, simply proceed with all of the default settings, when you reach "Disk Setup" screen select "Erase disk and install Ubuntu". When you reach the "Encryption and File System" screen select "Encrypt with a passphrase". Choose a password for the node, remember to write down this password (if you lose password you will have to reinstall ubuntu and resync the node, but the bitcoin wallet will not be affected).


## Step A2. [online computer] Install Security Updates

Once you've finished installing Linux, you need to install security updates. First connect to WIFI or LAN.

Open a terminal with `Ctrl + Alt + T`. Then type or copy and paste the following commands into the terminal.

Note: To copy and paste within a terminal on Linux you must use `Ctrl + Shift + C` to copy and `Ctrl + Shift + V` to paste.

`sudo apt update`

Press enter, then press Y if prompted and press enter again, wait for it to finish

`sudo apt -y full-upgrade`

press enter, then press Y if prompted and press enter again, wait for it to finish


## Step A3. [online computer] Install the Latest Version of Bitcoin Core

## [online computer] Download Bitcoin Core

open a terminal, navigate into the `~/Downloads` folder and then copy and paste the following command into the terminal and press enter to download Bitcoin Core and its signed hash.


```
wget https://bitcoincore.org/bin/bitcoin-core-31.1/bitcoin-31.1-x86_64-linux-gnu.tar.gz
wget https://bitcoincore.org/bin/bitcoin-core-31.1/SHA256SUMS
wget https://bitcoincore.org/bin/bitcoin-core-31.1/SHA256SUMS.asc

```

### [online computer] Verify Bitcoin Core

After you've finished downloading Bitcoin core verify the hash by running this command in the terminal.

```
sha256sum --ignore-missing --check SHA256SUMS
```

Press Enter and ensure you get an "OK" result. If you do not see an "OK" message STOP AND DO NOT PROCEED.

Again within the `~/Downloads` folder copy and paste the following command to verify the signatures on the Bitcoin Core Software.

```
wget -O guix.sigs.tar.gz https://github.com/bitcoin-core/guix.sigs/archive/refs/heads/main.tar.gz
tar -xzf guix.sigs.tar.gz
gpg --import guix.sigs-main/builder-keys/*
gpg --verify SHA256SUMS.asc
```

Look for `gpg: Good signature from...` on at least a few Bitcoin Core contributors. If you do not see any good signatures STOP AND DO NOT PROCEED.

If you see `WARNING: this key is not certified with a trusted signature! There is no indication that the signature belongs to the owner` [this can safely be ignored](gpg_warning.md).  

### [online computer] Unpack Bitcoin Core from Tarball

Again within the `~/Downloads` folder copy and paste the following command to unpack the Bitcoin Core tar.gz now that we have verified it is legitimate.

```
tar -xzf bitcoin-31.1-x86_64-linux-gnu.tar.gz -C ~
```

Bitcoin Core now exists within the home directory inside of the `~/bitcoin-31.1` folder.

## Step A4. [online computer] Start Bitcoin Core

### Enable Pruning

Open a terminal with `Ctrl + Alt + T`. 

We need to create a `~/.bitcoin` folder and copy the bitcoin configuration file example at `~/bitcoin-31.1/bitcoin.conf` into the new `~/.bitcoin` folder.

You can do that by running this command in the terminal

`mkdir -p ~/.bitcoin && cp ~/bitcoin-31.1/bitcoin.conf ~/.bitcoin/`

Using your file explorer, navigate into `~/.bitcoin` double click on the file named "Bitcoin.conf". 

Note: If you are using the file explorer you will need to click the drop down arrow in the top right corner of the window and click on "Show hidden files". The `~/.bitcoin` folder is hidden by default.

Add the following line anywhere in the file, make sure it's not on a line with `##`

`prune=5500`

Then click save and close the bitcoin.conf file.

## Start Bitcoin Daemon

Within your terminal, navigate into the `~/bitcoin-31.1/bin` folder, copy and paste the following command in the terminal.

`./bitcoind -daemon`

Press enter

This computer will now begin syncing the Bitcoin blockchain. This can take a while (days or weeks). You will need this process to completely finish before you can test your wallet.

Note: It is important to always properly shut down bitcoin core before turning off this computer, this prevents wasted time spent resyncing in the future. To do this navigate into the `~/bitcoin-31.1/bin` folder and run this command.

`./bitcoin-cli stop`

## Step A5: Switch to \*offline computer\*

Now switch to the second computer, this will be the \*offline computer\*. Place a piece of tape on this computer to mark it.

Insert the Linux USB (the one with tape) and turn the computer on. Remember if are not greeted by the Ubuntu installer, you may need to adjust the boot order in the BIOS. From this point forward the Linux USB will remain plugged into the \*offline computer\* (remember both the offline computer and the Linux USB are marked with tape).

After the Ubuntu splash screen select `Try Ubuntu`.

You will now temporarily connect to either WIFI or LAN.

## Step A6: [\*offline computer\*] Install Updates and Software

## [\*offline computer\*] Download this guide

You can open this guide in your browser on the offline machine for now and leave it open, we will disable networking shortly and it will no longer be accessible if you accidentally close it. So we will download a copy just in case.

Open a terminal and copy and paste the following command in the home directory and press enter.

`wget -O README.md https://raw.githubusercontent.com/bowlarbear/yeti-2.0/main/README.md?$(date +%s)`

You should now have this guide in the Home directory.

You can run `less README.md` inside the home directory to open this guide in a terminal window on the offline machine.

### [\*offline computer\*] Install Bitcoin Core

Repeat Step A3 on the \*offline computer\*.

### [\*offline computer\*] Install Brasero

Open a terminal and run the following command

```
sudo apt update
sudo apt -y install brasero
```

Press enter and wait for it to finish. You may see a dkpg error in the terminal after installing brasero but this can safely be ignored.

Note: The authenticity of the Brasero software is automatically checked by Ubuntu's apt package manager. Brasero is needed so we can make backups of our keys and burn them to M-discs.

## Step A7: [\*offline computer\*] Disable Networking 

Within the terminal copy and run the following command:

```
nmcli networking off
rfkill block bluetooth
```

This command will disable all networking functionality (WIFI, LAN, and Bluetooth)

### [\*offline computer\*] Disable any swap space

Swap space is virtual RAM that is borrowed from the internal storage drive. Open a terminal and run this command to verify that there is no swap space enabled on the \*offline computer\*.

`sudo swapoff -a`

# B. Creating a Multi Signature Cold Wallet on Bitcoin Core

## Step B1: [\*offline computer\*] Start Bitcoin Core

Open a terminal and navigating into the `~/bitcoin-31.1/bin` folder

start the Bitcoin Daemon

`./bitcoind -daemon`


## Step B2: [\*offline computer\*] Create 7 Wallets

Within `~/bitcoin-31.1/bin` in the terminal copy and paste the following...

```
for ((n=1;n<=7;n++))
do
 ./bitcoin-cli createwallet "key_${n}"
done
```

Press Enter


## Step B3: [\*offline computer\*] Capture Extended Public Keys (XPUBs) 

```
declare -A xpubs

for ((n=1;n<=7;n++))
do
 xpubs["xpub_${n}"]=$(./bitcoin-cli -rpcwallet="key_${n}" listdescriptors | jq '.descriptors | [.[] | select(.desc | startswith("wpkh") and contains("/0/*") )][0] | .desc' | grep -Po '(?<=\().*(?=\))' | sed 's /0/\* /<0;1>/* ')
done

```

## Step B4: [\*offline computer\*] Create the Multisig Wallet Descriptor

```
desc="wsh(sortedmulti(3,${xpubs["xpub_1"]},${xpubs["xpub_2"]},${xpubs["xpub_3"]},${xpubs["xpub_4"]},${xpubs["xpub_5"]},${xpubs["xpub_6"]},${xpubs["xpub_7"]}))"

checksum=$(./bitcoin-cli getdescriptorinfo $desc | jq -r '.checksum')

multisig_desc="[{\"desc\": \"${desc}#${checksum}\", \"active\": true, \"timestamp\": \"now\"}]"

./bitcoin-cli -named createwallet "multisig_watch_wallet" true true

./bitcoin-cli -rpcwallet="multisig_watch_wallet" importdescriptors "$multisig_desc"

./bitcoin-cli -rpcwallet="multisig_watch_wallet" getwalletinfo
```

## Step B5: [\*offline computer\*] Export the Watch Only Wallet Descriptor

Grab the second USB stick (with no tape), this will be the transfer USB, Insert it into the \*offline computer\*. Copy `~/.bitcoin/wallets/multisig_watch_wallet` onto the transfer USB and then remove the transfer USB from the \*offline computer\*.

Note: If you are using the file explorer to drag & drop to copy files you will need to click the drop down arrow in the top right corner of the window and click on "Show hidden files". The `~/.bitcoin` folder is hidden by default.

Note: It is wise to always run the `sync` command in the terminal and wait for it to finish before removing a USB.

Insert the transfer USB into the online computer. Copy the multisig_watch_wallet into the `~/.bitcoin/wallets` folder. When Bitcoin Core is finished syncing the blockchain, you will be able to load this wallet on the online computer with the following terminal command from within `~/bitcoin-31.1/bin`

`./bitcoin-cli loadwallet "multisig_watch_wallet"`

You can use either bitcoin-cli or bitcoin-qt (Bitcoin Core's graphical user interface) to load this wallet, see the transaction history, check the balance of the wallet, generate receive addresses, generate PSBTs (Partially Signed Bitcoin Transactions) for export to your offline machine, and broadcast fully signed Bitcoin Transactions.


## Step B6: [\*offline computer\*] Backup Keys
Now back up each of the 7 keys and the wallet descriptor.

Use brasero to create 7 mdisc backups. These files can be found in the `~/.bitcoin/wallets` folder. Take an M disc and write the number 1 on it with a permenent marker, insert disc 1 into the USB connected disc drive. Then use brasero create an .ISO of key_1 & the multisig_watch_wallet from `/.bitcoin/wallets` along with README.md which is a copy of this guide. Burn this .ISO to disc 1. Repeat for all 7 keys.

1 = key_1 & multisig_watch_wallet

2 = key_2 & multisig_watch_wallet

3 = key_3 & multisig_watch_wallet

4 = key_4 & multisig_watch_wallet

5 = key_5 & multisig_watch_wallet

6 = key_6 & multisig_watch_wallet

7 = key_7 & multisig_watch_wallet

# C.  Test Wallet Backups

## C1. [\*offline computer\*] Delete Wallets Folder

Stop Bitcoin Core

`./bitcoin-cli stop`

wait a moment for the daemon to finish shutting down

Delete the entire `~/.bitcoin/wallets` folder

start Bitcoin Core again

`./bitcoind -daemon`


## C2. [online computer] Create a Receive Address

Note: You can generate addresses on either your online machine or your offline machine, provided that you have the "multisig_watch_wallet" in the `~/.bitcoin/wallets` folder.

Note: In order to check balances and generate new addresses the "multisig_watch_wallet" wallet must loaded with either Bitcoin-QT, which is Bitcoin Core's Graphical User Interface (GUI) or with the Bitcoin-cli (see step B5). To use the GUI, simply double click on "Bitcoin-QT" inside of `~/bitcoin-31.1/bin`, then load "multisig_watch_wallet" and generate a receive address for a QR code.

The use the cli, load the wallet like in step B5 and then run:

```
./bitcoin-cli -rpcwallet="multisig_watch_wallet" getnewaddress
```

Test the wallet by sending a very small amount of Bitcoin to this address (this should be less than $5). 

Note: Alternatively, you can also generate a QR code for this address if you use Bitcoin-QT.


## C3. [online computer] Check the Balance of the Wallet

Note: You can only check the balance of your wallet from the online computer, with the wallet properly loaded as shown in step B5.

```
./bitcoin-cli -rpcwallet="multisig_watch_wallet" getbalances

```


## C4. [online computer] Create a Transaction 

 Note: You can only create a transaction from the online computer with the wallet properly loaded as shown in step B5.

### Important: Replace $amount and $destination_address with the right values, make sure these are correct before running

Note: The `amount` field is denominated in `0.00000000` BTC

```
funded_psbt=$(./bitcoin-cli -rpcwallet="multisig_watch_wallet" -named \
 walletcreatefundedpsbt \
 outputs="{\"$destination_address\": $amount}" \
 options='{"subtractFeeFromOutputs":[0]}' | jq -r '.psbt')

echo "$funded_psbt" > ~/Desktop/unsigned.psbt

```

Insert the transfer USB (no tape) into the online computer, copy the unsigned.psbt from the Desktop onto the transfer USB. Remove the transfer USB containing the PSBT from the online computer.


## C5. [\*offline computer\*] Sign the Transaction

### [\*offline computer\*] Transfer the Unsigned PSBT

Insert the transfer USB (no tape) into the \*offline computer\*. Copy or drag and drop `unsigned.psbt` from the transfer USB onto the Desktop.

### [\*offline computer\*] Verify the PSBT

Open a terminal and run this command.

`./bitcoin-cli decodepsbt "$(cat ~/Desktop/unsigned.psbt)"`

Verify the contents of the output, make sure that the `destination_address` and `amount` within `tx.vout` matches what you expect.

If the transaction does not match what you expect STOP and reevaluate.

### [\*offline computer\*] Load the Keys
Choose 3 of the M-discs, insert them one at a time into the \*offline computer\*'s USB connected disc drive, and copy the key_# directory into `~/.bitcoin/wallets`.

After copying a key run the following command, replace `key_#` with the name of the key you copied into `~/.bitcoin/wallets`

`./bitcoin-cli loadwallet "key_#"`

### [\*offline computer\*] Sign the PSBT

After loading all 3 of the keys, sign the PSBT with this script in the terminal

```
psbt=$(cat ~/Desktop/unsigned.psbt)

wallet1=$(./bitcoin-cli listwallets |jq -r '.[0]')
wallet2=$(./bitcoin-cli listwallets |jq -r '.[1]')
wallet3=$(./bitcoin-cli listwallets |jq -r '.[2]')

psbt_1=$(./bitcoin-cli -rpcwallet="$wallet1" walletprocesspsbt "$psbt" | jq -r '.psbt')
psbt_2=$(./bitcoin-cli -rpcwallet="$wallet2" walletprocesspsbt "$psbt_1" | jq -r '.psbt')
psbt_3=$(./bitcoin-cli -rpcwallet="$wallet3" walletprocesspsbt "$psbt_2" | jq -r '.psbt')

echo "$psbt_3" > ~/Desktop/signed.psbt

```

Note: There is a chance this process will fail if you attempt to run the signing script above with your "multisig_watch_wallet" loaded on the \*offline computer\*. To avoid this, only have 3 keys loaded when signing and nothing else. You can unload the watch wallet with the following command:

`./bitcoin-cli unloadwallet multisig_watch_wallet`

### [\*offline computer\*] Export Signed PSBT to Transfer USB

Copy `signed.psbt` from the Desktop onto the transfer USB. Remove the transfer USB from the \*offline computer\*

### [online computer] Import Signed PSBT to Online Computer

Insert the transfer USB into the online computer. Copy signed.psbt from the transfer USB onto the Desktop.

### [online computer] Verify the PSBT

Open a terminal and run this command.

`./bitcoin-cli decodepsbt "$(cat ~/Desktop/signed.psbt)"`

Verify the contents of the output, make sure that the `destination_address` and `amount` within `tx.vout` matches what you expect.

If the transaction does not match what you expect STOP and reevaluate.

## C6. [online computer] Broadcast Transaction

```
psbt=$(cat ~/Desktop/signed.psbt)
hex=$(./bitcoin-cli finalizepsbt "$psbt" | jq -r '.hex')
./bitcoin-cli sendrawtransaction "$hex"

```

Note: Delete old signed.psbt and unsigned.psbt files off of the transfer USB and offline and online desktops after finishing each transaction.

Repeat this process (steps C1 through C6) until you've tested all 7 of the key backups. This will require 3 test transactions total 

1st transaction: key1, key2, key3 

2nd transaction: key4, key5, key6

3rd transaction key7, + any 2 other keys

Warning: If any of the test transactions fail during this process, the best thing to do is to go back to step C4 and try again. If it still doesn't work then something is wrong and you should stop. Delete all psbts on both computer's `~/Desktop`. Delete the multisig_watch_wallet on the transfer usb. Delete both of the `.bitcoin/wallets` folders on both computers, and start over at step A5 with fresh m-dsics.

Only after you have successfully completed all 3 test transactions, testing all 7 keys as described above, will you have confirmed that the wallet is working properly.

## C7. Geographically Distribute Backups

The next step is to place each of the 7 back up discs into 7 different envelopes. Mark them with something non-descript like "Do not open. Property of <your_name>. Pass this on to to next of kin." What you write on these envelopes will ultimately be up to you, but it should be relatively non-descript. 

The 7 envelopes must be geographically distributed to 7 different locations. 

You now have a secure, Bitcoin multisig vault that can only be accessed by gathering 3 geographically distributed keys. 


# D. How to use the Wallet Normally

By this point you should already have a good understanding of how this works. The \*offline computer\* does not have any persistence. This is for your security, so no keys are ever written to the computers storage, they can never be recovered without the backup discs. Each time you wish to sign a psbt with the \*offline computer\* follow these steps carefully...

Insert Linux USB into the powered off, \*offline computer\*, turn the computer on, after the Ubuntu splash screen select `try ubuntu`

Temporarily connect to your home network, download and verify Bitcoin Core. Download this guide if needed (see steps A6 & A7). You do not need to reinstall Brasero.

### IMPORTANT: [\*offline computer\*] Before Inserting any Key Material

### [\*offline computer\*] Disable Networking

```
nmcli networking off
rfkill block bluetooth
```

### [\*offline computer\*] Disable Swap Space

`sudo swapoff -a`

From here the process for spending from the multisig is the same as above.

Next time you want to spend Bitcoin from the multisig:
1. [online computer] Create the unsigned PSBT on the online computer, drag the unsigned PSBT into the transfer USB (step C4)
2. [\*offline computer\*] insert transfer USB into the \*offline computer\*, drag the unsigned PSBT onto the desktop (step C4)
3. [\*offline computer\*] verify the psbt contents (step C5)
4. [\*offline computer\*] collect any 3 of the key discs, insert them 1 at a time and drag the key folders into the `~/.bitcoin/wallets` folder (step C5)
5. [\*offline computer\*] load the wallets from the terminal (step C5)
6. [\*offline computer\*] sign the PSBT (step C5)
7. [\*offline computer\*] drag the signed PSBT from the desktop onto the transfer USB, remove the transfer USB and insert it into the online computer (step C5)
8. [online computer] drag the signed PSBT from the transfer USB onto the desktop (step C5)
9. [online computer] verify the psbt contents (step C5)
10. [online computer] broadcast the signed PSBT (step C6)


For security you should always turn off the \*offline computer\* after you finish signing and exporting a PSBT.

Remember: These two laptops should be dedicated for use with Bitcoin Core ONLY. DO NOT use these two dedicated laptops for any other purpose or software.


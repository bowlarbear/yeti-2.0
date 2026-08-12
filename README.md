# How to set up secure, airgapped Bitcoin multisig with Bitcoin Core

## Note

This guide was created using [Bitcoin Core's official multisig-tutorial](https://github.com/bitcoin/bitcoin/blob/master/doc/multisig-tutorial.md) as a reference. 

Users can verify the scripts found here by comparing them to the scripts provided in the official multisig tutorial.

This guide aims to improve the usability of the official multisig guide, as well as provide setup instructions for a secure node & wallet, wallet & key backups, and an easy to use airgapped signing device.

This multisig vault is reccomended for storing between $10k-$5M in Bitcoin.

## You will need:

-2 Dedicated Laptops (8GB RAM minimum, 64GB storage minimum) (try refurbished thinkpads) (Chromebooks will not work)

-2 Fresh USB sticks (16GB minimum) (try kingston datatravelers 64GB)

-8 pack of Milleniata brand M-disc DVDs 4.7GB (do not buy any other brand than millenniata)

-A USB powered disc drive capable of writing M-Disc DVDs (try ASUS zendrive)

estimated total cost (using amazon for reference):
~$450

# A. Initial Set Up

## Step A1. [online computer] Install Ubuntu

Pick one laptop to be the online computer, this will be your Bitcoin node. We need to install Ubuntu on this computer.

[Download the latest version of Ubuntu here](https://ubuntu.com/download/desktop)

You will most likely need the Intel or AMD 64-bit architecture, download the latest version (26.04 as of this post).

Grab one of the fresh USB sticks and mark it with a sticker or a piece of tape. This will be the Linux USB, and it needs to be flashed with the ubuntu installer.

If you are on Windows you can download an app called Rufus and use that to create the live installer. MacOS users can download an app called balenaEtcher. If you know how to use dd and you already have access to a terminal this is best way.

Once you have the Linux USB ready, the next step will be to install Linux on the online computer. Doing this requires turning off the computer, inserting the Linux USB (USB with tape) into the online computer and turning the power back on. If this doesn't work on the first try, you might need to change the boot order within the BIOS.

After the Ubuntu splash screen select `Install Ubuntu`.

During installation, simply proceed with all of the default settings, when you reach the "Encryption and File System" screen select "Encrypt with a passphrase" choose a password for your node, remember to write down this password (if you lose password you will have to reinstall ubuntu and resync the node, but your bitcoin wallet will not be affected)


## Step A2. [online computer] Install security updates

Once you've finished installing Linux, you need to install security updates. First connect to WIFI or LAN.

Open a terminal with `Ctrl + Alt + T`. Then type or copy and paste the following commands into the terminal.

`sudo apt update`

Press enter, then press Y if prompted and press enter again, wait for it to finish

`sudo apt full-upgrade`

press enter, then press Y if prompted and press enter again, wait for it to finish


## Step A3. [online computer] Download the latest version of Bitcoin Core

Do NOT go to bitcoin.org (that website is unfortunately owned by scammers)

[Download the latest version of Bitcoin Core here](https://bitcoincore.org/en/download)

Click the big blue "Download Bitcoin Core" button.

Unpack the Tarball, you can go into the `~/Downloads` folder and right click on the bitcoin core file and click "Extract" then drag the new folder it creates into the HOME directory.

## Step A4. [online computer] Start Bitcoin Core

Open the folder in the Home directory and then click on Bitcoin-31.1

### IMPORTANT: If you opted not to upgrade the online computer's storage drive to atleast 2TB you need to set your node to pruned mode. In this case it will still do fully validate all historical bitcoin blocks, but it will not store a full copy of the bitcoin blockchain on the node.

Inside of Bitcoin-31.1 double click on the file named "Bitcoin.conf". 

Add the following line anywhere in the file
`prune=5500`
Then click save and close the bitcoin.conf file.

Open a terminal with `Ctrl + Alt + T`. Navigate into the Bitcoin folder we extracted in step 3 using the change directory command `cd`. We need to be inside of the `/bin` folder. 

Once inside the `~/Bitcoin-31.1/bin` folder, copy and paste the following command in the terminal.

`./bitcoind`

Press enter

This computer will now begin syncing the Bitcoin blockchain. This can take a while (days or weeks), if you like to speed it up you can use assumeutxo.

It is important to always properly shut down bitcoin core before turning off this computer, this prevents wasted time spent resyncing in the future. To do this navigate into the `~/Bitcoin-31.1/bin` folder and run this command.

`./bitcoin-cli stop`


## Step A5: Switch to offline computer

Now switch to the second computer, this will be the offline computer. Place a piece of tape on this computer to mark it.

The first thing to do is to remove any internal storage drives from the offline computer. This will vary depending on the model, but you should remove both the internal SATA drive and the NVME drive (some computers will only come with one or the other, some will come with both).

After you've removed all internal storage drives, put the computer back together, insert the Linux USB (the one with tape) and turn the computer on. From this point forward the Linux USB will remain plugged into the offline computer (remember both are marked with tape).

After the Ubuntu splash screen select `Try Ubuntu`.


## Step A6: [offline computer] Install Updates and Software

### [offline computer] Download this guide

Once you are on the desktop connect to WIFI or LAN.

Open this guide in firefox. You can just leave the browser window open on the guide, but you should also download this guide so you will still have access to it after we disable networking.

Open a terminal and copy and paste the following command and press enter.

`wget https://raw.githubusercontent.com/bowlarbear/yeti-2.0/main/README.md`

You should now have this guide in the Home directory.

You can run `less README.md` to open this guide in a terminal window. You can always download this guide again but it might be helpful to keep a copy on your transfer USB and on your backups discs that we will do later.

### [offline computer] Install updates

Open a new terminal window and copy and paste the following commands.

`sudo apt update`

Press enter, then press Y if prompted and press enter again, wait for it to finish

`sudo apt full-upgrade`

press enter, then press Y if prompted and press enter again, wait for it to finish

### [offline computer] Install Brasero

Download Brasero with the following command.

`sudo apt install brasero`

press enter and wait for it to finish.

### [offline computer] Install Bitcoin Core

Do NOT go to bitcoin.org (that website is unfortunately owned by scammers)

Go to https://bitcoincore.org/en/download

Click the big blue "Download Bitcoin Core" button.

Unpack the Tarball, you can go into the `~/Downloads` folder and right click on the bitcoin core file and click "Extract" then drag the new folder it creates into the HOME directory.


## Step A7: [offline computer] Disable networking on the offline computer

Open the terminal and run the following command:
`nmcli networking off`


# B. Creating a Multi Signature Cold Wallet on Bitcoin Core

## Step B1: [offline computer] Start Bitcoin Core

On the offline computer start by opening a terminal and navigating into the `~/Bitcoin-31.1/bin` folder

start the Bitcoin Daemon

`./bitcoind -daemon`


## Step B2: [offline computer] create 7 wallets

Within `~/Bitcoin-31.1/bin` in the terminal copy and paste the following...

```
for ((n=1;n<=7;n++))
do
 ./bitcoin-cli createwallet "key_${n}"
done
```

Press Enter


## Step B3: [offline computer] capture Extended Public Keys (XPUBs) from each wallet 

```
declare -A xpubs

for ((n=1;n<=7;n++))
do
 xpubs["xpub_${n}"]=$(./bitcoin-cli -rpcwallet="key_${n}" listdescriptors | jq '.descriptors | [.[] | select(.desc | startswith("wpkh") and contains("/0/*") )][0] | .desc' | grep -Po '(?<=\().*(?=\))' | sed 's /0/\* /<0;1>/* ')
done

```

## Step B4: [offline computer] create the multisig wallet descriptor

```
desc="wsh(sortedmulti(3,${xpubs["xpub_1"]},${xpubs["xpub_2"]},${xpubs["xpub_3"]},${xpubs["xpub_4"]},${xpubs["xpub_5"]},${xpubs["xpub_6"]},${xpubs["xpub_7"]}))"

checksum=$(./bitcoin-cli getdescriptorinfo $desc | jq -r '.checksum')

multisig_desc="[{\"desc\": \"${desc}#${checksum}\", \"active\": true, \"timestamp\": \"now\"}]"

./bitcoin-cli -named createwallet "multisig_watch_wallet" true true

./bitcoin-cli -rpcwallet="multisig_watch_wallet" importdescriptors "$multisig_desc"

./bitcoin-cli -rpcwallet="multisig_watch_wallet" getwalletinfo
```

## Step B5: [offline computer] Export the watch only wallet descriptor

Grab the second USB stick (with no tape), this will be the transfer USB, Insert it into the offline computer. Copy `~/.bitcoin/wallets/multisig_watch_wallet` onto the transfer USB and then remove the transfer USB from the offline computer.

NOTE: If you are using the file explorer to drag & drop to copy files you will need to click the drop down arrow in the top right corner of the window and click on "Show hidden files". The `~/.bitcoin` folder is hidden by default.

Insert the transfer USB into the online computer. Copy the multisig_watch_wallet into the `~/.bitcoin/wallets` folder. When Bitcoin Core is finished syncing you can now load this wallet on the online computer with the following terminal command.

`./bitcoin-cli loadwallet "multisig_watch_wallet"`

With this wallet loaded on the online computer, can now use either bitcoin-cli or bitcoin-qt (Bitcoin Core's graphical user interface) to see the transaction history, check the balance of the wallet, generate receive addresses, generate PSBTs for export, and broadcast fully signed Bitcoin Transactions.


# Step B6: [offline computer] Backup keys
Now back up each of the 7 keys and the wallet descriptor.

Use brasero to create 7 mdisc backups. These files can be found in the `~/.bitcoin/wallets` folder. Take an M disc and write the number 1 on it with a permenant marker, insert disc 1 into the USB connected disc drive. Then use brasero create an .ISO of key_1 & the multisig_watch_wallet from `/.bitcoin/wallets` along with README.md which is a copy of this guide. Burn this .ISO to disc 1. Repeat for all 7 keys.

1 = key_1 & multisig_watch_wallet

2 = key_2 & multisig_watch_wallet

3 = key_3 & multisig_watch_wallet

4 = key_4 & multisig_watch_wallet

5 = key_5 & multisig_watch_wallet

6 = key_6 & multisig_watch_wallet

7 = key_7 & multisig_watch_wallet

# C. [offline computer] Test your wallet backups

Stop Bitcoin Core

`./bitcoin-cli stop`

wait a moment for the daemon to finish shutting down

Delete the entire `~/.bitcoin/wallets` folder

start Bitcoin Core again

`./bitcoind`


## C1. [online computer] create a receive address

```
./bitcoin-cli -rpcwallet="multisig_watch_wallet" getnewaddress
```

Test the wallet by sending a very small amount of Bitcoin to this address (this should be less than $5). Alternatively, you can also generate a QR code for this address if you use Bitcoin-QT, which is Bitcoin Core's graphical user interface (GUI). Simply double click on "Bitcoin-QT" inside of `~/Bitcoin-31.1/bin`, then load "multisig_watch_wallet" in the GUI and generate a receive address for a QR code.


## C2. [online computer] Check the balance of the wallet

```
./bitcoin-cli -rpcwallet="multisig_watch_wallet" getbalances

```


## C3. [online computer] Create a transaction (replace $amount and $destination_addr with the right values, make sure these are correct!)

```
funded_psbt=$(./bitcoin-cli -rpcwallet="multisig_watch_wallet" -named \
 walletcreatefundedpsbt \
 outputs="{\"$destination_addr\": $amount}" \
 options='{"subtractFeeFromOutputs":[0]}' | jq -r '.psbt')

echo "$funded_psbt" > ~/Desktop/unsigned.psbt

```

Insert the transfer USB (no tape) into the online computer, copy the unsigned.psbt from the Desktop onto the transfer USB. Remove the transfer USB containing the PSBT from the online computer.


## C4. [offline computer] Sign the transaction

### [offline computer] Transfer the unsigned PSBT

Insert the transfer USB (no tape) into the offline computer. Copy or drag and drop unsigned.psbt from the transfer USB onto the Desktop.

### [offline computer] Load the keys
Choose 3 of the M-discs, insert them one at a time into the offline computer's USB connected disc drive, and copy the key_# directory into `~/.bitcoin/wallets`.

After copying a key run the following command, replace `key_#` with the name of the key you copied into `~/.bitcoin/wallets`

`./bitcoin-cli loadwallet "key_#"`

### [offline computer] Sign the PSBT

After loading all 3 of the keys, sign the PSBT with this script

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

### [offline computer] Export signed PSBT to transfer USB

Copy `signed.psbt` from the Desktop onto the transfer USB. Remove the transfer USB from the offline computer

### [online computer] import signed PSBT to online computer

Insert the transfer USB into the online computer. Copy signed.psbt from the transfer USB onto the Desktop.

## C5. [online computer] broadcast transaction

```
psbt=$(cat ~/Desktop/signed.psbt)
hex=$(./bitcoin-cli finalizepsbt "$psbt" | jq -r '.hex')
./bitcoin-cli sendrawtransaction "$hex"

```

Repeat this process until you've tested all 7 of your key backups. This will require 3 test transactions total 

1st transasction: key1, key2, key3 

2nd transaction: key4, key5, key6

3rd transaction key7, + any 2 other keys

## C6. Geographically Distribute Backups

The next step is to place each of your 7 back up discs into 7 different envelopes. Mark them with something nondescript like "Do not open. Last will and testment of <your_name>. Pass this onto to next of kin."

The 7 envelopes must be geographically distributed to 7 different locations. 

You now have a secure, Bitcoin multisig vault that can only accessed by gathering 3 geographically distributed keys. 


# D. How to use your wallet normally

By this point you should already have a good understanding of how this works. The offline computer does not have any persistence. This is for your security, so no keys are ever written to the computers storage, they can never be recovered without your backup discs. Each time you wish to sign a psbt with the offline computer follow these steps carefully...

Insert Linux USB into offline computer, click try ubuntu

Connect to WIFI or LAN, Download latest version of Bitcoin Core, unpack the tarball

### IMPORTANT: ALWAYS Disable networking before inserting any key material

`nmcli networking off`

From here the process for spending from your multisig is the same as above.

Next time you want to spend Bitcoin from the multisig:
1. [online computer] Create the unsigned PSBT on the online computer, drag the unsigned PSBT into the transfer USB (see step C3)
2. [offline computer] insert transfer USB into the offline computer, drag the unsigned PSBT onto the desktop
3. [offline computer] collect any 3 of your key discs, insert them 1 at a time and drag the key folders into the `~/.bitcoin/wallets` folder (see step C4)
4. [offline computer] load the wallets from the terminal
5. [offline computer] sign the PSBT
6. [offline computer] drag the signed PSBT from the desktop onto the transfer USB, remove the transfer USB and insert it into the online computer
7. [online computer] drag the signed PSBT from the transfer USB onto the desktop
7. [online computer] broadcast the signed PSBT (see step C5)


For security you should always turn off the offline computer after you finish signing and exporting a PSBT.

These two laptops should be deidcated for use with Bitcoin Core ONLY. DO NOT use these two dedicated laptops for any other purpose or software.


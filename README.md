# How to set up secure bitcoin storage

## You will need:

-2 Dedicated Laptops (8GB RAM minimum, 64GB storage minimum) (try refurbished thinkpads) (Chromebooks will not work)

-2 Fresh USB sticks (16GB minimum) (try kingston datatravelers 64GB)

-8 pack of Milleniata brand M-disc DVDs 4.7GB (do not buy any other brand than millenniata)

-A usb powered disc drive capable of writing M-Disc DVDs (try ASUS zendrive)

estimated total cost (using amazon for reference):
~$450


<!-- 
TODO break this out into a seperate subguide
### Optional:

-a 2 TB SATA SSD if you want a full archival node(ensure its the right size for your laptop, 2.5 inch SATA is what you should look for) (try crucial BX500)

estimated additional cost (using amazon for reference):
~$255

This will be your full node. If you've opted for a full archival node you must replace the SATA SSD by first taking the computer apart. You will only need a small phillips screwdriver to do this in most cases. Unplug the computer, remove the screws the bottom cover, and swap out the drives.  -->


## Step 1. [primary computer] Install Ubuntu

Pick one laptop to be your primary computer. Place a sticker or a small piece of tape on this laptop to mark it. 

Go to ubuntu.com/download/desktop

You will most likely need the Intel or AMD 64-bit architecture, download the latest version (26.04 as of this post).

Grab one of your USB sticks and mark it with a sticker or a piece of tape. This is your linux USB.

From here, depending on the computer you are using to set these things up you will need to flash one of your USB sticks with the ubuntu installer.

The easiest way to do this is with dd on the command line if you already have access to a Linux terminal, however exercise caution doing this.

If you are on Windows you can download an app called Rufus and use that to create your live installer. MacOS users can use dd from the terminal or download an app called balenaEtcher.

Once you have your linux USB the next step will be to install linux on your primary machine (the one with the tape). Doing this requires turning off the computer, inserting your linux USB (the one with the tape) into your primary computer (also the one with the tape) and turning the power back on. If this doesn't work on the first try, you might need to change your boot order within the BIOS.

After the splash screen select `Install Ubuntu`.

During installation, simply proceed with all of the default settings, but on the encryption screen choose LUKS encryption, remember to write down your password.


## Step 2. [primary computer] Install security updates

Once you've finished installing linux, you need to install security updates. First connect to WIFI or LAN.

Open a terminal with `Ctrl + Alt + T`. Then type or copy and paste the following commands into the terminal.

`sudo apt update`

Press enter, then press Y if prompted and press enter again, wait for it to finish

`sudo apt full-upgrade`

press enter, then press Y if prompted and press enter again, wait for it to finish


## Step 3. [primary computer] Download the latest version of Bitcoin Core

Do NOT go to bitcoin.org (that website is unfortunately owned by scammers)

Go to https://bitcoincore.org/en/download

Click the big blue "Download Bitcoin Core" button.

Unpack the Tarball, you can go into your ~/Downloads folder and right click on the bitcoin core file and click "Extract" then drag the new folder it creates into the HOME directory.

## Step 4. [primary computer] Start Bitcoin Core

Open the folder in your Home directory and then click on Bitcoin-31.1

### IMPORTANT: If you opted not to upgrade your primary computer's storage drive to atleast 2TB you need to set your node to pruned mode. In this case it will still do fully validate all historical bitcoin blocks, but it will not store a full copy of the bitcoin blockchain on your computer.

Inside of Bitcoin-31.1 double click on the file named "Bitcoin.conf". 

Add the following line anywhere in the file
`prune=5500`
Then click save and close the bitcoin.conf file.

Open a terminal with `Ctrl + Alt + T`. Navigate into the Bitcoin folder we extracted in step 3 using the change directory command `cd`. We need to be inside of the `/bin` folder. 

Once inside the `/bin` folder, copy and paste the following command in the terminal.

`./bitcoind`

Press enter

Your computer will now begin syncing the Bitcoin blockchain. This can take a while (days or weeks), if you like to speed it up you can use assumeutxo.

It is important to always properly shut down bitcoin core before turning off your computer, this prevents wasted time spent resyncing in the future. To do this navigate into the Bitcoin-31.1/bin folder and run this command.

`./bitcoin-cli stop`


## Step 5: Switch to offline computer

Now switch to your second computer, this will be our offline computer. 

Insert your linux USB and turn the computer on. 

After the splash screen select `Try Ubuntu`.


## Step 6: [offline computer] Install Updates and Software

### [offline computer] Install updates

Once you are on the desktop connect to WIFI or LAN.

Open a terminal and copy and paste the following commands.

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

Unpack the Tarball, you can go into your ~/Downloads folder and right click on the bitcoin core file and click "Extract" then drag the new folder it creates into the HOME directory.


## Step 7: [offline computer] Disable networking on your offline device

Open the terminal and run the following command:
`nmcli networking off`


## [offline computer] Creating Your Multi Signature Cold Wallet on Bitcoin Core

On your offline computer start by opening a terminal and navigating into the Bitcoin-31.1/bin folder

start the Bitcoin Daemon

`./bitcoind -daemon`


## [offline computer] create 7 wallets

Within ~/Bitcoin-31.1/bin in your terminal copy and paste the following...

```
for ((n=1;n<=7;n++))
do
 ./bitcoin-cli createwallet "key_${n}"
done
```

Press Enter


## [offline computer] capture Extended Public Keys (XPUBs) from each wallet 

```
declare -A xpubs

for ((n=1;n<=7;n++))
do
 xpubs["xpub_${n}"]=$(./bitcoin-cli -rpcwallet="key_${n}" listdescriptors | jq '.descriptors | [.[] | select(.desc | startswith("wpkh") and contains("/0/*") )][0] | .desc' | grep -Po '(?<=\().*(?=\))' | sed 's /0/\* /<0;1>/* ')
done

```

## [offline computer] create the multisig wallet descriptor

```
desc="wsh(sortedmulti(3,${xpubs["xpub_1"]},${xpubs["xpub_2"]},${xpubs["xpub_3"]},${xpubs["xpub_4"]},${xpubs["xpub_5"]},${xpubs["xpub_6"]},${xpubs["xpub_7"]}))"

checksum=$(./bitcoin-cli getdescriptorinfo $desc | jq -r '.checksum')

multisig_desc="[{\"desc\": \"${desc}#${checksum}\", \"active\": true, \"timestamp\": \"now\"}]"

./bitcoin-cli -named createwallet "multisig_watch_wallet" true true

./bitcoin-cli -rpcwallet="multisig_watch_wallet" importdescriptors "$multisig_desc"

./bitcoin-cli -rpcwallet="multisig_watch_wallet" getwalletinfo
```

## [offline computer] Export your watch only wallet descriptor

Insert your second USB stick into your offline computer, the one without the tape on it. Copy `~/.bitcoin/wallets/multisig_watch_wallet` onto your USB stick. Remove this USB stick from your offline computer.

Insert this USB stick into your online computer. Copy the multisig_watch_wallet into your `~/.bitcoin/wallets` folder. When Bitcoin Core is finished syncing you can now load this wallet on your online machine with the following terminal command.

`./bitcoin-cli loadwallet "multisig_watch_wallet"`

With this wallet loaded on your online computer, can now use either bitcoin-cli or bitcoin-qt (Bitcoin Core's graphical user interface) to see your transaction history, check the balance of the wallet, generate receive addresses, generate PSBTs for export, and broadcast fully signed Bitcoin Transactions.


# [offline computer] Backup keys
Now back up each of the 7 keys and your wallet descriptor.

Use brasero to create 7 mdisc backups. These files can be found in your `~/.bitcoin/wallets` folder. Take an M disc and write the number 1 on it with a permenant marker, insert disc 1 into your usb connected disc drive. Then use brasero create an .ISO of key_1 & the multisig_watch_wallet from `/.bitcoin/wallets`. Burn this .ISO to disc 1. Repeat for all 7 keys.

1 = key_1 & multisig_watch_wallet

2 = key_2 & multisig_watch_wallet

3 = key_3 & multisig_watch_wallet

4 = key_4 & multisig_watch_wallet

5 = key_5 & multisig_watch_wallet

6 = key_6 & multisig_watch_wallet

7 = key_7 & multisig_watch_wallet

# [offline computer] Test your backups

Stop Bitcoin Core

`./bitcoin-cli stop`

wait a moment for the daemon to finish shutting down

Delete your entire `~/.bitcoin/wallets` folder

start Bitcoin Core again

`./bitcoind`


## [online computer] create a receive address

```
./bitcoin-cli -rpcwallet="multisig_watch_wallet" getnewaddress
```

Test the wallet by sending a very small amount of Bitcoin to this address (this should be less than $5). Alternatively, you can also generate a QR code for this address if you use Bitcoin-QT, which is Bitcoin Core's graphical user interface (GUI). Simply double click on "Bitcoin-QT" inside of Bitcoin-31.1/bin, then load "multisig_watch_wallet" in the GUI and generate a receive address for a QR code.


## [online computer] Check the balance of the wallet

```
./bitcoin-cli -rpcwallet="multisig_watch_wallet" getbalances

```


## [online computer] Create a transaction (replace $amount and $destination_addr with the right values, make sure these are correct!)

```
funded_psbt=$(./bitcoin-cli -rpcwallet="multisig_watch_wallet" -named \
 walletcreatefundedpsbt \
 outputs="{\"$destination_addr\": $amount}" \
 options='{"subtractFeeFromOutputs":[0]}' | jq -r '.psbt')

echo "$funded_psbt" > ~/Desktop/unsigned.psbt

```

Insert your USB (without tape) into your online computer, copy your unsigned.psbt from your Desktop onto the USB stick. Remove the USB that contains your PSBT from your online computer.


## [offline computer] Sign the transaction

### [offline computer] Transfer your unsigned PSBT

Insert your USB (without tape) into your offline computer. Copy or drag and drop unsigned.psbt from your USB onto your Desktop.

### [offline computer] Load your keys
Choose 3 of your M-discs, one at a time, insert them into your offline computer and copy the key_# directory into `~/.bitcoin/wallets`.

After copying a key run the following command, replace `key_#` with the name of the key you copied into `~/.bitcoin/wallets`

`./bitcoin-cli loadwallet "key_#"`

### [offline computer] Sign your PSBT

After loading all 3 of your keys, sign the PSBT with this script

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

<!--reference script ```
psbt_1=$(./bitcoin-cli -rpcwallet="key_1" walletprocesspsbt $funded_psbt | jq -r '.psbt')

psbt_2=$(./bitcoin-cli -rpcwallet="key_2" walletprocesspsbt $psbt_1 | jq -r '.psbt')

psbt_3=$(./bitcoin-cli -rpcwallet="key_3" walletprocesspsbt $psbt_2 | jq -r '.psbt')

psbt_4=$(./bitcoin-cli -rpcwallet="key_4" walletprocesspsbt $psbt_3 | jq -r '.psbt')

psbt_5=$(./bitcoin-cli -rpcwallet="key_5" walletprocesspsbt $psbt_4 | jq -r '.psbt')

psbt_6=$(./bitcoin-cli -rpcwallet="key_6" walletprocesspsbt $psbt_5 | jq -r '.psbt')

psbt_7=$(./bitcoin-cli -rpcwallet="key_7" walletprocesspsbt $psbt_6 | jq -r '.psbt')

finalized_psbt_hex=$(./bitcoin-cli finalizepsbt $psbt_6 | jq -r '.hex')

``` -->

### [offline computer] Export signed PSBT to USB

Copy `signed.psbt` from your Desktop onto your USB. Remove the USB from your offline computer

### [online computer] import signed PSBT to online computer

Insert your USB into your online computer. Copy signed.psbt from your usb onto your Desktop.

## [online computer] broadcast transaction

```
psbt=$(cat ~/Desktop/signed.psbt)
hex=$(./bitcoin-cli finalizepsbt "$psbt" | jq -r '.hex')
./bitcoin-cli sendrawtransaction "$hex"

```

Repeat this process util you've tested all of your key backups. You now have a secure Bitcoin multisig vault.

# How to use your wallet normally

By this point you should already have a good understanding of how this works. Your offline computer does not have any persistence. This is for your security, so no keys are ever written to the computers storage and can never be recovered. Each time you wish to sign a psbt with your offline computer follow these steps carefully...

Insert Linux USB into offline computer, click try ubuntu

Connect to WIFI or LAN, Download latest version of Bitcoin Core, unpack the tarball

### IMPORTANT: ALWAYS Disable networking before inserting any key material

`nmcli networking off`

From here the proces for spending from your multisig is the same as above.

Next time you want to spend your Bitcoin from the multisig:
1. [online computer] Create the unsigned PSBT on your online computer, drag the unsigned PSBT into your USB stick
2. [offline computer] insert USB stick into your offline computer, drag the unsigned PSBT onto the desktop
3. [offline computer] collect any 3 of your key discs, insert them 1 at a time and drag the key folders into your .bitcoin/wallets folder
4. [offline computer] load the wallets from the terminal
5. [offline computer] sign the PSBT
6. [offline computer] drag the signed PSBT from your desktop onto your usb stick, remove the USB and insert it into your node
7. [online computer] drag the signed PSBT from your USB onto your desktop
7. [online computer] broadcast the signed PSBT


For security you should always turn off your offline machine after you finish signing and exporting a PSBT.

Your laptops should be deidcated for use with Bitcoin Core ONLY. DO NOT use these two dedicated laptops for any other purpose or software. 

TODO pruning is fine? Verify this is true



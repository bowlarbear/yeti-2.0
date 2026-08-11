# How to set up secure bitcoin storage

## You will need:

-2 Dedicated Laptops (8GB RAM minimum, 64GB storage minimum) (try refurbished thinkpads) (Chromebooks will not work)

-2 Fresh USB sticks (16GB minimum) (try kingston datatravelers 64GB)

-8 pack of M disc DVDs (BD-R are cheapest)

-A usb powered disc drive capable of read/write DVDs (try ASUS zendrive)

estimated total cost (using amazon for reference):
~$450


### Optional:

-a 2 TB SATA SSD if you want a full archival node(ensure its the right size for your laptop, 2.5 inch SATA is what you should look for) (try crucial BX500)

estimated additional cost (using amazon for reference):
~$255

Pick one laptop to be your primary computer. Place a sticker or a small piece of tape on this laptop to mark it. This will be your full node. If you've opted for a full archival node you must replace the SATA SSD by first taking the computer apart. You will only need a small phillips screwdriver to do this in most cases. Unplug the computer, remove the screws the bottom cover, and swap out the drives. 


## Step 1. Install Ubuntu on both laptops

Go to ubuntu.com/download/desktop

You will most likely need the Intel or AMD 64-bit architecture, download the latest version (26.04 as of this post)

From here, depending on the computer you are using to set these things up you will need to flash one of you USB sticks with the ubuntu installer

The easiest way to do this is with dd on the command line if you already have access to a linux terminal, however exercise caution doing this.

If you are on Windows download an app called Rufus and use that to create your live installer. MacOS users can use dd from the terminal or download balenaEtcher

During installation, you can use all of the default settings, however when you reach the encryption screen select LUKS encryption.


## Step 2. Install security updates

You can do this with the Software Updater in the GUI, but an easier way is to open a terminal with `Ctrl + Alt + T`

Then type or copy and paste the follow commands into the terminal.

`sudo apt update`

Press enter, then press Y if prompted and press enter again, wait for it to finish

`sudo apt upgrade`

press enter, then press Y if prompted and press enter again, wait for it to finish


## Step 3. Download the latest version of Bitcoin Core

Do NOT go to bitcoin.org (that website is unfortunately owned by scammers)

Go to bitcoincore.org/en/download

Click the big blue "Download Bitcoin Core" button.

Unpack the Tarball, you can go into your ~/Downloads folder and right click on the bitcoin core file and click "Extract" then drag the new folder it creates into HOME.


## Step 4: Download Brasero

`sudo apt install brasero`


## Step 5: Disable networking on your offline device

Open the terminal and run the following command:
`nmcli networking off`

Note: If you ever need to disable this for any reason simply run 

`nmcli networking on`

Step 6: Sync your Full node on online machine

Open the folder in your Home directory and then click on Bitcoin-31.1

### IMPORTANT: If you opted not to upgrade your storage drive to atleast 2TB you need to set your node to pruned mode. In this case it will still do fully validate all historical bitcoin blocks, but it will not store a full copy of the bitcoin blockchain on your computer.

Inside of Bitcoin-31.1 double click on the file named "Bitcoin.conf". 

Add the following line anywhere in the file
`prune=5500`
Then click save and close the bitcoin.conf file.

Now go back to Bitcon-31.1 and click on the folder called "bin" (this stands for binaries) and its where all of the code for the bitcoin software lives.

We are going to use Bitcoin's Graphical User Interface which is called "Bitcoin-QT" so double click on the file inside of Bin called Bitcoin-QT. Your Bitcoin node will now sync the blockchain, this can take a long time, so leave the computer running until it is finished. 

It is important to always shut down bitcoin core before turning off your computer, this prevents wasted time resyncing in the future.







## Creating a Cold Multi Signature Wallet on Bitcoin Core

On your offline computer start by opening a terminal and navigating into the Bitcoin-31.1/bin folder

start the Bitcoin Daemon

`./bitcoind -daemon`


## create 7 wallets

```
for ((n=1;n<=7;n++))
do
 ./bitcoin-cli createwallet "key_${n}"
done
```


## capture descriptors from each wallet 

```
declare -A xpubs

for ((n=1;n<=7;n++))
do
 xpubs["xpub_${n}"]=$(./bitcoin-cli -rpcwallet="key_${n}" listdescriptors | jq '.descriptors | [.[] | select(.desc | startswith("wpkh") and contains("/0/*") )][0] | .desc' | grep -Po '(?<=\().*(?=\))' | sed 's /0/\* /<0;1>/* ')
done

```

## create the multisig wallet descriptor

```
desc="wsh(sortedmulti(3,${xpubs["xpub_1"]},${xpubs["xpub_2"]},${xpubs["xpub_3"]},${xpubs["xpub_4"]},${xpubs["xpub_5"]},${xpubs["xpub_6"]},${xpubs["xpub_7"]}))"

checksum=$(./bitcoin-cli getdescriptorinfo $desc | jq -r '.checksum')

multisig_desc="[{\"desc\": \"${desc}#${checksum}\", \"active\": true, \"timestamp\": \"now\"}]"

./bitcoin-cli -named createwallet "multisig_watch_wallet" true true

./bitcoin-cli -rpcwallet="multisig_watch_wallet" importdescriptors "$multisig_desc"

./bitcoin-cli -rpcwallet="multisig_watch_wallet" getwalletinfo
```

TODO insert descriptor export/import to node here

# Test your wallet


## create a receive address

```
./bitcoin-cli -rpcwallet="multisig_watch_wallet" getnewaddress
```

Test the wallet by sending a very small amount of Bitcoin to this address (this should be less than $5)



## Check the balance of the wallet

```
./bitcoin-cli -rpcwallet="multisig_watch_wallet" getbalances

```



## Create a transaction (ensure amount and destination address are correct)

```
funded_psbt=$(./bitcoin-cli -rpcwallet="multisig_watch_wallet" -named \
 walletcreatefundedpsbt \
 outputs="{\"$destination_addr\": $amount}" | jq -r '.psbt')

```

TODO insert PSBT export/import to offline machine here


## Sign the transaction

TODO make this script more agnostic, should be able to work with any 3 wallets loaded

TODO make this script output PSBT to file in ~/Desktop

```
psbt_1=$(./bitcoin-cli -rpcwallet="key_1" walletprocesspsbt $funded_psbt | jq -r '.psbt')

psbt_2=$(./bitcoin-cli -rpcwallet="key_2" walletprocesspsbt $psbt_1 | jq -r '.psbt')

psbt_3=$(./bitcoin-cli -rpcwallet="key_3" walletprocesspsbt $psbt_2 | jq -r '.psbt')

psbt_4=$(./bitcoin-cli -rpcwallet="key_4" walletprocesspsbt $psbt_3 | jq -r '.psbt')

psbt_5=$(./bitcoin-cli -rpcwallet="key_5" walletprocesspsbt $psbt_4 | jq -r '.psbt')

psbt_6=$(./bitcoin-cli -rpcwallet="key_6" walletprocesspsbt $psbt_5 | jq -r '.psbt')

psbt_7=$(./bitcoin-cli -rpcwallet="key_7" walletprocesspsbt $psbt_6 | jq -r '.psbt')

finalized_psbt_hex=$(./bitcoin-cli finalizepsbt $psbt_6 | jq -r '.hex')

```

TODO insert PSBT export/import to node here

TODO rewrite broadcast transaction to use psbt file from ~/Desktop

## broadcast transaction
`./bitcoin-cli sendrawtransaction $finalized_psbt_hex`


# Backup keys
Now that you've successfully tested your multisig wallet we must back up each of the 7 keys and your wallet descriptor.

Use brasero to create 7 mdisc backups. These files can be found in your ~/.bitcoin/wallets folder

1 = key_1 & multisig_watch_wallet

2 = key_2 & multisig_watch_wallet

3 = key_3 & multisig_watch_wallet

4 = key_4 & multisig_watch_wallet

5 = key_5 & multisig_watch_wallet

6 = key_6 & multisig_watch_wallet

7 = key_7 & multisig_watch_wallet

## Test your backups
Delete your ~/.bitcoin/wallets folder

perform another full send/receive test using your backups


## Cleanup
Shred the password for the offline computer. Reinstall Ubuntu on the offline computer and reinstall Bitcoin Core for your convenience and then remember to turn networking off on the device.

`nmcli networking off`

Next time you want to spend your Bitcoin from the multisig:
1. [online machine] Create a PSBT on your node machine, drag the PSBT into your USB stick
2. [offline machine] insert PSBT USB stick into your offline machine, drag the PSBT onto the desktop
3. [offline machine] collect any 3 of your key discs, insert them 1 at a time and drag the key folders into your .bitcoin/wallets folder
4. [offline machine] load the wallets from the terminal
5. [offline machine] sign and finalize the PSBT
6. [offline machine] drag the PSBT onto your usb stick, remove the USB and insert it into your node
7. [online machine] broadcast the PSBT




TODO refine the sign transaction script to be more agnostic

TODO write psbt to file and transfer to node for broadcast on USB stick

TODO pruning is fine? Verify this is true



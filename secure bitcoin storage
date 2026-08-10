How to set up secure bitcoin storage

You will need:
-2 Dedicated Laptops (8GB RAM minimum, 64GB storage minimum) I recommend refurbished thinkpads ~157.83 ea 
-2 Fresh USB sticks (16GB minimum) I recommend kingston datatravelers 64GB ~8.99 ea
-Atleast 8 pack of M disc DVDs (BD-R are cheapest) (15 pack millenniata M-disc price reference) ~75.00
-A usb powered disc drive capable of read/write DVDs I recommend ASUS zendrive ~39.99

total cost (using amazon for reference):
~$457.62



Optional:
-a 2 TB SATA SSD if you want a full archival node(ensure its the right size for your laptop, 2.5 inch SATA is what you should look for)

additional cost (using amazon for reference):
~$255 (crucial BX5000 price reference)

Replace the SATA SSD in the laptop you intend to use as your full node. You will only need a small phillips screwdriver to do this in most cases. Unplug the computer, remove the screws the bottom cover, and swap out the drives. 




Before starting, place a sticker or a piece of tape on the laptop you wish to use as your node computer so you can remember which is which. 


Step 0. Install linux with full disc encryption
choose 4 or 5 random words for each computer's encryption password, write these passwords down



Step 1. Install Ubuntu on both laptops

Go to ubuntu.com/download/desktop

You will most likely need the Intel or AMD 64-bit architecture, download the latest version (26.04 as of this post)

From here, depending on the computer you are using to set these things up you will need to flash one of you USB sticks with the ubuntu installer

The easiest way to do this is with dd on the command line if you already have access to a linux terminal, however exercise caution doing this.

If you are on Windows download an app called Rufus and use that to create your live installer. MacOS users can use dd from the terminal or download balenaEtcher









Step 2. Install security updates

You can do this with the Software Updater in the GUI, but an easier way is to open a terminal with `Ctrl + Alt + T`

Then type or copy and paste the follow commands into the terminal.

`sudo apt update`

Press enter, then press Y if prompted and press enter again, wait for it to finish

`sudo apt upgrade`

press enter, then press Y if prompted and press enter again, wait for it to finish









Step 3. Download the latest version of Bitcoin Core

Do NOT go to bitcoin.org (that website is unfortunately owned by scammers)

Go to bitcoincore.org/en/download

Click the big blue "Download Bitcoin Core" button.

Unpack the Tarball, you can go into your ~/Downloads folder and right click on the bitcoin core file and click "Extract" then drag the new folder it creates into HOME.








Step 4: Download Brasero

`sudo apt install brasero`







Step 5: Disable networking on your offline device

Open the terminal and run the following command:
`nmcli networking off`

Note: If you ever need to disable this for any reason simply run 

`nmcli networking on`







Step 6: Sync your Full node on online machine

Open the folder in your Home directory and then click on Bitcoin-31.1

IMPORTANT: If you opted not to upgrade your storage drive to atleast 2TB you need to set your node to pruned mode. In this case it will still do fully validate all historical bitcoin blocks, but it will not store a full copy of the bitcoin blockchain on your computer.

Inside of Bitcoin-31.1 double click on the file named "Bitcoin.conf". 

Add the following line anywhere in the file
`prune=5500`
Then click save and close the bitcoin.conf file.

Now go back to Bitcon-31.1 and click on the folder called "bin" (this stands for binaries) and its where all of the code for the bitcoin software lives.

We are going to use Bitcoin's Graphical User Interface which is called "Bitcoin-QT" so double click on the file inside of Bin called Bitcoin-QT. Your Bitcoin node will now sync the blockchain, this can take a long time, so leave the computer running until it is finished. 

It is important to always shut down bitcoin core before turning off your computer, this prevents wasted time resyncing in the future.







Creating a Cold Multi Signature Wallet on Bitcoin Core

On your offline computer start by opening a terminal and navigating into the Bitcoin-31.1/bin folder

start the Bitcoin Daemon

`./bitcoind -daemon`


create 7 wallets

```
for ((n=1;n<=7;n++))
do
 ./bitcoin-cli createwallet "key_${n}"
done
```


capture descriptors from each wallet 

```
declare -A xpubs

for ((n=1;n<=7;n++))
do
 xpubs["xpub_${n}"]=$(./bitcoin-cli -rpcwallet="key_${n}" listdescriptors | jq '.descriptors | [.[] | select(.desc | startswith("wpkh") and contains("/0/*") )][0] | .desc' | grep -Po '(?<=\().*(?=\))' | sed 's /0/\* /<0;1>/* ')
done

```

create the multisig wallet descriptor

```
desc="wsh(sortedmulti(3,${xpubs["xpub_1"]},${xpubs["xpub_2"]},${xpubs["xpub_3"]},${xpubs["xpub_4"]},${xpubs["xpub_5"]},${xpubs["xpub_6"]},${xpubs["xpub_7"]}))"

checksum=$(./bitcoin-cli getdescriptorinfo $desc | jq -r '.checksum')

multisig_desc="[{\"desc\": \"${desc}#${checksum}\", \"active\": true, \"timestamp\": \"now\"}]"

./bitcoin-cli -named createwallet "multisig_watch_wallet" true true

./bitcoin-cli -rpcwallet="multisig_watch_wallet" importdescriptors "$multisig_desc"

./bitcoin-cli -rpcwallet="multisig_watch_wallet" getwalletinfo
```



create a receive address

```
./bitcoin-cli -rpcwallet="multisig_watch_wallet" getnewaddress
```

Test the wallet by sending a very small amount of Bitcoin to this address (this should be less than $5)



Check the balance of the wallet

```
./bitcoin-cli -rpcwallet="multisig_watch_wallet" getbalances

```



Create a transaction (ensure amount and destination address are correct)

```
funded_psbt=$(./bitcoin-cli -rpcwallet="multisig_watch_wallet" -named \
 walletcreatefundedpsbt \
 outputs="{\"$destination_addr\": $amount}" | jq -r '.psbt')

```




Sign the transaction

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

broadcast transaction
./bitcoin-cli sendrawtransaction $finalized_psbt_hex


Now that you've successfully tested your multisig wallet we must back up each of the 7 keys and your wallet descriptor.

Use brasero to create 7 mdisc backups. These files can be found in your ~/.bitcoin/wallets folder

1 = key_1 & multisig_watch_wallet
2 = key_2 & multisig_watch_wallet
3 = key_3 & multisig_watch_wallet
4 = key_4 & multisig_watch_wallet
5 = key_5 & multisig_watch_wallet
6 = key_6 & multisig_watch_wallet
7 = key_7 & multisig_watch_wallet



Shred the password for the offline computer. Reflash the offline computer with linux for convenience.


TODO refine the sign transaction script to be more agnostic

TODO write psbt to file and transfer to node for broadcast on USB stick

TODO export the watch only to full node on usb stick

TODO pruning is fine?



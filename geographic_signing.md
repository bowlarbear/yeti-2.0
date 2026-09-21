# Sign at One Key Location

Follow these steps at each of three key locations. Use a fresh boot session and load only the key stored at that location.

## Import the PSBT

Copy `current.psbt` from the transfer USB to the Desktop.

[Verify the PSBT contents](verify_psbt.md), using `current.psbt` in place of `unsigned.psbt` in the decode command.

## Load One Key

Copy that location's `key_#` folder into `~/.bitcoin/wallets` and replace `key_#` below with its name.

```
~/bitcoin-31.1/bin/bitcoin-cli loadwallet "key_#"
```

## Sign Once

```
psbt=$(cat ~/Desktop/current.psbt)
psbt=$(~/bitcoin-31.1/bin/bitcoin-cli -rpcwallet="key_#" walletprocesspsbt "$psbt" | jq -r '.psbt')
echo "$psbt" > ~/Desktop/current.psbt
```

Copy `current.psbt` back to the transfer USB, remove the USB, and power off the offline computer.

Repeat these steps at the next key location. After the third signature, return the transfer USB to the online computer.

Separate offline computers or independent people at each location provide stronger isolation, but are not required.

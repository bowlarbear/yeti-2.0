# Pruning

If you wish to use a pruned node you should first copy the `~/bitcoin-32.1/bitcoin.conf` into your `~/.bitcoin` folder on your online computer. Then you will want to open the `bitcoin.conf` file for editing (use Text Editor unless you know vim or nano).

On a new line (any line that does not start with a `##`) add the following `prune=5500`. This number corresponds to the size of your prune cache. The number provided here is 5.5GB. It is not recommended that you exceed approximately 70% of your available storage space on your online computer for your prune cache, but a larger prune cache is better.

If you have 64GB of internal storage space use `prune=10000`.

If you have 128GB of internal storage space use `prune=80000`.

If you have 256GB of internal storage space use `prune=160000`.

If you have 512GB of internal storage space use `prune=350000`.

If you have 1TB of internal storage space use `prune=750000`.

In order for these changes to take effect after changing this file, you must stop your node if it is already running:

```
~/bitcoin-32.1/bin/bitcoin-cli stop
```

Then start the daemon again to start pruning:

```
~/bitcoin-32.1/bin/bitcoind -daemon

```


## Error: "Wallet Loading Failed. Prune: last wallet synchronization goes beyond pruned data"

If you encounter the above error after attempting to load your "multisig_watch_wallet" with:

```
~/bitcoin-32.1/bin/bitcoin-cli loadwallet "multisig_watch_wallet"
```

...This is a common issue encountered when importing wallets to a pruned node, the reason this error occurs is because a pruned node does not keep the full blockchain history.

The best way to avoid encountering this error is to use a [full archival node](https://github.com/bowlarbear/yeti-2.0/blob/main/FAQ.md#q-can-i-use-a-pruned-node). The second best way to avoid this error is to load your wallet into your online computer while it is still performing the initial sync of the Bitcoin blockchain, however, if you do encounter this error there is an easy solution...

### How to fix the problem

Follow these instructions carefully:

On the online computer...

Stop the Node.

```
~/bitcoin-32.1/bin/bitcoin-cli stop
```

Wait about 10 seconds for the node to fully shut down.

Then, restart the node with this command:

```
~/bitcoin-32.1/bin/bitcoind -daemon -reindex -nowallet
```

The node is going to start reindexing, wait a couple of minutes for it to sync the headers.

Then load the watch-only wallet with this command:

```
~/bitcoin-32.1/bin/bitcoin-cli loadwallet "multisig_watch_wallet"
```

After this, you will need to wait for the wallet to finish re-syncing the blockchain and scanning your wallet's history.



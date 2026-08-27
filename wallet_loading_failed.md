# Error: "Wallet Loading Failed. Prune: last wallet synchronization goes beyond pruned data"

This is a common issue encountered when importing wallets to a pruned node, the reason this error occurs is because a pruned node does not keep the full blockchain history.

The best way to avoid encountering this error is to use a full archival node. The second best way to avoid this error is to load your wallet into your online computer while it is still performing the initial sync of the Bitcoin blockchain, however, if you do encounter this error there is an easy solution...

## How to fix the problem

Follow these instructions carefully:

On the online computer...

Stop the Node.

```
~/bitcoin-31.1/bin/bitcoin-cli stop
```

Wait about 10 seconds for the node to fully shut down.

Then, restart the node with this command:

```
~/bitcoin-31.1/bin/bitcoind -daemon -reindex -nowallet
```

The node is going to start reindexing, wait a couple of minutes for it to sync the headers.

Then load the watch only wallet with this command:

```
~/bitcoin-31.1/bin/bitcoin-cli loadwallet "multisig_watch_wallet"
```

After this, you will need to wait for the wallet to finish re-syncing the blockchain and scanning the your wallet's history.
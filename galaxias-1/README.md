# Quick Reference - (galaxias-1)

Seed

- 958a3fd40bab38a33bf08f03d3b97c9e6de24860@3.39.107.230:26656

Persistent Peers

- 3fb28d47955cd2f43487e1aa354f11f1fa3af76e@3.39.110.186:26656

RPC

- https://testnet-rpc.galaxychain.zone

<br/>
<br/>

### 🗣️ — Validators

---

If you want running a validator node on the Galaxy Testnet, check out to on the [Galaxy Testnet Validator Guide](https://github.com/galaxynetwork/networks/blob/main/galaxias-1/testnet-validator.md)

<br/>
<br/>

# Sync From Genesis Guide (galaxias-1)

This guide will show you how to sync from genesis to the new testnet. PLEASE NOTE, the first block may take a while (up to an hour) to get passed

Clone the galaxy repo and install v1.0.0

```
git clone https://github.com/galaxynetwork/galaxy.git
git checkout v1.0.0
make install
```

Set up testnet genesis

```
galaxyd init NODENAME --chain-id galaxias-1
cd ~/.galaxy/config/
curl https://media.githubusercontent.com/media/galaxynetwork/networks/main/galaxias-1/genesis.json > genesis.json
```

Edit the config.toml:

Add the seed node

```
958a3fd40bab38a33bf08f03d3b97c9e6de24860@3.39.107.230:26656
```

Add the persistent peers

```
3fb28d47955cd2f43487e1aa354f11f1fa3af76e@3.39.110.186:26656
```

Start galaxyd with --x-crisis-skip-assert-invariants flag.

```
galaxyd start --x-crisis-skip-assert-invariants
```

You only need to use this flag once. Your daemon will run the first block and then get stuck searching for peers. This can take an hour or more. After an hour or so, you will then sync blocks. If you cancel this process before finding two or more blocks, you will have to use `galaxyd unsafe-reset-all` and then start with the skip assert invariants flag again. After finding two or more blocks you are in the clear. You can cancel the daemon at any point you want and simply use `galaxyd start` to get it running again.

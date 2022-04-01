# Setting Up a Genesis Galaxy Validator

First of all, thank you for participating our journey.
We are at the beginning of creating our infinite, one and only universe. You are one of the pioneers of creating the galaxy universe.

**Before beginning, please check the following.**

Before to register genesis validator, please check your airdrop amount on this page: https://galaxychain.zone/airdrop.

1. Gentxs must be submitted by Before UTC on April 7 00:00.
2. Setting up process is almost the same as cosmos-based networks, especially Osmosis. Also, if you've participate as a validators on a cosmos-based network, this process is generally the same.
3. As you all know, Galaxy aims to create Metaverse based on NFT and unique worldview. By starting this new journey, we need a wide range of updates continuously to make our metaverse more the universe-like. For this new journey, we all have to actively communicate and get efforts. Please join this [Galaxy Discord](https://discord.gg/GrEcUCqr) and create a new universe.(validator regiter admin : whiteT#0899)

## OS/Minimum server spec

- System OS : Ubuntu 18.04
- 4 or more physical<sup>[1]</sup> CPU cores
- At least 500GB of SSD disk storage
- At least 8GB of memory
- At least 100mbps network bandwidth

### Install Go

Galaxy is built using Go and requires Go version 1.16+. In this example, we will be installing Go on the above Ubuntu 20.04:

```sh
# First remove any existing old Go installation
sudo rm -rf /usr/local/go

# Install the latest version of Go using this helpful script
curl https://raw.githubusercontent.com/canha/golang-tools-install-script/master/goinstall.sh | bash

# Update environment variables to include go
cat <<'EOF' >>$HOME/.profile
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin
EOF
source $HOME/.profile
```

To verify that Go is installed:

```sh
go version
# Should return go version go1.16.4 linux/amd64
```

### Get Galaxy Source Code

Use git to retrieve Galaxy source code from the [official repo](https://github.com/galaxies-labs/galaxy), and checkout the `genesis` tag, which contains the latest stable release.

```sh
git clone https://github.com/galaxies-labs/galaxy
cd galaxy
git checkout launch-gentxs
```

## Install galaxyd

You can now build Galaxy node software. Running the following command will install the executable galaxyd (Galaxy node daemon) to your GOPATH.

```sh
make install
```

### Verify Your Installation

Verify that everything is OK. If you get something _like_ the following, you've successfully installed Galaxy on your system.

```sh
galaxyd version --long

name: galaxy
server_name: galaxyd
version: launch-gentxs-1-gd19fd2e
commit: d19fd2e477d2abfb603658db00bc4e6328b881b8
build_tags: netgo,ledger
go: go version go1.16.6 darwin/amd64
```

If the software version does not match, then please check your `$PATH` to ensure the correct `galaxyd` is running.

### Save your Chain ID in galaxyd config

We recommend saving the mainnet `chain-id` into your `galaxyd`'s client.toml. This will make it so you do not have to manually pass in the chain-id flag for every CLI command.

```sh
galaxyd config chain-id galaxy-1
```

### Initialize your Node

Now that your software is installed, you can initialize the directory for galaxyd.

```sh
galaxyd init <your_moniker>
```

This will create a new `.galaxyd` folder in your HOME directory.

### Download Pregenesis File

You can now download the "pregenesis" file for the chain. This is a genesis file with the chain-id and airdrop balances.

```sh
cd $HOME/.galaxyd/config/

curl https://raw.githubusercontent.com/galaxies-labs/networks/main/galaxy-1/pregenesis.json > $HOME/.galaxyd/config/genesis.json
```

### Import Validator Key

The create a gentx, you will need the private key to an address that received an allocation in the airdrop.

There are a couple options for how to import a key into `galaxyd`.

You can import such a key into `galaxyd` via a mnemonic or exporting and importing a keyfile from an existing CLI.

#### Import Via Mnemonic

To import via mnemonic, you can do so using the following command and then input your mnemonic when prompted.

```sh
galaxyd keys add <key_name> --recover
```

#### Import From Another CLI

If you have the private key saved in the keystore of another CLI (such as gaiad), you can easily import it
into `galaxyd` using the following steps.

1. Export the key from an existing keystore. In this example we will use gaiad. When prompted, input a password to encrypt the key file with.

```sh
gaiad keys export <original_key_name>
```

2. Copy the output starting from the line that says `BEGIN TENDERMINT PRIVATE KEY` and ending with the line that says `END TENDERMINT PRIVATE KEY` into a txt file somewhere on your machine.
3. Import the key into `galaxyd` using the following command. When prompted for a password, use the same password used in step 1 to encrypt the keyfile.

```sh
galaxyd keys import <new_key_name> ./path/to/key.txt
```

4. Delete the keyfile from your machine.

#### Import via Ledger

To import a key stored on a ledger, the process will be exactly the same as adding a ledger key to the CLI normally.
You can connect a Ledger device with the Cosmos app open and then run:

```sh
galaxyd keys add <key_name> --ledger
```

and follow any prompts.

#### Get your Tendermint Validator Pubkey

You must get your validator's consensus pubkey as it will be necessary to include in the transaction to create your validator.

If you are using Tendermint's native `priv_validator.json` as your consensus key, you display your validator public key using the following command

```
galaxyd tendermint show-validator
```

The pubkey should be formatted with the bech32 prefix `glxvalconspub1`.

If you are using a custom signing mechanism such as `tmkms`, please refer to their relevant docs to retrieve your validator pubkey.

### Create GenTx

Now that you have you key imported, you are able to use it to create your gentx.

To create the genesis transaction, you will have to choose the following parameters for your validator:

- moniker
- commission-rate
- commission-max-rate
- commission-max-change-rate
- min-self-delegation (must be >1)
- website (optional)
- details (optional)
- identity (keybase key hash, this is used to get validator logos in block explorers. optional)
- pubkey (gotten in previous step)

Note that your gentx will be rejected if you use an amount greater than what you have as liquid from the airdrop. Recall only 20% of your fairdrop allocation is liquid at genesis. Also, note that Galaxy has a chain-mandated minimum commission rate of 5%.

If you would like to override the memo field, use the `--ip` and `--node-id` flags.

An example genesis command would thus look like:

```sh
galaxyd gentx <key_name> 1000000uglx \
  --chain-id="galaxy-1" \
  --moniker={YOUR MONIKER} \
  --website={YOUR WEBSITE} \
  --details={YOUR DETAILS} \
  --commission-rate="0.1" \
  --commission-max-rate="0.20" \
  --commission-max-change-rate="0.01" \
  --min-self-delegation="1" \
  --identity="5B5AB9D8FBBCEDC6" \
  --pubkey="galaxyvalconspub1zcjduepqnxl4ntf8wjn0275smfll4n4lg9cwcurz2qt6dkhrjzf94up8g4cspyyzn9"
```

It will show an output something similar to:

```sh
Genesis transaction written to "/Users/ubuntu/.galaxyd/config/gentx/gentx-eb3b1768d00e66ef83acb1eee59e1d3a35cf76fc.json"
```

### Submit Your GenTx

To submit your GenTx for inclusion in the chain, please upload it to the [github.com/galaxies-labs/networks](https://github.com/galaxies-labs/networks) repo until UTC on April 7 00:00.

To upload the your genesis file, please follow these steps:

1. Rename the gentx file just generated to gentx-{your-moniker}.json (please do not have any spaces or special characters in the file name)
2. Fork this repo by going to https://github.com/galaxies-labs/networks, clicking on fork, and choose your account (if multiple).
3. Clone your copy of the fork to your local machine

```sh
git clone https://github.com/<your_github_username>/networks
```

4. Copy the gentx to the networks repo (ensure that it is in the correct folder)

```sh
cp ~/.galaxyd/config/gentx/gentx-<your-moniker>.json networks/galaxy-1/gentxs/
```

5. Commit and push to your repo.

```sh
cd networks
git add galaxy-1/gentxs/*
git commit -m "Add <your validator moniker> gentx"
git push origin main
```

6. Create a pull request from your fork to master on this repo.
7. Let us know on Discord when you've completed this process!
8. Stay tuned for next steps which will be distributed after UTC March 30 00:00.

---

# Part 2

**Please join our [Galaxy Discord](https://discord.gg/GrEcUCqr) #validators channel and kindly wait for a next announcement.**

**The Chain Genesis Time is expected to be April 8, 2022 UTC.**

---

_Disclaimer: This content is provided for informational purposes only, and should not be relied upon as legal, business, investment, or tax advice. You should consult your own advisors as to those matters. References to any securities or digital assets are for illustrative purposes only and do not constitute an investment recommendation or offer to provide investment advisory services. Furthermore, this content is not directed at nor intended for use by any investors or prospective investors, and may not under any circumstances be relied upon when making investment decisions._

# Setting Up a Galaxy Validator (Testnet)

First of all, thank you for participating our journey.
We are at the beginning of creating our infinite, one and only universe. You are one of the pioneers of creating the galaxy universe.

If you wish to become a Genesis Validator after participating in the Testnet Validator within the period, you will receive a incentive.

## Using Testnet Faucets

Submit your validator information and get the $GLX on the Discord faucet channel.
<br>
[Galaxy Discord - (Faucet)](https://discord.gg/97GWGpDCzW)
(validator register admin : whiteT#0899)

### Claiming Testnet $GLX:

Testnet token faucets are accessible through Galaxy Discord server.

## OS/Minimum server spec

- System OS : Ubuntu 18.04
- 2 or more physical<sup>[1]</sup> CPU cores
- At least 256GB of SSD disk storage
- At least 8GB of memory
- At least 100mbps network bandwidth

### Install Go

Galaxy is built using Go and requires Go version 1.16+. In this example, we will be installing Go on the above Ubuntu 20.04:

```sh
# First remove any existing old Go installation
sudo rm -rf /usr/local/go

# Install Go
wget https://go.dev/dl/go1.16.6.linux-amd64.tar.gz

# Decompress.
# You may need sudo permission.
tar -C /usr/local -xzf go1.16.6.linux-amd64.tar.gz

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
# Should return go version go1.16.6 linux/amd64
```

### Get Galaxy Source Code

Use git to retrieve Galaxy source code from the [official repo](https://github.com/galaxies-labs/galaxy), and checkout the `v1.0.0` tag, which contains the testnet stable release.

```sh
git clone https://github.com/galaxies-labs/galaxy
cd galaxy
git checkout v1.0.0
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
version: launch-gentxs
commit: 941005fd26071b8018bb2107be1077b12e242c88
build_tags: netgo,ledger
go: go version go1.16.6 linux/amd64
```

If the software version does not match, then please check your `$PATH` to ensure the correct `galaxyd` is running.

### Save your Chain ID in galaxyd config

We recommend saving the testnet `chain-id` into your `galaxyd`'s client.toml. This will make it so you do not have to manually pass in the chain-id flag for every CLI command.

```sh
galaxyd config chain-id galaxias-1
```

### Initialize your Node

Now that your software is installed, you can initialize the directory for galaxyd.

```sh
galaxyd init <your_moniker>
```

This will create a new `.galaxyd` folder in your HOME directory.

### Download Pregenesis File

You can now download the "genesis" file for the test chain. This is a genesis file with the chain-id and airdrop balances.

```sh
cd $HOME/.galaxy/config/

curl https://media.githubusercontent.com/media/galaxies-labs/networks/main/galaxias-1/genesis.json > $HOME/.galaxy/config/genesis.json
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

The pubkey should be formatted with the bech32 prefix `galaxyvalconspub`.

If you are using a custom signing mechanism such as `tmkms`, please refer to their relevant docs to retrieve your validator pubkey.

### Create Validator

Now that you have you key imported, you are able to use it to create validator.

To create the validator transaction, you will have to choose the following parameters for your validator:

- from
- amount
- moniker
- commission-rate
- commission-max-rate
- commission-max-change-rate
- min-self-delegation (must be >1)
- website (optional)
- details (optional)
- identity (keybase key hash, this is used to get validator logos in block explorers. optional)
- pubkey (gotten in previous step)

Note that your tx will be rejected if you use an amount greater than what you have as liquid from the account balance. Also, note that Galaxy has a chain-mandated minimum commission rate of 5%.

If you would like to override the memo field, use the `--ip` and `--node-id` flags.

An example create validator command would thus look like:

```sh
galaxyd tx staking create-validator \
  --from={YOUR KEY NAME} \
  --amount={YOUR BOND AMOUNT} \
  --moniker={YOUR MONIKER} \
  --website={YOUR WEBSITE} \
  --details={YOUR DETAILS} \
  --commission-rate="0.1" \
  --commission-max-rate="0.20" \
  --commission-max-change-rate="0.01" \
  --min-self-delegation="1" \
  --identity=$(galaxyd tendermint show-node-id) \
  --pubkey=$(galaxyd tendermint show-validator)
```

# Galaxy Mainnet

## _Genesis Start Time: 28 April, 2022 09:00 UTC_

<br/>

### 👥 [peer list](https://github.com/galaxies-labs/networks/blob/main/peers.md)

### 📸 [snapshots](https://github.com/galaxies-labs/networks/blob/main/snapshots.md)

 <br/>

## OS/Minimum server spec

- System OS : Ubuntu 18.04
- 4 or more physical<sup>[1]</sup> CPU cores
- At least 500GB of SSD disk storage
- At least 16GB of memory
- At least 100mbps network bandwidth

### Install Go

Galaxy is built using Go and requires Go version 1.16+. In this example, we will be installing Go on the above Ubuntu 18.04:

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

Use git to retrieve Galaxy source code from the [official repo](https://github.com/galaxies-labs/galaxy), and checkout the `v1.0.0` tag, which contains the latest stable release.

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

### Galaxyd Version

```sh
galaxyd version --long

name: galaxy
server_name: galaxyd
version: v1.0.0
commit: 941005fd26071b8018bb2107be1077b12e242c88
build_tags: netgo,ledger
go: go version go1.16.6 linux/amd64

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

### Download Genesis File

```sh
cd $HOME/.galaxy/config/

curl https://media.githubusercontent.com/media/galaxies-labs/networks/main/galaxy-1/genesis.json > $HOME/.galaxy/config/genesis.json
```

### Genesis sha256

```sh
shasum -a 256 ~/.galaxy/config/genesis.json
> 2003cfaca53c3f9120a36957103fbbe6562d4f6c6c50a3e9502c49dbb8e2ba5b

sha256sum ~/.galaxy/config/genesis.json
> 2003cfaca53c3f9120a36957103fbbe6562d4f6c6c50a3e9502c49dbb8e2ba5b
```

### Start galaxyd

```sh
galaxyd start
```

NOTE: If you had run an older binary previously, you might get a panic on start. If so, please start fresh with:

```sh
galaxyd start  --x-crisis-skip-assert-invariants
```

---

_Disclaimer: This content is provided for informational purposes only, and should not be relied upon as legal, business, investment, or tax advice. You should consult your own advisors as to those matters. References to any securities or digital assets are for illustrative purposes only and do not constitute an investment recommendation or offer to provide investment advisory services. Furthermore, this content is not directed at nor intended for use by any investors or prospective investors, and may not under any circumstances be relied upon when making investment decisions._

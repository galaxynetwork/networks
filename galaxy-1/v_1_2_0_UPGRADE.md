# Galaxy v1.2.0 Upgrade

The upgrade is scheduled for a date announced on the Discord #validators announcement Channel.

## 1. Upgrade Go

> ### Be sure to upgrade Go to 1.18

Galaxy is built using Go and requires Go version 1.18 for v1.2.0 In this example, we will be installing Go on the above Ubuntu 18.04:

```sh
# First remove any existing old Go installation
sudo rm -rf /usr/local/go

# Remove any existing old go packages
sudo rm -rf /home/ubuntu/go

# Install Go
wget https://go.dev/dl/go1.18.linux-amd64.tar.gz

# Decompress.
# You may need sudo permission.
tar -C /usr/local -xzf go1.18.linux-amd64.tar.gz

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
# Should return go version go1.18 linux/amd64
```

## 2 Change the remote url. (repository name changed)

```sh
#Remove
- github.com/galaxies-labs/galaxy

#Add
+ github.com/galaxynetwork/galaxy
```

## 3 Upgrade Galaxy binary

```bash
# get the new version (run inside the repo)
git checkout main && git pull
git checkout v1.2.0
make build && make install

# check the version - should be v1.2.0
# galaxyd version --long will be commit e5c03e5931580fdacd9c14c1ace7c1bb2869489b
galaxyd version
```

## 4. Prepare Galaxy State Snapshot in Home Directory

**_ All nodes should be started with the same version and the same state. _**

Assume your home directory is /home/.galaxy

> ### This guide will never touch the `/home/.galaxy/config` directory. Please keep the config directory and the file requested to be backed up.

### 1. Backup up the `priv_validator_state.json` file in <home>/data directory (this is important)

### 2. Download Snapshot (Provided by 'Paranormal Brothers')

```bash
#1. Remove existing data directory
rm -rf ~/.galaxy/data

#2. Download snapshot to server
wget -O data.tar.gz http://95.216.72.28/data.tar.gz

#3. unpack
tar -xf data.tar.gz -C /home/.galaxy/
#Unpack 118Gb will take around 10 minutes, be patient and wait...

#4. delete data.tar.gz
rm -v data.tar.gz
```

### 3. Remove this file `priv_validator_state.json` in /home/.galaxy/data/ `coming from the Snapshot` (Preserve the files you backed up)

> (Be sure to check it out. Avoid double signing.)

### 4. Restore the backed up `priv_validator_state.json` file to /home/.galaxy/data/ directory

### 5. When all of the above preparations are complete, prepare to restart the Galaxy using the crontab local package or what you want.

> There is a risk of slashing if different nodes have different restart times.
>
> So use the package for convenience.

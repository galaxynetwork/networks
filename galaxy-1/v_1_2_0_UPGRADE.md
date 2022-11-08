# Galaxy v1.2.0 Upgrade

The upgrade is scheduled which for 11:00AM UTC on 11th November 2022\_.

### 1. Upgrade Go

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

### 2 Change the remote url. (repository name changed)

```sh
#Remove
- github.com/galaxies-labs/galaxy

#Add
+ github.com/galaxynetwork/galaxy
```

### 3 Upgrade Galaxy binary

```bash
# get the new version (run inside the repo)
git checkout main && git pull
git checkout v1.2.0
make build && make install

# check the version - should be v1.2.0
# galaxys version --long will be commit 09a33cc6cb5aaaa842621424ff7fce6c9637a212
galaxyd version
```

### 4. Restart Galaxy

```bash
galaxyd start
```

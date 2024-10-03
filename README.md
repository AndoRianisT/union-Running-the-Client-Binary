# Union Running the Client Binary

## Prerequisites

Before you begin, ensure that your system meets the following requirements:

- Operating System: Ubuntu 20.04 LTS or later, macOS Catalina or later, or Windows 10/11.
- Hardware: Minimum 4 GB RAM, 2 CPU cores, and 20 GB of free disk space.
- Software Dependencies:

## Installing Git

**Ubuntu:**

```
sudo apt update
sudo apt install git -y
```

**macOS (using Homebrew):**

```
brew update
brew install git
```
**Windows:** Download and install Git from the [official website](https://git-scm.com/download/win).

## Installing Go

**Download and Install Go:**

Visit the [Go Downloads page](https://golang.org/dl/) and follow the installation instructions for your operating system.

**Verify Installation:**

```
go version
```
You should see output similar to:

```
go version go1.18 linux/amd64
```

## Cloning the Repository

Begin by cloning the Union.Build repository containing the *uniond* source code.

```
git clone https://github.com/UnionBuild/uniond.git
cd uniond
```
> Note: Replace the repository URL with the correct one if different.

## Installing Dependencies

*uniond* may have several dependencies that need to be installed before building the daemon.

## Using Go Modules

Ensure that Go modules are enabled (they are by default in Go 1.13 and later).

```
export GO111MODULE=on
```
## Installing Required Go Packages
```
go mod download
```
This command downloads all the necessary Go packages specified in the go.mod file.

## Building *uniond*

Once the dependencies are installed, you can build the *uniond* binary.

```
go build -o uniond main.go
```
> Explanation:
- go build compiles the packages and dependencies.
- -o uniond specifies the output binary name.
- main.go is the entry point of the application.

## Verifying the Build

After building, verify that the uniond executable is present:

```
ls -la uniond
```
You should see the uniond file with execute permissions.

Configuring uniond
Before running uniond, you need to configure it to connect to the testnet.

## Creating a Configuration File

Create a configuration directory and file:

```
mkdir -p ~/.uniond
nano ~/.uniond/config.toml
```
## Sample config.toml

Insert the following configuration into config.toml. Modify the parameters as needed.

```
[server]
listen_addr = "0.0.0.0:26657"
grpc_listen_addr = "0.0.0.0:9090"

[client]
node = "tcp://localhost:26657"

[mempool]
size = 1000
```
> Note: Adjust the listen_addr and grpc_listen_addr if you have specific network configurations.

## Setting Environment Variables (Optional)

You can set environment variables to override configuration settings.

```
export UNIOND_HOME=~/.uniond
export UNIOND_CONFIG=~/.uniond/config.toml
```
## Running uniond

With the configuration in place, you can now start the uniond daemon.

```
./uniond start
```
## Running uniond as a Background Service

For continuous operation, it's advisable to run uniond as a background service using systemd (Linux) or other init systems.

**Example: Using systemd on Linux**

1. Create a systemd Service File:

```
sudo nano /etc/systemd/system/uniond.service
```
2. Insert the Following Configuration:

```
[Unit]
Description=Union.Build Daemon
After=network.target

[Service]
User=your_username
ExecStart=/path/to/uniond start
Restart=on-failure
Environment=UNIOND_HOME=/home/your_username/.uniond
Environment=UNIOND_CONFIG=/home/your_username/.uniond/config.toml

[Install]
WantedBy=multi-user.target
```
> Replace /path/to/uniond with the actual path to the uniond binary and your_username with your actual username.

3. Reload systemd and Start the Service:

```
sudo systemctl daemon-reload
sudo systemctl start uniond
sudo systemctl enable uniond
```
4. Check Service Status:

```
sudo systemctl status uniond
```
## Connecting to the Testnet

To participate in the Union.Build testnet, you need to connect your uniond node to the testnet peers.

## Adding Peers

Edit the config.toml to include testnet peers.

```
nano ~/.uniond/config.toml
```
Add the following under the [p2p] section:

```
[p2p]
seeds = "peer1.testnet.union.build:26656,peer2.testnet.union.build:26656"
persistent_peers = "peer3.testnet.union.build:26656,peer4.testnet.union.build:26656"
```
> Note: Replace the seed and peer addresses with the actual testnet peer addresses provided by Union.Build.

## Syncing with the Network

After adding peers, restart uniond to apply the changes.

```
./uniond restart
```

Monitor the synchronization process:

```
./uniond status
```
Wait until your node is fully synced with the testnet.

## Verifying the Setup

Ensure that your uniond node is running correctly and connected to the testnet.

## Checking Node Status

Use the following command to check the status of your node:

```
./uniond status
```

Sample output:

```
{
  "node_info": {
    "id": "abcdef123456...",
    "listen_addr": "0.0.0.0:26657",
    ...
  },
  "sync_info": {
    "latest_block_hash": "12345...",
    "latest_app_hash": "67890...",
    "latest_block_height": "1000",
    "latest_block_time": "2024-10-03T12:34:56Z",
    ...
  },
  "validator_info": {
    ...
  }
}
```
## Testing RPC Endpoints

You can interact with your node using RPC endpoints.

**Example: Fetch Latest Block:**

```
curl http://localhost:26657/block
```

**Sample Response:**

```
{
  "jsonrpc": "2.0",
  "id": "",
  "result": {
    "block_id": { ... },
    "block": { ... }
  }
}
```

## Troubleshooting

If you encounter issues during setup, consider the following troubleshooting steps.

**Common Issues**

1. Port Conflicts:

- Ensure that the ports specified in config.toml (e.g., 26657, 9090) are not in use by other applications.
- Use netstat or lsof to check for port usage.
```
sudo netstat -tulpn | grep LISTEN
```
2. Firewall Restrictions:

-Ensure that your firewall allows incoming and outgoing traffic on the necessary ports.

- Ubuntu (using UFW):

```
sudo ufw allow 26657/tcp
sudo ufw allow 9090/tcp
```
3. Synchronization Issues:

- Verify that your node is connected to peers.

- Check peer connectivity:

```
./uniond tendermint show-peers
```
- If peers are not connecting, verify the peer addresses in config.toml.

4. Insufficient Resources:

- Ensure your system meets the minimum hardware requirements.

- Monitor system resources:

```
top
```
## Logs Inspection

Check the uniond logs for detailed error messages.

**If running as a service (systemd):**

```
sudo journalctl -u uniond -f
```

**If running manually:**

Logs are typically output to the terminal. Review the messages for errors.

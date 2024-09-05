## Go wukong

Based on the go-ethereum v1.10.17 source code.

### Building the source

Building `geth` requires both a Go (version 1.14 or later) and a C compiler. You can install
them using your favourite package manager. Once the dependencies are installed, run

```shell
make geth
```

or, to build the full suite of utilities:

```shell
make all
```

### How to deploy node and export RPC interface

 * Create non privileged user

```
useradd -m wukong
```

 * Create directories to store blockchain and node software

```
mkdir /home/wukong/data/geth
mkdir /home/wukong/opt/geth
```

 * Download node software

```
curl https://github.com/wukongspace/go-wukong/releases/download/v2.0.0/geth-linux-amd64 -o /home/wukong/opt/geth/geth-linux-amd64
chmod +x home/wukong/opt/geth/geth-linux-amd64
chown -R wukong.wukong /home/wukong
```

 * Create systemd unit file

```
cat > /etc/systemd/system/wukong-node.service << EOF
[Unit]
Description=wukong node
After=network.target

[Service]
User=wukong
Group=wukong
Restart=on-failure
RestartSec=10
ExecStart=/home/wukong/opt/geth/geth-linux-amd64 --datadir /home/wukong/data/geth \
    --port 38000 \
    --http \
    --http.addr 127.0.0.1 \
    --http.port 8545 \
    --http.vhosts * \
    --http.api "eth,net,web3" \
    --ws \
    --ws.addr 127.0.0.1 \
    --ws.port 8546 \
    --ws.origins * \
    --ws.api "eth,net,web3" \
    --cache 512 \
    --maxpeers 128 \
    --gpo.maxprice 1000000000 \
    --rpc.gascap 21000000000

[Install]
WantedBy=default.target
EOF
```
 * Enable and start service

```
systemctl daemon-reload
systemctl enable wukong-node
systemctl start wukong-node
```

 * Check status and logs

```
systemctl status wukong-node
```

```
journalctl -u octa-node
```

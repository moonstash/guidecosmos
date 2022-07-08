![](https://i.yapx.ru/RTuEU.jpg)


In this guide, we have made setting up a node as easy as possible

    curl -s https://raw.githubusercontent.com/StakeTake/guidecosmos/main/stride/STRIDE/stride > stride.sh && chmod +x stride.sh && ./stride.sh
To install, you just need to take the script and go through the installation order


#START WITH STATE-SYNC
stop stride node
```
sudo systemctl stop strided
```
clear data
```
strided tendermint unsafe-reset-all --home $HOME/.stride
```
add peer
```
peers="c73d5d83ae121dd9f2ebbfd381724c844a5e5106@stride-node1.poolparty.stridenet.co:26656"; \
sed -i.bak -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.stride/config/config.toml
```
Add variables
```
SNAP_RPC=https://stride-node3.poolparty.stridenet.co:445
LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)
echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH
```
Now enter all the datat to `config.toml`
```
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.stride/config/config.toml
```
restart ur node
```
sudo systemctl restart strided && journalctl -u strided -f -o cat
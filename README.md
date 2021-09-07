# testnet-ki-rizon-Ibc

In this tutorial, I will briefly describe the process of creating a relayer between two cosmos networks using an example
Rizon and Kichain, which are on different servers. 

## 1. Сheck whether it is possible to conduct cross transactions at the Rizon node:
```
rizond q ibc-transfer params
```

in response we should receive:

> receive_enabled: true send_enabled: true

## 2. installing Relayer and Initializing
```
git clone https://github.com/cosmos/relayer.git
cd relayer
make install
rly version
```
output:
> version: 1.0.0-rc1–152-g112205b

**Initializing the repeater:**
```
rly config init
```

## 3. Create chain configurations
```
mkdir rly_config
cd rly_config
```
```
nano kichain-t-4.json
```
insert: 
> {  "chain-id": "kichain-t-4",   "rpc-addr": "https://rpc-challenge.blockchain.ki:443",    "account-prefix": "tki",   "gas-adjustment": 1.5,   "gas-prices": "0.025utki",   "trusting-period": "48h" }  

```
nano groot-011.json
```
insert: 
> {  "chain-id": "groot-011",  "rpc-addr": "http://rizon-node-ip:26657",   "account-prefix": "rizon",  "gas-adjustment": 1.5,  "gas-prices": "0.025uatolo",  "trusting-period": "48h" }

(instead of rizon-node-ip, insert your server ip)

## 4. Parse settings into config of the relay:
```
rly chains add -f groot-011.json
rly chains add -f kichain-t-4.json
cd
```
## 5. Create new wallets or restore the ones you already have:

create:
```
rly keys add groot-011 name of your wallet 
rly keys add kichain-t-4 name of your wallet
```
or restore:
```
rly keys restore groot-011 name of your wallet "mnemonic from wallet"
rly keys restore kichain-t-4 name of your wallet "mnemonic from wallet" 
```
## 6. Add the newly created keys to the config of the relay:
```
rly chains edit groot-011 key name of your wallet
rly chains edit kichain-t-4 key name of your wallet
```
## 7. change the timeout of waiting for confirmation to 30s by changing the standard 10s:
```
nano ~/.relayer/config/config.yaml
```
> timeout: 30s  

## 8. Fund wallets on both networks
faucet Rizon: http://faucet.rizon.world/

check balances:
```
rly q balance groot-011
rly q balance kichain-t-4
```
## 9. Generate a channel between the networks:
```
rly paths generate groot-011 kichain-t-4 transfer  -- port=transfer
```
you should see the output: 
> Generated path(transfer), run 'rly paths show transfer  -- yaml' to see details

## 10. Check if everything is ready
```
rly paths list -d
```

If you see checkmarks – everything is good: 
> 0: transfer             -> chns(✔) clnts(✔) conn(✔) chan(✔) (kichain-t-4:transfer<>groot-011:transfer)

## 10. Transfer funds between our test wallets: 
```
rly tx transfer groot-011 kichain-t-4 1000uatolo $(rly chains address kichain-t-4)
```
>I[2021-09-07|00:47:39.440] ✔ [groot-011]@{421129} - msg(0:transfer)

**My Hash:** 
029E1E918E8D56FF06CB16805033144F7529E5D5BBC24DB2DCCCB2854812C705
38640D409B7B72823ACF6CC130449D587E90BE4A36C868D1EDB06684E1C05635
92DA9A14B5E307375C93B4D3916B2502F5493D3CEC3ACA7CC557709751CE2452
FB617AEA89BA7A7F7428EF3293EBBE7717F4E0551ACF87F58A5A119EB66D53D0
9386F84E1F512C11D61096961EF42DF71D22F0C2EE6C624C0DFCC416A00A768F

```
rly tx transfer kichain-t-4 groot-011  1000utki $(rly chains address groot-011)
```
**My Hash:** 
6F9C3B2F01DBFCA2D03EBECCD36F62F420B750F0434D0F58152080E3BA0EE1FA
3ADC343BD446885A2E7D190622B985652054C28E2F17583F68E63DB88A2D8F59
7D190896352EC92FE5675F3668F4037C76E02CC2BAD91227DE67A4B96C8CF55B
A577E63BF2C9D6A62BDB2475724A3C86C167207260908E96AF1E96AC28EE1E8E
9880F805F4A797812F3F707F68D81C543A3D7A3D6A4D27FE7F1A74FA0E82112D





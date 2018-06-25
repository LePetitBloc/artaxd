# [Artax core Wallet Daemon (`artaxd`)](https://github.com/LePetitBloc/artaxd)

[![Docker Stars][docker-svg]][docker-url]

Artax core Wallet Daemon for headless wallets and **masternodes**. 

> For a list of all available wallet images see https://lepetitbloc.github.io/wallets/

## Issues
- Parameters value issues should be reported at:
https://github.com/LePetitBloc/wallets

- All other issues should be reported at:
https://github.com/LePetitBloc/wallets-builder

## Usage
The container can either be used as a classic headless wallet or a **masternode**, only the *command* arguments will differ.

### Headless wallet
1. Run a **wallet** container and specify at least the `rpcuser` and `rpcpassword` to interact with the **Artax** daemon:
```
docker run --name artax-wallet --restart always -d lepetitbloc/artaxd:1.2 -rpcuser=artax-wallet -rpcpassword=q4qwsaha
```
> We recommend to mount a volume for easier access to the *data* and the *configuration* files.
> You should also create a configuration for your RPC credentials (see `./wallet/.artaxcore/artax.conf`) to avoid retyping them when using the internal `artax-cli`.
> ```
> docker run --name artax-wallet --restart always -d -v ${PWD}/wallet/:/home/artax/ lepetitbloc/artaxd:1.2
> ```

> :warning: Ensure that a `data` directory **exists** and is **writable** in the mounted host directory.

2. Once your wallet is running, you can print your main address:
```
docker exec artax-wallet artax-cli getaccountaddress ""
```
> GK92mbjS9bCAUuU7DEyyuK9US1qLqkoyce

2. **Encrypt your wallet:**
> You can use `pwgen` first to generate your *passphrase*:
> ```
> pwgen 32 1
> ```
> quohd4kaw9guvi8ie7phaighawaiLoo6
```
docker exec artax-wallet artax-cli encryptwallet quohd4kaw9guvi8ie7phaighawaiLoo6
```
> Wallet encrypted; Artax Core server stopping, restart to run with encrypted wallet. The keypool has been flushed, you need to make a new backup.

:bangbang: Don't forget to **backup** your *passphrase*.

### Masternode

#### Prerequisites
1. You must have received *2000 XAX* on your **wallet** in a **single transaction**, and **must** have waited for, at least, **1 confirmation**.
> **Note1:** If the *2000 XAX* came from multiple transactions, you can send them back to yourself.

> **Note2:** Beware of the transaction cost, you should own *2001 XAX* as a safety measure.

2. Only then you may find the corresponding transaction `hash` and `index` :
```
docker exec artax-wallet artax-cli masternode outputs
```
>```
>{
>  "8e835a7d867d335434925c32f38902268e131e99a5821557d3e77f8ca3829fd8" : "0"
>}
>```

3. Then generate a **masternode** private key:
```
masternode genkey
```
>```
>7ev3RXQXYfztreEz8wmPKgJUpNiqkAkkdxt24C3ZKtg5qEVfou9
>```

4. And finally creates the `./wallet/.artaxcore//masternode.conf` file, and fill in following this template:
> `mn01 masternode:21529 YouMasterNodePrivateKey TransactionHash 0 YourWalletAddress:100`
```
touch ./wallet/.artaxcore//masternode.conf
```

#### Setup
1. As a classic wallet, create a `./masternode/.artaxcore/artax.conf` configuration file
```
rpcuser=artax-mn01
rpcpassword=q4qwsaha
masternode=1
masternodeprivkey=7ev3RXQXYfztreEz8wmPKgJUpNiqkAkkdxt24C3ZKtg5qEVfou9
externalip=YOUR.EXTERNAL.IP:21526
addnode=45.32.233.39:21527
addnode=144.202.74.29:21527
addnode=199.247.0.99:21527
addnode=35.185.28.120:21527
addnode=45.32.22.238:21527
addnode=140.82.60.29:21527
addnode=108.61.252.205:21527
addnode=45.63.82.193:21527
addnode=46.101.48.90:21527
addnode=132.148.139.228:21527
addnode=121.63.252.178:21512
addnode=199.247.6.97:21527
addnode=173.208.188.35:21527
addnode=52.47.168.149:21527
addnode=144.202.36.100:21544
addnode=207.148.0.96:21527
addnode=54.37.72.144:21532
addnode=104.207.143.189:21527
addnode=165.227.87.76:21529
addnode=207.154.194.110:21527
```

2. Run a container as a **masternode**:
```
docker run --name artax-masternode --restart always -d -p 21526:21526 -p 21527:21527 -v ${PWD}/masternode/:/home/artax/ lepetitbloc/artaxd:1.2 -masternode=1
```

3. Check the the number of `blocks` until the chain is sync:
```
docker exec artax-masternode artax-cli getinfo
```

4. Once the chain synced you can start the **masternode** from your **wallet**:
```
docker exec artax-wallet artax-cli artax-masternode start-all
```
> You might need to unlock your **wallet** first:
> ```
>  docker exec artax-wallet artax-cli walletpassphrase quohd4kaw9guvi8ie7phaighawaiLoo6 60
> ```
> :warning: Mind the **space** before the command above, that's not a typo, it’s meant to avoid storing your passphrase in *history*.

5. Then check the **masternode** status with your initial **transaction hash**:
```
docker exec artax-wallet artax-cli masternodelist | grep 6d94f70499c3f7ba2c59acaa5c04e54ef123d0e460bb07c55ace6464deaf3c85
```
> `"6d94f70499c3f7ba2c59acaa5c04e54ef123d0e460bb07c55ace6464deaf3c85-1": "ENABLED",`

## docker-compose
You could setup **both** at the same time using `docker-compose`.
Check the provided `docker-compose.yml` as an example and tweak it to your needs!
```
docker-compose up --build
```


## Resources
- Website https://www.artaxcoin.org/
- Github https://github.com/artaxcommittee/Artax
- Bitcointalk announcement https://bitcointalk.org/index.php?topic=3267175.0
- Block explorer https://artax.blockxplorer.info/

## Parent project
- https://github.com/LePetitBloc/wallets
- https://github.com/LePetitBloc/wallets-builder

## Parent image
- Berkeley DB v4.8.30.NC
`FROM lepetitbloc/bdb:4.8.30.NC`
> https://github.com/LePetitBloc/bdb/tree/4.8.30.NC
> https://hub.docker.com/r/lepetitbloc/bdb/

## Licence
MIT

[docker-url]: https://hub.docker.com/r/lepetitbloc/artaxd/
[docker-svg]: https://img.shields.io/docker/stars/lepetitbloc/artaxd.svg

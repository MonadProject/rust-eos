origin readme see https://github.com/MonadProject/rust-eos/blob/master/README-EN.md

### 一、下载docker

```
https://www.docker.com/community-edition

```

### 二、建立工作目录

```
mkdir contracts

cd contracts
```

### 三、获取镜像

```
docker pull eosio/eos:v1.4.2
```

### 四、启动节点和钱包

```
docker run --name eosio \
  --publish 7777:7777 \
  --publish 127.0.0.1:5555:5555 \
  --volume /Users/xiaohuo/Code/rust-eos/contracts:/Users/xiaohuo/Code/rust-eos/contracts \
  --detach \
  eosio/eos:v1.4.2 \
  /bin/bash -c \
  "keosd --http-server-address=0.0.0.0:5555 & exec nodeos -e -p eosio --plugin eosio::producer_plugin --plugin eosio::chain_api_plugin --plugin eosio::history_plugin --plugin eosio::history_api_plugin --plugin eosio::http_plugin -d /mnt/dev/data --config-dir /mnt/dev/config --http-server-address=0.0.0.0:7777 --access-control-allow-origin=* --contracts-console --http-validate-host=false --filter-on='*'"
```

### 五、检查

#### 检查节点

```
docker logs --tail 10 eosio
```

#### 检查钱包

```
docker exec -it eosio bash

cleos --wallet-url http://127.0.0.1:5555 wallet list

exit

```

#### 检查端点

```
curl http://localhost:7777/v1/chain/get_info
```

#### 六、创建 cleos 别名 

```
alias cleos='docker exec -it eosio /opt/eosio/bin/cleos --url http://127.0.0.1:7777 --wallet-url http://127.0.0.1:5555'
```

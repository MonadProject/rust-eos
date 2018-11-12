origin readme see https://github.com/MonadProject/rust-eos/blob/master/README-EN.md

### 准备工作

<h5> 一、下载docker

```
https://www.docker.com/community-edition

```

<h5> 二、建立工作目录

```
mkdir contracts

cd contracts
```

<h5> 三、获取镜像

```
docker pull eosio/eos:v1.4.2
```

<h5> 四、启动节点和钱包

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

<h5> 五、检查

<h6> 检查节点

```
docker logs --tail 10 eosio
```

<h6> 检查钱包

```
docker exec -it eosio bash

cleos --wallet-url http://127.0.0.1:5555 wallet list

exit

```

<h6> 检查端点

```
curl http://localhost:7777/v1/chain/get_info
```


<h5> 六、创建 cleos 别名 

```
alias cleos='docker exec -it eosio /opt/eosio/bin/cleos --url http://127.0.0.1:7777 --wallet-url http://127.0.0.1:5555'
```



### 创建开发者钱包

<h5> 一、创建钱包

```
cleos wallet create --to-console
```

保存好返回的密码 PW5JhivXoP2ZBSqC68QrFzjN44WPagfQvHJs2dcZ2C3797aoStHUC（仅开发模式下使用，生产模式另见文档）

<h5> 二、打开钱包

```
打开钱包
cleos wallet open

列举可用钱包列表
cleos wallet list

解锁钱包
cleos wallet unlock
```

<h5> 三、解锁钱包

```
解锁钱包
cleos wallet unlock
```

<h5> 四、导入开发者密钥

```
cleos wallet import
```

systen user: eosio 5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3 请勿在生产环境使用

EOS 的账户创建需要关联钱包公钥，并且同一个钱包公钥可以创建多个账户，但是各个账户是独立的


### 创建测试账户

```
cleos create account eosio bob EOS6kZmiNLCGQX4a88r2zyfcyRjukJH5TwwpSNbgD3kkFsUcoXvgJ 
cleos create account eosio alice EOS6kZmiNLCGQX4a88r2zyfcyRjukJH5TwwpSNbgD3kkFsUcoXvgJ
```

单机测试情况下使用账户 eosio 来创建账户，公网环境下需要一定的费用

### 生成、优化 WASM 文件

```
cargo build --release --target=wasm32-unknown-unknown
wasm-gc target/wasm32-unknown-unknown/release/hello.wasm hello_gc.wasm
wasm-opt hello_gc.wasm --output hello_gc_opt.wasm -Oz
```

### 部署abi 和 wasm

```
cleos set abi hello /mnt/dev/project/hello.abi.json
cleos set code hello /mnt/dev/project/hello_gc_opt.wasm
```

### 调用智能合约

```
cleos push action hello hi '["world"]' -p 'hello@active'
```
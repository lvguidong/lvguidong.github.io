---
layout: post
title: 使用truffle构建第一个DAPP
tags: 
- eth
- truffle
- DApp
date: 2022-01-17 08:12 +0800
---

​	使用truffle快速搭建一个DApp

​	相关[链接](https://trufflesuite.com/boxes/webpack/index.html)

# 环境准备

  1. 安装 truffle

 ```
 npm install -g truffle
 ```

相关版本如下:

```
dong@DESKTOP-KS722GA:/mnt/g/dong/coin/eth/metabox$ truffle version
Truffle v5.4.28 (core: 5.4.28)
Solidity v0.5.16 (solc-js)
Node v14.18.3
Web3.js v1.5.3
```



# 下载

在`boxes`下载webpack:

```
dong@DESKTOP-KS722GA:/mnt/g/dong/coin/eth/metabox$ truffle unbox metabox

Starting unbox...
=================

✔ Preparing to download box
✖ Downloading
.........
```

目录结构如下, build是编译后的目录。

<img src="https://github.com/lvguidong/lvguidong.github.io/blob/main/_posts/images/truffle-webpack.png?raw=true" alt="block-min" style="zoom:80%;" />

# 打开开发控制台

```
truffle develop
```

```
dong@DESKTOP-KS722GA:/mnt/g/dong/coin/eth/metabox$ truffle develop
Truffle Develop started at http://127.0.0.1:8545/

Accounts:
(0) 0x5c4e20fcdc9f1be5d5c8046d66f5168ddc7d9dec
(1) 0xc179c124b60fa060bb4d3a3e9005c193a97dbffb
(2) 0xd2cbb435a91c7adaa3b0c6ae36c2db605d8045a7
(3) 0x32dbb6434cdcbb0b83b772c5a203674c143974d5
(4) 0xa9e382bdf0b47e8b8999bd52ea2602ec613fe0a1
(5) 0x7ec2fe6e197b1286f52684791831c0e54401c051
(6) 0xa82d1fe2cddf6097dfb18ca8d42a787b345c03b3
(7) 0x5399f9bbec9b22b3ecef4a99f951266ef0239ec3
(8) 0x502d2d8106962a589ba31cdc3734025196d25c01
(9) 0xc80be6719b5ff51a9b23a2e19afb9139d7e48ec4

Private Keys:
(0) 06edb6b18b3add6892660a020db19e2e819b95ad9100c30c829a95214fe0950c
(1) e328b1cc3650bf7c1ea9883783c2d76e5fd4407b12e831afa9917aabfa568c11
(2) 9bc32c96c551715e9cf0afcd9f149e7f785631611c469705c5dbf125d16d1d1f
(3) c91870d6dca085a8dc60ef5a1eb863231fd6cc1b13051779de2a7b79093ea741
(4) 8203a15b4666dd7ec3666aa8ed689bcf837ed61ddda29c2229776c1dc8fc4675
(5) 45a6402fac56065c473085fd0364bbcb84a98e777519f1d43b0082472dc6900d
(6) 07d73889082a6a6014e4570066dad0fa2eb00549da29b630e21046632d67bfde
(7) 9bf1a1112bdc81f080dd803b7ed747c1af32b1135db91a3c23e717c35c5be466
truffle(develop)>
(9) 02decda14432bd38882ae5ddc906361dd94c0960328606b4aaab0d1b31c66cdb

Mnemonic: appear property feed parrot miracle antique yard victory plastic nest federal bachelor

⚠️  Important ⚠️  : This mnemonic was created for you by Truffle. It is not secure.
Ensure you do not use it on production blockchains, or else you risk losing funds.

truffle(develop)>
```



# 编译

```
compile
```

```
truffle(develop)> compile
===========================
> Compiling ./contracts/ConvertLib.sol
> Compiling ./contracts/MetaCoin.sol
> Compiling ./contracts/Migrations.sol
> Artifacts written to /mnt/g/dong/coin/eth/metabox/build/contracts
> Compiled successfully using:
truffle(develop)>
```



# 部署

```
migrate
```

```
truffle(develop)> migrate

Compiling your contracts...
===========================
> Everything is up to date, there is nothing to compile.



Starting migrations...
======================
> Network name:    'develop'
> Network id:      5777
> Block gas limit: 6721975 (0x6691b7)


1_initial_migration.js
======================

   Deploying 'Migrations'
   ----------------------
   > transaction hash:    0x41398e259c19fbe09ad5c2912c9004a4e7c84d2e8acb5327786b4a97fd3cc162
   > Blocks: 0            Seconds: 0
   > contract address:    0x57ae61E7095F27C1E26A951e237F7A9F1547f180
   > block number:        1
   > block timestamp:     1642429378
   > account:             0x5C4e20fcDc9f1Be5D5c8046d66F5168dDc7d9deC
   > balance:             99.99967165
   > gas used:            164175 (0x2814f)
   > gas price:           2 gwei
   > value sent:          0 ETH
   > total cost:          0.00032835 ETH


   > Saving migration to chain.
   > Saving artifacts
   -------------------------------------
   > Total cost:          0.00032835 ETH


2_deploy_contracts.js
=====================

   Deploying 'ConvertLib'
   ----------------------
   > transaction hash:    0xc57c7a993bbdd1695a17123e541873e6af016585234d8cc5b92575b9f095bf03
   > Blocks: 0            Seconds: 0
   > contract address:    0x7B9804FDd13929fC83F58150307846c64cd03e9a
   > block number:        3
   > block timestamp:     1642429378
   > account:             0x5C4e20fcDc9f1Be5D5c8046d66F5168dDc7d9deC
   > balance:             99.999396028
   > gas used:            95470 (0x174ee)
   > gas price:           2 gwei
   > value sent:          0 ETH
   > total cost:          0.00019094 ETH


   Linking
   -------
   * Contract: MetaCoin <--> Library: ConvertLib (at address: 0x7B9804FDd13929fC83F58150307846c64cd03e9a)

   Deploying 'MetaCoin'
   --------------------
   > transaction hash:    0x6cbbb3cc546a19af009380da6c156f6544482b3b45298a736fc0c13f552e7f27
   > Blocks: 0            Seconds: 0
   > contract address:    0x7EA0e5f0fA5381e1DEF85bc7cC264862cFA10C99
   > block number:        4
   > block timestamp:     1642429379
   > account:             0x5C4e20fcDc9f1Be5D5c8046d66F5168dDc7d9deC
   > balance:             99.998822898
   > gas used:            286565 (0x45f65)
   > gas price:           2 gwei
   > value sent:          0 ETH
   > total cost:          0.00057313 ETH


   > Saving migration to chain.
   > Saving artifacts
   -------------------------------------
   > Total cost:          0.00076407 ETH


Summary
=======
> Total deployments:   3
> Final cost:          0.00109242 ETH


- Blocks: 0            Seconds: 0
- Saving migration to chain.
- Blocks: 0            Seconds: 0
- Blocks: 0            Seconds: 0
- Saving migration to chain.

truffle(develop)>
```

现在合约已经部署到搭建的链上了，合约地址是： `0x7B9804FDd13929fC83F58150307846c64cd03e9a`, 



# 运行DApp

​	在app目录下运行：

```
dong@DESKTOP-KS722GA:/mnt/g/dong/coin/eth/metabox$ cd app/
dong@DESKTOP-KS722GA:/mnt/g/dong/coin/eth/metabox/app$ npm run dev

> app@1.0.0 dev /mnt/g/dong/coin/eth/metabox/app
> webpack-dev-server

ℹ ｢wds｣: Project is running at http://localhost:8080/
ℹ ｢wds｣: webpack output is served from /
ℹ ｢wds｣: Content not from webpack is served from /mnt/g/dong/coin/eth/metabox/app/dist
ℹ ｢wdm｣: Hash: 401e5e7851416c79906a
Version: webpack 4.41.2
Time: 2036ms
Built at: 01/17/2022 10:26:43 PM
     Asset       Size  Chunks             Chunk Names
index.html  879 bytes          [emitted]
  index.js   2.43 MiB    main  [emitted]  main
Entrypoint main = index.js
[0] multi (webpack)-dev-server/client?http://localhost:8080 ./src/index.js 40 bytes {main} [built]
[../build/contracts/MetaCoin.json] 105 KiB {main} [built]
[./node_modules/ansi-html/index.js] 4.16 KiB {main} [built]
[./node_modules/html-entities/index.js] 231 bytes {main} [built]
[./node_modules/web3/src/index.js] 2.01 KiB {main} [built]
[./node_modules/webpack-dev-server/client/index.js?http://localhost:8080] (webpack)-dev-server/client?http://localhost:8080 4.29 KiB {main} [built]
[./node_modules/webpack-dev-server/client/overlay.js] (webpack)-dev-server/client/overlay.js 3.51 KiB {main} [built]
[./node_modules/webpack-dev-server/client/socket.js] (webpack)-dev-server/client/socket.js 1.53 KiB {main} [built]
[./node_modules/webpack-dev-server/client/utils/createSocketUrl.js] (webpack)-dev-server/client/utils/createSocketUrl.js 2.89 KiB {main} [built]
[./node_modules/webpack-dev-server/client/utils/log.js] (webpack)-dev-server/client/utils/log.js 964 bytes {main} [built]
[./node_modules/webpack-dev-server/client/utils/reloadApp.js] (webpack)-dev-server/client/utils/reloadApp.js 1.59 KiB {main} [built]
[./node_modules/webpack-dev-server/client/utils/sendMessage.js] (webpack)-dev-server/client/utils/sendMessage.js 402 bytes {main} [built]
[./node_modules/webpack-dev-server/node_modules/strip-ansi/index.js] (webpack)-dev-server/node_modules/strip-ansi/index.js 161 bytes {main} [built]
[./node_modules/webpack/hot sync ^\.\/log$] (webpack)/hot sync nonrecursive ^\.\/log$ 170 bytes {main} [built]
[./src/index.js] 2.08 KiB {main} [built]
    + 350 hidden modules
ℹ ｢wdm｣: Compiled successfully.
```

如上所示，打开 http://localhost:8080/ 

<img src="https://github.com/lvguidong/lvguidong.github.io/blob/main/_posts/images/truffle-dapp1.png?raw=true" alt="block-min" style="zoom:80%;" />

# 发送一笔交易

<img src="https://github.com/lvguidong/lvguidong.github.io/blob/main/_posts/images/truffle-dapp2.png?raw=true" alt="block-min" style="zoom:80%;" />

交易成功后金额减少：

<img src="https://github.com/lvguidong/lvguidong.github.io/blob/main/_posts/images/truffle-dapp3.png?raw=true" />



一个简单的DApp就部署成功运行了。
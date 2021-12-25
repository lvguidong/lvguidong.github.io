---
layout: post
title: BTC开发者指南翻译-区块链(Block Chain)
tags: 
- 区块链
- BlockChain
- BTC
date: 2021-12-25 16:00 +0800
---
翻译原文在 [**这里**](https://developer.bitcoin.org/devguide/block_chain.html)

# 区块链(Block Chain)
区块链提供了比特币的公共账本(ledger)，一个有序的(ordered)、带有时间戳的(timestamped)交易记录。该系统用于防止双重支付(double spending)和修改以前的交易记录

# 介绍(Introdution)
比特币网络中的每个完整节点独立存储一个区块链，其中仅包含由该节点验证的区块。当多个节点在其区块链中都拥有相同的区块时，则认为它们已达成共识(consensus)。这些节点为维持共识而遵循的验证规则称为共识规则(consensus rules)。本节描述了 Bitcoin Core 使用的许多共识规则。
<img src="https://github.com/lvguidong/lvguidong.github.io/blob/main/_posts/images/btc_tr_blockchain1.svg?raw=true?raw=true" alt="block-min" style="zoom:80%;" />

上图显示了区块链的简化版本。一个或多个新交易的区块被收集到区块的交易数据部分。每笔交易的副本都会被哈希(hashed)，然后将哈希配对、哈希、再次配对、再散列，直到剩下一个散列，即默克尔树(merkle tree)的默克尔根(merkle root)。
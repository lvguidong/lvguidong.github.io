---
layout: post
title: BTC开发者指南翻译-交易(Transaction)
tags: 
- 区块链
- BlockChain
- BTC
date: 2021-12-25 16:02 +0800
---
​		翻译原文在 [**这里**](https://developer.bitcoin.org/devguide/transactions.html)


交易让用户花费聪(satoshis)。每个交易都是由几个部分构建的，这使得简单直接支付和复杂的交易成为可能。

# 介绍(Introduction)
本节将描述每个部分并演示如何将它们一起使用，以构建完整的交易。
要保持简单，本节coinbase交易不存在。 Coinbase交易只能由比特币矿工创建，它们是下面列出的许多规则的例外。这里不会指出coinbase对哪一条规则是例外，您可以在本指南的“区块链”部分中了解到。
<img src="https://developer.bitcoin.org/_images/en-tx-overview.svg" alt="block-min" />
上图显示了比特币交易的主要部分。每个事务至少有一个输入和一个输出。每个`输入`都花费了satoshis支付给以前的输出。然后，每个输出未花费交易输出（UTXO），直到之后作为输入花费它。当你的比特币钱包告诉你有10,000个Satoshi余额时，它真的意味着你有10,000个Satoshis在一个或多个UTXO中。

每个交易都以四字节的`交易版本号`(transaction version)为前缀，它告知比特币节点和矿工使用哪个规则集去验证他。这让开发人员为未来交易创建新规则可以不验证之前的交易。
<img src="https://developer.bitcoin.org/_images/en-tx-overview-spending.svg" alt="block-min" />

每个输出具有基于交易中其位置的隐含索引号 - 第一个输出的索引为零。输出包含以聪为计数单位的金额，它将其支付给满足条件pubkey脚本。任何可以满足Pubkey脚本的条件的人都可以花费给它的聪。

输入使用事务标识符（txid）和输出索引号(output index)（通常称为输出向量的vout“）来识别要花费的特定输出。它还具有一个签名脚本，它允许它提供满足Pubkey脚本中的条件的数据。 （序列号sequence number和锁定时间locktime相关，并将在以后的小节中涵盖。）

下面的数字有助于说明通过显示这些特性的流程， Alice发送Bob的交易以及稍后用来花费该交易的Bob来使用这些功能。 Alice和Bob都将使用最常见的支付给publickey - 哈希（P2PKH）交易类型。 P2PKH让Alice将Satoshis花在典型的比特币地址中，然后让Bob进一步使用简单的加密密钥对(cryptographic key pair)来花费那些Satoshis。

<img src="https://developer.bitcoin.org/_images/en-creating-p2pkh-output.svg" alt="block-min" />

Bob必须首先在Alice创建第一个交易之前生成私有/公钥对(private/public key pair)。比特币使用椭圆曲线数字签名算法（ECDSA）与SECP256K1曲线; SECP256K1私钥是256位随机数据，该数据的副本是确定地转换为SECP256K1公钥。由于可以可靠地重复转换，因此不需要存储公钥。

然后将公钥（Pubkey）加密哈希。Pubkey Hash也可以可靠地计算的得出，因此也不需要存储。哈希缩短并混淆公钥，使手动交易更容易，并为意外问题提供安全性，如在未来可能出现通过公钥重建出私钥。

Bob向Alice提供了公钥哈希。 公钥哈希通过编码得到比特币地址，这种地址使用base58编码得到的字符串包含了地址版本数字(address version number)，哈希值和一个错误校验(error-detection checksum)。比特币可以通过任何介质发送地址，包括单向媒介从而省去花费着和消费者的沟通，并且它可以进一步编码成另一种格式，例如包含`bitcoin URI`：`二维码`。

一旦Alice将地址和解码重新解码为标准哈希，就可以创建第一个交易。她创建了一个标准的P2PKH事务输出，包含说明如果可以证明他们控制与Bob散列公钥相对应的私钥，则允许任何人花费该输出。这些指令称为pubkey脚本或scriptPubkey。

Alice广播(broadcast)交易，它被添加到区块链中。网络将其视为未花费输出（UTXO），Bob的钱包软件将其显示为可花费的余额。

一段时间后，Bob决定花费UTXO，他必须创建一个输入，该输入引用其Alice创建的哈希交易，称为事务标识符（txid），以及其索引号使用的特定输出（输出索引out index），然后，他必须创建一个签名脚本(signature script) - 一个数据参满足Alice中放置在先前输出的Pubkey脚本中的条件的数据。签名脚本也称为scriptsig。

公钥脚本和签名脚本将SECP256K1 PUBKEYS和签名与条件逻辑组合，创建可编程授权机制。
<img src="https://developer.bitcoin.org/_images/en-unlocking-p2pkh-output.svg" alt="block-min" />

对于P2PKH式输出，Bob的签名脚本将包含以下两条数据：
1. 他的完整（未哈希）公钥，所以Pubkey脚本可以检查它是否哈希与Alice提供的Pubkey Hash相同的值。
2. 通过使用ECDSA加密公式进行的SECP256K1签名，将某些交易数据（下面描述）与BOB的私钥相结合。这使得Pubkey脚本验证Bob拥有创建公钥的私钥。

Bob的SECP256K1签名不仅仅是Bob控制他的私钥;它还使其交易篡改的非签名脚本部分篡改，因此Bob可以通过`点对点网络`(peer-to-peer network)安全地广播它们。
<img src="https://developer.bitcoin.org/_images/en-signing-output-to-spend.svg" alt="block-min" />

如上图所示，BOB签名的数据包括先前交易的txid和输出索引(output index)，先前的输出的Pubkey脚本，Pubkey脚本Bob创建，这将让下一个接收者花费此交易的输出，以及satoshis的数量花费下一个接收者。实质上，除了任何签名脚本外，签名的整个交易都符合完整的公钥和SECP256K1签名。

在签名脚本中签名和公钥后，Bob通过点对点网络(peer-to-peer network)将交易广播到比特币矿工。每个节点和矿工在广播之前独立地验证交易，进一步或尝试将其包含在新的交易块中。

# P2PKH 脚本验证(P2PKH Script Validation)


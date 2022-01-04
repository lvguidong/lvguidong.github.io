---
layout: post
title: BTC开发者指南翻译-交易(Transaction)
tags: 
- 区块链
- BlockChain
- BTC
date: 2021-12-25 16:02 +0800
---


翻译原文在 [**这里**](https://developer.bitcoin.org/devguide/transactions.html) 

交易让用户花费聪(satoshis)。每个交易都是由几个部分构建的，这使得简单直接支付和复杂的交易成为可能。



# 介绍(Introduction)
​	本节将描述每个部分并演示如何将它们一起使用，以构建完整的交易。
为了简单说明，本节coinbase交易不存在。 coinbase交易只能由比特币矿工创建，它们是下面列出的许多规则的例外。这里不会指出coinbase对哪一条规则是例外，您可以在本指南的“区块链”部分中了解到。
<img src="https://developer.bitcoin.org/_images/en-tx-overview.svg" alt="block-min" />
​	上图显示了比特币交易的主要部分。每个事务至少有一个输入和一个输出。每个`输入`都花费了satoshis支付给以前的输出。然后，每个输出未花费交易输出（UTXO），直到之后作为输入花费它。当你的比特币钱包告诉你有10,000个Satoshi余额时，它真的意味着你有10,000个Satoshis在一个或多个UTXO中。

​	每个交易都以四字节的`交易版本号`(transaction version)为前缀，它告知比特币节点和矿工使用哪个规则集去验证他。这让开发人员为未来交易创建新规则可以不验证之前的交易。
<img src="https://developer.bitcoin.org/_images/en-tx-overview-spending.svg" alt="block-min" />

​	每个输出具有基于交易中其位置的隐含索引号 - 第一个输出的索引为零。输出包含以聪为计数单位的金额，它将其支付给满足条件pubkey脚本。任何可以满足Pubkey脚本的条件的人都可以花费给它的聪。

​	输入使用事务标识符（txid）和输出索引号(output index)（通常称为输出向量的vout“）来识别要花费的特定输出。它还具有一个签名脚本，它允许它提供满足Pubkey脚本中的条件的数据。 （序列号sequence number和锁定时间locktime相关，并将在以后的小节中涵盖。）

​	下面的数字有助于说明通过显示这些特性的流程， Alice发送Bob的交易以及稍后用来花费该交易的Bob来使用这些功能。 Alice和Bob都将使用最常见的支付给publickey - 哈希（P2PKH）交易类型。 P2PKH让Alice将Satoshis花在典型的比特币地址中，然后让Bob进一步使用简单的加密密钥对(cryptographic key pair)来花费那些Satoshis。

<img src="https://developer.bitcoin.org/_images/en-creating-p2pkh-output.svg" alt="block-min" />

​	Bob必须首先在Alice创建第一个交易之前生成私有/公钥对(private/public key pair)。比特币使用椭圆曲线数字签名算法（ECDSA）与SECP256K1曲线; SECP256K1私钥是256位随机数据，该数据的副本是确定地转换为SECP256K1公钥。由于可以可靠地重复转换，因此不需要存储公钥。

​	然后将公钥（Pubkey）加密哈希。Pubkey Hash也可以可靠地计算的得出，因此也不需要存储。哈希缩短并混淆公钥，使手动交易更容易，并为意外问题提供安全性，如在未来可能出现通过公钥重建出私钥。

​	Bob向Alice提供了公钥哈希。 公钥哈希通过编码得到比特币地址，这种地址使用base58编码得到的字符串包含了地址版本数字(address version number)，哈希值和一个错误校验(error-detection checksum)。比特币可以通过任何介质发送地址，包括单向媒介从而省去花费着和消费者的沟通，并且它可以进一步编码成另一种格式，例如包含`bitcoin URI`：`二维码`。

​	一旦Alice将地址和解码重新解码为标准哈希，就可以创建第一个交易。她创建了一个标准的P2PKH事务输出，包含说明如果可以证明他们控制与Bob散列公钥相对应的私钥，则允许任何人花费该输出。这些指令称为pubkey脚本或scriptPubkey。

​	Alice广播(broadcast)交易，它被添加到区块链中。网络将其视为未花费输出（UTXO），Bob的钱包软件将其显示为可花费的余额。

​	一段时间后，Bob决定花费UTXO，他必须创建一个输入，该输入引用其Alice创建的哈希交易，称为事务标识符（txid），以及其索引号使用的特定输出（输出索引out index），然后，他必须创建一个签名脚本(signature script) - 一个数据参满足Alice中放置在先前输出的Pubkey脚本中的条件的数据。签名脚本也称为scriptsig。

​	公钥脚本和签名脚本将SECP256K1 PUBKEYS和签名与条件逻辑组合，创建可编程授权机制。
<img src="https://developer.bitcoin.org/_images/en-unlocking-p2pkh-output.svg" alt="block-min" />

​	对于P2PKH式输出，Bob的签名脚本将包含以下两条数据：
1. 他的完整（未哈希）公钥，所以Pubkey脚本可以检查它是否哈希与Alice提供的Pubkey Hash相同的值。
2. 通过使用ECDSA加密公式进行的SECP256K1签名，将某些交易数据（下面描述）与BOB的私钥相结合。这使得Pubkey脚本验证Bob拥有创建公钥的私钥。

Bob的SECP256K1签名不仅仅是Bob控制他的私钥;它还使其交易篡改的非签名脚本部分篡改，因此Bob可以通过`点对点网络`(peer-to-peer network)安全地广播它们。
<img src="https://developer.bitcoin.org/_images/en-signing-output-to-spend.svg" alt="block-min" />

​	如上图所示，BOB签名的数据包括先前交易的txid和输出索引(output index)，先前的输出的Pubkey脚本，Pubkey脚本Bob创建，这将让下一个接收者花费此交易的输出，以及satoshis的数量花费下一个接收者。实质上，除了任何签名脚本外，签名的整个交易都符合完整的公钥和SECP256K1签名。

在签名脚本中签名和公钥后，Bob通过点对点网络(peer-to-peer network)将交易广播到比特币矿工。每个节点和矿工在广播之前独立地验证交易，进一步或尝试将其包含在新的交易块中。



# P2PKH 脚本验证(P2PKH Script Validation)

​	验证过程需要执行签名脚本和公钥脚本。在一个P2PKH输出中，公钥脚本为：

> ```
> OP_DUP OP_HASH160 <PubkeyHash> OP_EQUALVERIFY OP_CHECKSIG
> ```

​	花费者的签名脚本被执行后添加到脚本的前面。在一个P2PKH交易中，签名脚本包含`secp256k1`（sig）签名和完整的公钥（pubkey），创建过程如下：

> ```
> <Sig> <PubKey> OP_DUP OP_HASH160 <PubkeyHash> OP_EQUALVERIFY OP_CHECKSIG
> ```

​	脚本语言是一种类`Forth`基于堆栈的语言，专门设计为无状态和非图灵完备的。无状态可以保证一旦一个交易被添加到区块链，没有条件能够表明它一直未被消费。非图灵完备（主要指没有循环和goto语句）可以使脚本不过于灵活便于预测，可以大大简化安全模型。

​	为了测试交易是否有效，签名脚本和公钥脚本操作一次执行一项，从 Bob 的签名脚本开始，一直到 Alice 的公钥脚本结束。下图显示了对标准 P2PKH 公钥脚本的评估；下图是该过程的描述:

​	<img src="https://developer.bitcoin.org/_images/en-p2pkh-stack.svg" alt="block-min" />

1. 签名（来自Bob的签名脚本）被添加到一个空栈中。由于它只是数据而已，所以不需要做其他的工作。公钥Key（也是来自签名脚本）添加在签名的上方。

2. 对于Alice的公钥脚本，首先执行最开始的`OP_DUP`操作。`OP_DUP`把当前堆栈顶部的数据复制一份再压入堆栈，这里指的是复制一份Bob提供的公钥。

3. 接下来执行`OP_HASH160`操作，得到堆栈顶部数据的Hash值并放在堆栈中。这里是创建的Bob的公钥的hash值。

4. 接下来Alice的公钥脚本将之前Bob在第一次交易中提供的公钥hash值入栈。到这里，堆栈顶部包含了2份Bob公钥的hash值。

5. 从这里开始变的有趣：Alice的公钥脚本执行`OP_EQUALVERIFY`操作。`OP_EQUALVERIFY`等价于顺序执行`OP_EQUAL`和`OP_VERIFY`（未显示）。

   `OP_EQUAL`（未显示）价差堆栈顶部的两个元素。这里是检查由Bob提供的公钥的hash值和Alice在第一交易中获得的公钥Hash值是否相等。`OP_EQUAL`将栈顶参与比较的两个值出栈，并把它们的比较结果（0表示false，1表示true）压入堆栈。

   `OP_VERIFY`（未显示）检查栈顶元素。如果这个值是false，它立即结束校验过程，当前交易校验结果失败。否则它将栈顶的true元素弹出。

6. 最终，Alice的公钥脚本执行`OP_CHECKSIG`，通过刚刚检验过的Bob提供的公钥来检查Bob提供的用私钥产生的签名。如果签名跟公钥匹配，并且其中的内容时使用都是需要签名的数据，`OP_CHECKSIG`把true添加到堆栈顶部。

如果执行完公钥脚本后最终栈顶元素不是false，则判定交易**有效**（假定这里交易没有其他的问题）。



# P2SH脚本(P2SH Scripts)

​	公钥脚本由支出者创建，支出者对脚本内容的兴趣其实不大。实际收款人才是真正关心脚本条件的人，并且如果他们愿意，他们可以让支出者是哪个指定的公钥脚本。不幸的是，配置的公钥脚本没有较短的比特币地址简单，并且没有标准的方式能够把他们和BIP70（后面进行讨论）广泛应用前的程序结合起来。

​	为了解决这些问题，P2SH（pay-to-script-hash，支付到脚本hash）交易在2012年产生，让支出者创建一个包含兑换脚本hash值的公钥脚本。

​	基本的P2SH工作流程示例如下，它看起来和P2PKH工作流几乎相同。Bob使用自己喜欢的任意的脚本作为兑换脚本，计算它的hash值，然后将兑换脚本的hash值提供给Alice。Alice创建一个P2SH版本的输出，其中包含了Bob的兑换脚本的hash值。

​	<img src="https://developer.bitcoin.org/_images/en-creating-p2sh-output.svg" alt="block-min" />

​	当BObo想花掉这笔输出，他在签名脚本中将完整的兑换脚本和签名一起提供。p2p网络保证完整的兑换脚本hash值和Alice在她的输出中存入的hash值相同，然后就像执行公钥脚本一样执行该兑换脚本，即如果兑换脚本返回非false则允许Bob花费这笔输出。

<img src="https://developer.bitcoin.org/_images/en-unlocking-p2sh-output.svg" alt="block-min" />

​	赎回脚本的哈希与公钥哈希具有相同的属性——因此只需稍作改动即可将其转换为标准的比特币地址格式，以将其与标准地址区分开来。这使得收集 P2SH 式地址就像收集 P2PKH 式地址一样简单。hash还会混淆赎回脚本中的任何公钥，因此 P2SH 脚本与 P2PKH 公钥散列一样安全。



# 标准交易(Standard Transactions)

​	比特币在发现了几个早期版本的危险Bug之后，增加了一个测试。该测试仅仅来自指信任网络的交易，信任网络需满足1)公钥脚本和签名脚本匹配一个信任模板小集合，2)该网络的其他交易没有违反另外一个保证网络行为正常的集合的内容。这就是`IsStandard()`测试，传递给该测试的交易称为**标准交易**。

​	非标准交易是指那些未通过测试的交易，它们仍然可能被没有使用默认比特币核心设置的节点接受。如果他们被包含到区块当中，同样需要避免`IsStandard()`测试和被处理。

​	










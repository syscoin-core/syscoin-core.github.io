---
title: Segregated Witness Upgrade Guide
name: segwit-upgrade-guide
id: en-segwit-upgrade-guide
type: posts
layout: post
lang: en
permalink: /en/2016/10/27/segwit-upgrade-guide/
version: 1
excerpt: Almost two years of iterative design, development, and testing has gone into the version of segwit being released in Syscoin Core 0.13.1, with much of the effort over the last year focused on making it as easy as possible for existing Syscoin users, businesses, developers, and miners to upgrade to segwit.
redirect_from:
  - /en/segwit_adoption/
  - /zh_CN/segwit_adoption/
---
{% include toc.html %}
{% include references.md %}

Almost two years of iterative design, development, and testing has gone into the version of segwit being released in Syscoin Core 0.13.1, with much of the effort over the last year focused on making it as easy as possible for existing Syscoin users, businesses, developers, and miners to upgrade to segwit.

Initial segwit adoption requires the participation of two groups:

- **[Miners][miners guide]** representing 95% or more of the total Syscoin network hash rate must signal support for segwit in order to  lock-in segwit’s activation.

- **[Full nodes][node guide]** run by a reasonable number of users and business to validate the payments they receive need to be upgraded to Syscoin Core 0.13.1 or above, or another segwit-compatible implementation in order to incentivize miners to follow segwit’s rules after segwit activates.  (This is Syscoin’s normal incentivization mechanism where miners only receive income for generating a block if they follow all of the consensus rules, which will include the new segwit consensus rules once segwit activates.)

The segwit soft fork has been designed to allow individuals in both groups to voluntarily decide whether or not they want to adopt segwit, and guides are provided below for both those who want to adopt segwit and those who don’t.

If enough miners do decide to adopt segwit, it will eventually activate and wallet users will be able to begin creating transactions with segregated witnesses.  The segwit soft fork has also been designed to be both backwards and forwards compatible with all commonly-used wallets, so wallet developers and users can also independently decide whether they want to adopt segwit or continue making transactions without segregated witnesses.  Guides are provided below for both adopting and non-adopting [developers][dev guide] and [users][user guide].

In addition to instructions, the end of each guide section below also provides a short list of recommended places to ask any segwit-related questions you may have.

## Miners

*This section is written for solo miners and mining pool operators.  Pool miners should contact their pool operators for information about what they need to do (if anything) to upgrade or not upgrade to segwit.* 

The BIP9 soft fork deployment mechanism is being used for segwit---the same mechanism used for the BIP 68/112/113 soft fork that activated on 4 July 2016.  Whether you wish to upgrade or not, you should understand the important stages of the upgrade process:

- **Started:** Segwit will be in the *started* stage from the beginning of the first retarget period on or after 15 November 2016 until it either activates or is considered failed (under BIP9’s policy) after one year of not reaching locked-in.  During this time, miners who are willing and able to enforce segwit’s new consensus rules will be signaling their intent to do so by placing bit 1 in the block header versionbits field.

- **Locked-in:** if 95% of blocks during a 2,016-block retarget period signal support for segwit, the segwit soft-fork will be *locked-in* with *activation* scheduled for 2,016 blocks later (about two weeks).

- **Activated:** after the completion of the *locked-in* period, the miners who signaled readiness to enforce segwit will begin producing segwit-style blocks that contain transactions with segregated witnesses.

### Upgrading

The BIP9 parameters for the segwit soft fork allow miners to begin signaling their support for it at the beginning of the first retarget period on or after 15 November 2016.  To signal support, you will need to do the following:

- Upgrade the full node you use for transaction selection and block construction to Syscoin Core 0.13.1+ or another segwit-compatible full node.

- Upgrade your mining software, mining pool software, or both to a segwit-compatible version.

- Begin producing blocks containing segwit’s BIP9 versionbit, which is bit 1.

When segwit is activated, you will want to be able to mine and relay segwit-style blocks.  The following mining software has been upgraded to support segwit.

- Full nodes:
  - [Syscoin Core](https://syscoin.org/en/download) 0.13.1 or later
  - [Syscoin Knots](http://syscoinknots.org/) 0.13.1 or later
  - [Btcd](https://github.com/btcsuite/btcd/pull/656)\*

- Mining software:
  - [BFGMiner](https://github.com/luke-jr/bfgminer)\*
  - [CGMiner](https://github.com/ckolivas/cgminer)
  - [libblkmaker](https://github.com/syscoin/libblkmaker/pull/6)\*

- Pool software:
  - [ckpool](https://bitbucket.org/ckolivas/ckpool)
  - [Eloipool](https://github.com/luke-jr/eloipool)
  - [Stratum-Mining](https://github.com/slush0/stratum-mining/pull/16)\*
 
- Relay software:
   - [Syscoin FIBRE](http://syscoinfibre.org/)

Please note that software that supports the GetBlockTemplate (GBT) RPC must be upgraded to support the BIP9 and BIP145 changes to GBT.  All the programs linked above that support GBT have been upgraded.

Segwit is already activated and enforced on testnet, so you may find it useful to test your infrastructure upgrade by mining with some small amount of hashrate on testnet.  Alternatively, Syscoin Core 0.13.1’s regression test mode (regtest) also supports segwit by default.

**Questions?** Solo miners and pool operators are welcome to ask for help in #syscoin-mining on irc.freenode.net.  Pool miners should contact their pool operators for any questions about the pool’s policies regarding segwit.

### Not upgrading

This section describes what you can do as a miner if you don’t want to enforce segwit.

During the *started* phase, if you don’t want to adopt segwit, you may simply refuse to upgrade to a segwit-compatible full node such as Syscoin Core 0.13.1 or above, as well as avoiding any mining software that assumes you want to set segwit’s versionbit of bit 1.

If segwit reaches *locked-in*, you still don’t need to upgrade, but upgrading is strongly recommended.  The segwit soft fork does not require you to produce segwit-style blocks, so you may continue producing non-segwit blocks indefinitely.  However, once segwit activates, it will be possible for other miners to produce blocks that you consider to be valid but which every segwit-enforcing node rejects; if you build any of your blocks upon those invalid blocks, your blocks will be considered invalid too.

For this reason, after segwit reaches *locked-in*, it is recommended that you either upgrade your full node to Syscoin Core 0.13.1 or later (or a compatible full node), or that you follow the “not upgrading” instructions in the Full Node section below to use Syscoin Core 0.13.1 or later as a filter for your pre-segwit software.

## Full node users

*This section is written for anyone operating a full node, including both businesses and individuals.* 

Full nodes prevent their users from accepting any blocks that violate any of Syscoin’s consensus rules.  In a soft fork upgrade such as segwit, new rules are added, and any nodes that don’t upgrade won’t know about those new rules.  This is not a problem: the segwit soft fork is designed to allow non-upgraded users to continue using Syscoin the same way they did before the soft fork.

However, anyone who wants to use the features enabled by the segwit soft fork will want to know that a sufficient number of full node users have upgraded their nodes to refuse blocks and transactions that violate the segwit rules, thereby providing a strong incentive for miners to follow segwit’s updated consensus rules.

This system has worked well in the past, with at least 25% of reachable nodes (and usually 50% or more) upgraded before each of the previous several soft forks activated (not counting the BIP50 emergency and temporary soft fork).  There is no reason to expect any differently for the segwit soft fork, and upgrading is an easy way for people who support segwit to help encourage its adoption.  Those who are uninterested in segwit may, of course, simply not upgrade.  Details for both cases are described below.

### Upgrading

To upgrade to a segwit-compatible release, download a segwit-compatible version of your full node software (such as the [Syscoin Core 0.13.1 release](https://syscoin.org/en/download)), ensure that the files you downloaded are legitimate (using PGP or another method), stop the old version of your node software, and start the new version of the software. Note that if you upgrade after segwit has activated, your node will need to download and resync blocks from the activation point forward, since the old version did not download them completely.

You may use the Syscoin Core RPC `getblockchaininfo` to track the status of the segwit soft fork (labeled `segwit` in the list of BIP9-style soft forks).  This information includes how many recent blocks have been produced by miners signaling their intention to enforce segwit’s new consensus rules.  The results from the `getblockchaininfo` RPC will also let you determine when segwit’s soft fork has become locked in (meaning it will activate within the next 2,016 blocks) and activated (meaning it is now enforced by miners).

The wallet provided with Syscoin Core 0.13.1 will continue to only generate non-segwit P2PKH addresses for receiving payment by default.  Later releases are expected to allow users to choose to receive payments to segwit addresses.

If you’re a developer or expert user who wants to generate addresses for testing, please see the [segwit dev guide][].

**Questions?** If you use Syscoin Core as your full node, please see the [Get Help](https://syscoin.org/en/syscoin-core/help) page on Syscoin.org for various support options.  If you use another full node, the best place to ask is wherever users of your full node software go for support.  The maintainers of your software will be familiar with the idea behind segwit at the very least, and they will be able to tell you when it will be implemented and how it will affect you.

### Not upgrading

If you don’t want to upgrade to segwit and you aren’t a miner, you may simply continue using your current full node software.  Segwit is implemented as a soft fork, so you don’t need to upgrade.  You also don’t need to upgrade any wallets that connect to your full node; they will continue working as they did before (see the [wallet section][user guide] below for details).

However, if you accept transactions with fewer blocks of confirmation (such as a single block or two), please note that after a soft fork activates, there is a small increased risk that full nodes which don’t upgrade will temporarily accept invalid blocks.  The situation will resolve itself within a few blocks as upgraded miners continue to enforce the new segwit consensus rules, but there is no guarantee that transactions shown as confirmed in the invalid block will continue to be confirmed in valid blocks.      

The easiest way to prevent this problem is to upgrade to Syscoin Core 0.13.1 or later, or another full node release that is compatible with the segwit soft fork.  If you still don’t wish to upgrade, it is possible to use a newer Syscoin Core release as a filter for older Syscoin Core releases.

![Filtering by an upgraded node](/assets/images/filtering-by-upgraded-node.svg)

In this configuration, you set your current Syscoin Core node (which we’ll call the “older node”) to connect exclusively to a node running Syscoin Core 0.13.1 or later (which we’ll call the “newer node”).  The newer node is connected to the Syscoin P2P network as usual.  Because the newer node knows about the segwit changes to the consensus rules, it won’t relay invalid blocks or transactions to the older node---but it will relay everything else.

When using this configuration, please note that the older node, if it uses Syscoin Core defaults, will not see transactions using segwit features until those transactions are included in a block.

Configuration:

For the newer node, start it normally and let it sync the blockchain.  At present, you cannot use a pruned node for this purpose because pruned nodes will not act as relay nodes.  You may optionally start the newer node with either or both of the following command line parameters so that it treats the older node as special (these options may also be placed in Syscoin Core’s configuration file):

~~~
  -whitebind=<addr>
       Bind to given address and whitelist peers connecting to it. Use
       [host]:port notation for IPv6

  -whitelist=<netmask>
       Whitelist peers connecting from the given netmask or IP address. Can be
       specified multiple times. Whitelisted peers cannot be DoS banned
       and their transactions are always relayed, even if they are
       already in the mempool, useful e.g. for a gateway
~~~

For the older node, first wait for the newer node to finish syncing the blockchain and then restart the older node with the following command line parameter (this may also be placed in the Syscoin Core configuration file):

    -connect=<IP_address_or_DNS_name_of_newer_node>

For example,

    -connect=192.168.8.8

This will cause the older node to only connect to the newer node so that all blocks and transactions are filtered by the newer node.

## Wallet users

*This section is written for anyone using a lightweight wallet, a web wallet, a wallet connected to a personal full node, or any other wallet.* 

### Upgrading

If you do want to upgrade to segwit, you will first need to wait for miners to activate segwit, and then you will need a wallet that supports receiving and spending segwit-style payments.  This applies to Syscoin Core’s wallet, lightweight wallets, and wallets where third-parties send and receive syscoins on your behalf (sometimes called web wallets).  Users of Syscoin Core or other full nodes should also read the section above about full nodes.

After your wallet is upgraded to support segwit, it will generate receiving addresses that start with a ‘3’ (a P2SH address).  Some wallets have been generating P2SH addresses for years, so this may not be a change for you.

All commonly used wallets are able to pay P2SH addresses, so you will be able to receive payments from any common wallet, whether or not they have upgraded to segwit.  When spending your syscoins after the upgrade to segwit, you will still be able to pay the original type of Syscoin addresses that start with a ‘1’ (a P2PKH address) as well as being able to pay other users of P2SH addresses.

When spending your syscoins with a segwit wallet, you will notice the following:

- When spending only syscoins you received before upgrading, you should notice no difference to transactions you created before upgrading.

- When spending syscoins you received after upgrading to segwit to someone who has not upgraded to segwit, they may not see your transaction until after it is included in a block.  This is a safety feature that avoids showing them transactions their wallet doesn’t entirely understand until those transactions have confirmed.  After the transaction confirms, they will be able to see and spend the syscoins you sent them like normal.

- When spending syscoins you received to your new P2SH addresses after upgrading, you may notice that the transaction fee you pay is slightly lower than when spending non-segwit payments you previously received.  This is because the part of the transaction that contains your signature (the “witness”) doesn’t need to be accessed quickly by Syscoin full nodes, so segwit allows miners to store up to 4 times as many witness bytes in a block as they store non-witness bytes.  This better aligns the cost of creating a block (and thus its transaction fees) with the actual costs of operating a full node.

**Questions?** If you have any questions, the best place to ask is wherever users of your wallet go for support.  The maintainers of your wallet will be familiar with the ideas behind segwit, and they will be able to tell you if segwit will be implemented for your wallet, when that might happen, and how it will affect your usage of your wallet.

### Not upgrading

If you don’t want to upgrade to segwit, you may simply continue to use any wallet that has not added segwit support.  Even though you haven’t upgraded, you will be able to transact with both users who have upgraded to segwit and users who, like you, haven’t upgraded to segwit.

If you don't upgrade, you may experience one difference: if someone who has upgraded to segwit pays you, your wallet may not show you the payment until after it has been included in a block.  This is a safety feature that prevents your wallet from seeing transactions it doesn’t completely understand until they’ve been confirmed by a miner.

## Syscoin software developers

*This section is written for developers of any Syscoin software that processes transactions or blocks.*

All Syscoin software (including mining software, provided it only selects transactions that follow the default relay policy) should continue working as before, and you will only need to upgrade your software if you want to take advantage of segwit’s new features.

Segwit is described for developers in the following documents:

- **[Segwit wallet developers guide][segwit dev guide]:** a summary description of everything you need to know to upgrade your wallet to support segwit.  

- **[BIP141][] Segregated witness (consensus layer):** developers seeking to implement any aspect of segwit should read the Specification section of this document.  Developers of mining and full node software will find the BIP9 parameters for segwit in the Deployment section.

- **[BIP143][] Transaction signature verification for version 0 witness program:** developers of any software that wish to create or verify segwit signatures should read the Specification section of this document and are recommend to use the Example section for testing.

- **[BIP144][] Segregated witness (peer services):** developers seeking to send or receive segregated witnesses over the Syscoin P2P network should read the Specification section of this document.

- **[BIP145][] getblocktemplate updates for segregated witness:** developers of mining and other software that produce or use the GetBlockTemplate RPC should read BIP145 and the related GBT changes in [BIP9][].

- **[BIP147][] Dealing with dummy stack element malleability:** developers of wallets and especially new transaction scripts should be aware of this new consensus rule that mirrors a long-existing default network relay policy in forbidding passing anything besides the `OP_0` “null” opcode as the “dummy” parameter to a checkmultisig-style opcode.  After segwit is activated, this new consensus rule will apply to both transactions that use segwit and those that don’t.

Please note, [BIP142][] (address format for segregated witness) is in *deferred* status (as defined by BIP1) and is not proposed as a standard.  Instead, wallet developers are invited to discuss on the [syscoin-dev mailing list][syscoin-dev] the creation of a new Syscoin address format that will be more usable than current base58check-encoded addresses.

Most implementation details for BIPs 141, 143, 144, and 145 may be found in [Syscoin Core PR#8149](https://github.com/syscoin/syscoin/pull/8149).  The implementation for BIP147 may be found in [Syscoin Core PR#8636](https://github.com/syscoin/syscoin/pull/8636).

For testing changes on a segwit-enabled network, testnet (testnet3) has supported segwit for several months now and includes a large number of segwit blocks, including blocks that have very nearly the maximum block size allowed for by segwit.  Syscoin Core’s regression-testing (regtest) mode also supports segwit by default in Syscoin Core 0.13.0 and 0.13.1.

A number of free and open source software Syscoin wallets and packages besides Syscoin Core have also already added segwit compatibility or have segwit-compatible code ready to deploy, so you may be able to use their code changes as an example for updating your software if their copyright license is compatible with your code.

**Questions?** Syscoin development questions may be asked in the #syscoin-dev IRC chatroom on irc.freenode.net.  Questions may also be asked on Syscoin.StackExchange.com and the SyscoinTalk.org [technical discussion board.](https://bitcointalk.org/index.php?board=6.0)

[miners guide]: #miners
[node guide]: #full-node-users
[dev guide]: #syscoin-software-developers
[user guide]: #wallet-users
[segwit dev guide]: /en/segwit_wallet_dev/

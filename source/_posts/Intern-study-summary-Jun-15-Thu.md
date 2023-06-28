---
title: Intern study summary - Jun 15 Thu
date: 2023-06-23 01:44:09
excerpt: Learning record for what I learned during my internship at 300K—a quant company on the day of 15th of Jun, Thu
tags:
  [
    Intern,
    Intern Log,
    Python,
    Python Grammar,
    PyCharm,
    PyCharm Professional,
    Terminal,
    Terminal Command,
    Intern At 300K,
    Learning Log,
    Tech,
    Decentralized Exchange,
    Dex,
    xarray,
  ]
categories:
  - Daily Logs
  - Intern Log
  - Tech Summary
---

# Intern study summary - Jun 15 Thu

This blog is a record for what I learned during my internship at 300K—a quant company.

I spent most of my day on fixing bugs of the project. Thus this blog might include less new information.

# Asymmetric Encryption

Think about a scenario: Alice is communicating with Bob by mail. The problem is that they do not want other people to see their conversation but they cannot ensure that people won’t see their mails(other people might be the courier or other people who had access during the posting process).

Thus they figured a way to encrypt the mail. For example, they let every character move to the next one: “I love you” become “J mpwf zpv”. The way to cipher the mail is also called a **_key_**. Anyone with the cipher key could decipher the mail. However, a new problem came out: how can they tell each other about the way of encrypting? They cannot send the key to each other since the sending process could be monitored.

The above mentioned way of encrypting is called symmetric encryption, where the same key is used for both encryption and decryption.

{% asset_img symmetric-encrypt.jpeg symmetric encryption %}

Now, to solve the problem of having to send the key, a new way of encrypting was invented, which is the asymmetric encryption.

Think about a mail box with two doors.

One of the door is a tiny small door that only allows putting in mails. The second door is a bigger door. Only through this door can a mail be taken out.

Each door is paired with a key, of course. The key to open the first door (that only allows putting in mails) is called **_public key(pk)_**. The second key is thus called a **_private key_**, or **_secret key_**(**_sk_**).

Now, let’s go through how this weird box solved the problem of symmetric encrypt:

Before Bob send an mails to Alice, Alice send this mail box with two door aforementioned and a public key to Bob. Once Bob got the box and the public key, Bob put an mail into the box. Then send the mail which is inside the box back to Alice. Since Alice did not give her private key to anyone, no one could take the mail out of the box until Alice get the box.

This solution made the communicating process much securer. The mail itself is called _plaintext_, the box with mail inside is called _ciphertext_ (I use putting a mail into a box as a metaphor of cipher.)

{% asset_img assymetric-encrypt.jpeg assymmetric encryption %}

# `make`

`make` is a build automation tool that automatically builds executable programs and libraries from source code by reading files called `Makefiles` which specify how to derive the target program.

If you are looking to use the `make` command on your Mac, here's what you should do:

1. Install Xcode: Xcode is a free development tool from Apple. `make` is part of this toolset. To install Xcode, you need to go to the Apple App Store, search for Xcode, and install it.
2. Once you have Xcode installed, open a terminal window (you can find Terminal by searching for it in Spotlight - the magnifying glass icon at the top right of your screen).
3. In the Terminal, type the following command to install Command Line Tools, which includes `make`:

   ```shell
   xcode-select --install
   ```

   You might be prompted for your password. Type it in and press enter. This will start the download and installation process.

4. After the installation is completed, you can verify the installation by typing `make -v` in the Terminal. If `make` is installed, you'll see some version information outputted.

In case you have already installed Xcode and Command Line Tools, and you're just looking for how to use the `make` command, here's an example:

Let's say you have a C program and you want to compile it using `make`.

First, create a file named `Makefile` in your project directory and it might look something like this:

```makefile
myprogram: myprogram.c
    gcc -o myprogram myprogram.c
```

This `Makefile` tells `make` how to build your program. Here `myprogram` is the target, `myprogram.c` is the dependency, and `gcc -o myprogram myprogram.c` is the command `make` will run to create or update the target.

Then you can just run `make` in your terminal:

```shell
make
```

This will compile your `myprogram.c` file into an executable called `myprogram`.

If your source code changes, you can recompile just by running `make` again. If your code hasn't changed, `make` won't do anything.

## `make: Nothing to be done for 'xxxx'.`

I got this problem when I have the `'xxxx'` in my Makefile. Make sure that the Makefile the format is the same. For a new make command, use `tab` instead of `space`, I found PyCharm will automatically convert `tab` to `space` which took me a while to debug.

# `xarray` library in Python

I need to merge two `DataArrays` together when I was processing the data.

Here is the detailed documentation for `xarray`: [Documentation](https://docs.xarray.dev/en/stable/user-guide/combining.html#concatenate)

## concat

Here is a visual sense of concat:

{% asset_img xarray-concat.jpeg xarray concat %}

Here is the [video](https://www.youtube.com/watch?v=xdrcMi_FB8Q&t=1102s&ab_channel=WestDRI) I watched to learn about `xarray`, they also compared it with `numpy` and `pandas`, which might be helpful for those who are familiar with those two library.

```python
>>> import xarray as xr
>>> import numpy as np
>>> data = xr.DataArray(
... np.random.random(size=(4, 3)),
... dims=("y", "x"),
... )
>>> data
<xarray.DataArray (y: 4, x: 3)>
array([[0.66017383, 0.01435655, 0.82392498],
       [0.06476051, 0.6504625 , 0.61194439],
       [0.40693448, 0.22710373, 0.30344243],
       [0.06840932, 0.32321194, 0.13938409]])
Dimensions without coordinates: y, x
>>> data2 = xr.DataArray(np.random.random(size=(4, 3)),dims=("y", "x"),)
>>> data2
<xarray.DataArray (y: 4, x: 3)>
array([[0.90414607, 0.12080436, 0.12541235],
       [0.68558428, 0.27262736, 0.74151799],
       [0.21410586, 0.02924536, 0.32955468],
       [0.01220016, 0.25980773, 0.47056426]])
Dimensions without coordinates: y, x
>>> data3 = xr.DataArray(np.random.random(size=(4, 4)),dims=("y", "x"),)
>>> data3
<xarray.DataArray (y: 4, x: 4)>
array([[0.19104632, 0.40055872, 0.20014418, 0.60157153],
       [0.94211448, 0.21033792, 0.63576454, 0.77343922],
       [0.3038103 , 0.70945801, 0.95828555, 0.19862075],
       [0.73792562, 0.52394306, 0.9605408 , 0.48479543]])
Dimensions without coordinates: y, x
```

```python
>>> xr.concat([data, data2], dim = "y")
<xarray.DataArray (y: 8, x: 3)>
array([[0.66017383, 0.01435655, 0.82392498],
       [0.06476051, 0.6504625 , 0.61194439],
       [0.40693448, 0.22710373, 0.30344243],
       [0.06840932, 0.32321194, 0.13938409],
       [0.90414607, 0.12080436, 0.12541235],
       [0.68558428, 0.27262736, 0.74151799],
       [0.21410586, 0.02924536, 0.32955468],
       [0.01220016, 0.25980773, 0.47056426]])
Dimensions without coordinates: y, x
>>> data
<xarray.DataArray (y: 4, x: 3)>
array([[0.66017383, 0.01435655, 0.82392498],
       [0.06476051, 0.6504625 , 0.61194439],
       [0.40693448, 0.22710373, 0.30344243],
       [0.06840932, 0.32321194, 0.13938409]])
Dimensions without coordinates: y, x
>>> data2
<xarray.DataArray (y: 4, x: 3)>
array([[0.90414607, 0.12080436, 0.12541235],
       [0.68558428, 0.27262736, 0.74151799],
       [0.21410586, 0.02924536, 0.32955468],
       [0.01220016, 0.25980773, 0.47056426]])
Dimensions without coordinates: y, x
>>> xr.concat([data, data3], dim = "x")
<xarray.DataArray (y: 4, x: 7)>
array([[0.66017383, 0.01435655, 0.82392498, 0.19104632, 0.40055872,
        0.20014418, 0.60157153],
       [0.06476051, 0.6504625 , 0.61194439, 0.94211448, 0.21033792,
        0.63576454, 0.77343922],
       [0.40693448, 0.22710373, 0.30344243, 0.3038103 , 0.70945801,
        0.95828555, 0.19862075],
       [0.06840932, 0.32321194, 0.13938409, 0.73792562, 0.52394306,
        0.9605408 , 0.48479543]])
Dimensions without coordinates: y, x
```

# DEX (decentralized exchanges)

DEX and CEX refer to two types of cryptocurrency exchanges: Decentralized Exchanges (DEX) and Centralized Exchanges (CEX).

CEXs operate similarly to traditional financial exchanges. They are managed by a central authority (the company running the exchange), which provides a platform for buyers and sellers to trade cryptocurrencies. They handle the security of users' funds and users' private keys are held by the exchange. Examples of CEXs include Binance, Coinbase, and Kraken.

DEXs, on the other hand, operate without a central authority. They use blockchain technology to enable direct peer-to-peer cryptocurrency transactions. Users keep control of their private keys and funds, which adds to the security, but also means users are fully responsible for their own security. Examples of DEXs include Uniswap, SushiSwap, and PancakeSwap.

Key differences include:

1. Custody: In CEX, the exchange has custody of the funds while in DEX, users have full control of their funds.
2. Anonymity: DEXs often don't require user identification, making them more anonymous than CEXs, which usually require some form of KYC (Know Your Customer) process.
3. Number and Types of Assets: CEXs often have a wider range of cryptocurrencies and other assets, while DEXs mainly focus on tokens of a specific blockchain (like ERC20 tokens for Ethereum).
4. Trust: In CEXs, users need to trust the exchange, while in DEXs, trust is based on smart contracts and the underlying blockchain technology.

## blockchain

A blockchain is a type of distributed ledger technology that stores data across multiple systems in a network. It's designed to be secure, transparent, and resistant to modification of the data. This data is stored in blocks, and each block is linked to the one before it, forming a chain (hence the name, blockchain).

Here's how it works in a bit more detail:

1. **Data Transactions**: When a transaction occurs (like a Bitcoin being sent from one person to another), that transaction is grouped together with other transactions that have occurred in the same time frame into a block.
2. **Block Verification**: This block is then sent to all the nodes in the network. Nodes are computers that are participating in the blockchain network. They have a copy of the entire blockchain and work to verify the transactions in the new block.
3. **Consensus**: Once the nodes reach consensus and confirm that the transactions in the block are valid, the block is added to the chain. The new block includes a unique identifier called a cryptographic hash, as well as the hash of the previous block in the chain, which is what links the blocks together in a chain and makes the blockchain secure.

One of the most famous uses of blockchain technology is for cryptocurrencies like Bitcoin, but there are many other potential uses, including supply chain tracking, voting systems, identity verification, and more.

The main advantages of blockchain technology are its transparency, security, and decentralization. However, it also faces challenges, such as scalability and the significant amount of energy required by the consensus process in many blockchains (like Bitcoin's proof-of-work system).

## How DEX uses blockchain exactly?

Decentralized Exchanges (DEXs) use blockchain technology to facilitate direct peer-to-peer transactions, bypassing the need for a central authority.

Here's a more detailed explanation:

1. **Smart Contracts**: DEXs are primarily built using smart contracts. These are self-executing contracts with the terms of the agreement directly written into code. They're stored on the blockchain, which makes them transparent and immutable. In a DEX, smart contracts are used to facilitate and automate the trading process.
2. **On-chain Transactions**: All trades occur directly on the blockchain. When a user submits a trade on a DEX, it's essentially a transaction that's broadcast to the entire network, just like any other blockchain transaction.
3. **Liquidity Pools**: In many DEXs, liquidity is provided by users themselves through a system known as liquidity pools. Instead of an order book matching buyers and sellers, users deposit pairs of tokens into a pool, creating a market that others can trade against. The smart contract maintains the balance between the two tokens in the pool based on the trades happening.
4. **Wallet Integration**: DEXs allow direct integration with decentralized wallets (like MetaMask). Since trades are wallet-to-wallet, users maintain control over their private keys throughout the entire process, enhancing security.
5. **Token Swaps**: DEXs primarily facilitate token swaps, which is the exchange of one cryptocurrency for another. This is achieved through smart contracts which handle the swap automatically once the user initiates the transaction.

Remember, though, that while the decentralized nature of DEXs offers advantages like increased privacy and control over one's assets, it also means that the responsibility for security rests largely with the user. If you lose access to your wallet, for instance, the DEX can't help you recover it.

## More resources:

Here are some of passages and videos I watched:

In English:

- Videos:
  - [What is a DEX? How A Decentralized Exchange Works](https://www.youtube.com/watch?v=2tTVJL4bpTU&ab_channel=WhiteboardCrypto)
  - A good YouTube Channel: [Whiteboard Crypto](https://www.youtube.com/c/WhiteboardCrypto)

In Chinese:

- Passages:
  - [一文读懂 DEX 去中心化交易所](https://zhuanlan.zhihu.com/p/634263865)
- Videos:
  - [【科普篇】什么是去中心化交易所 Uniswap(dApp)以及什么是 Uni 代币动画演示中英双字幕](https://www.bilibili.com/video/BV1pG4y1b7HY/?spm_id_from=333.337.search-card.all.click&vd_source=d84085799dfdb99d199a04d156250394)
  - [尚硅谷区块链全套教程完整版(深入掌握以太坊核心技术)](https://www.bilibili.com/video/BV1NJ411D7rf/?spm_id_from=333.337.search-card.all.click&vd_source=d84085799dfdb99d199a04d156250394)

# yaml

YAML, which stands for "YAML Ain't Markup Language," is a human-friendly data serialization standard for all programming languages. It's often used for configuration files and in applications where data is being stored or transmitted. YAML files typically have the extension `.yaml` or `.yml`.

In YAML, we use indentation to represent hierarchical relationships, similar to the way it's done in Python. This can be easier to read than other data formats like JSON or XML, where structural information can be obscured by syntactical clutter.

There are several data types that YAML can represent, but two of the most important are lists (also known as sequences) and maps (also known as dictionaries or hashes).

1. **Lists**: Lists are simply a sequence of items. In YAML, lists are represented by each item in the list being on a new line with the same indentation and a leading dash (`-`).

   For example:

   ```yaml
   fruits:
     - Apple
     - Banana
     - Orange
   ```

   In this case, "fruits" is a list that includes "Apple", "Banana", and "Orange".

2. **Maps**: Maps are used to link together pairs of items. In YAML, maps are represented with a colon (`:`) between the items in each pair.

   For example:

   ```yaml
   person:
     name: John
     age: 30
   ```

   In this case, "person" is a map that includes "name: John" and "age: 30".

Moreover, YAML supports complex nesting of sequences and maps. This allows you to create a data structure as complex as your application requires. For example:

```yaml
persons:
  - name: John
    age: 30
  - name: Jane
    age: 25
```

In this case, "persons" is a list of maps, each map represents a person with attributes "name" and "age".

YAML files are commonly used for configuration purposes, especially in DevOps environments, and can be found in applications like Ansible, Kubernetes, and Docker. They are also often used for defining workflows, like GitHub Actions workflows, or setting up continuous integration/continuous deployment (CI/CD) pipelines. Additionally, they can be used for data exchange between languages with different data structures, data serialization, and more.

# Command Line tips & PyCharm:

I am using the PyCharm professional that is the most recent version up to today.

## `command + shift + f` to search every thing in project

Sometimes you can only remember a line of code or a specific special word in one of the files in your project. When you are trying to locate such words, or trying to local such files by such words or lines of codes, you could try `command + shift + f`. This command will find all the occurrence of your input string in all the files.

## `command + shift + o` to search file

This is used to such a specific file. For example, you want to open a file named “self-destruct.py”, you could search “self-destruct.py” after using `command + shift + o`, then click `return` on your keyboard.

## `Cc` and `W`

When you are trying to local a specific word that must to totally identical to your input, you could use `Cc` and `W`. This would be useful when you are trying to replace certain word with another one.

`Cc` is matching with the same case. When this is clicked, “Ray” and “ray” is not the same word. Which means you cannot find “ray” by searching “Ray”.

`W` is searching the whole word. When this is clicked, you won’t find “Ray-is-smart” or “Rayisexcellent” by searching “Ray”.

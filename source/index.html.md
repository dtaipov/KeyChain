---
title: KeyChain Documentation

language_tabs: # must be one of https://git.io/vQNgJ

  - javascript

toc_footers:
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# KeyChain Documentation
## Introduction

**KeyChain** is a standalone app for signing transactions and generating key pairs. It stores private keys in an isolated environment where no logger, debugger or spyware can intercept them because of the [three-layer security](https://github.com/arrayio/array-io-keychain/wiki/KeyChain-security#three-security-layers-of-keychain) protecting each action of the system.
**KeyChain** supports transactions from and to various blockchains, including Ethereum and Ethereum classic, Litecoin, Bitcoin, Bitcoin Cash, and Bitshares. 

## Installation

Download and install KeyChain for [macOS](https://github.com/arrayio/array-io-keychain/releases/download/0.10/KeyChain.Installer.v0.10.zip). Windows and Linux installers are coming soon.

*Try out KeyChain on the [demo page](https://arrayio.github.io/array-io-keychain/demo/).*

After installation, connect to the demo-page: http://localhost:16384/ to check if the installation was successful and to test the KeyChain commands. In case everything went well, you will see the following page and you will be able to see responses to the commands in the "Response" box when you click on them.

![screenshot from 2018-12-10 15-57-27](https://user-images.githubusercontent.com/34011337/49734247-be211a80-fc94-11e8-8d85-c70b738ecae3.png)

If you are having trouble connecting to the page, [contact us](#contact) and we will do our best to help you.

You can find comprehensive installation guides for [macOS](https://github.com/arrayio/array-io-keychain/wiki/Installation-guide-for-macOS), [Windows](https://github.com/arrayio/array-io-keychain/wiki/Installation-guide-for-Windows), and [Linux](https://github.com/arrayio/array-io-keychain/wiki/Installation-guide-for-Linux) in our Wiki. 

## Getting started

> Require libraries setup

```javascript
const unsign = require('@warren-bank/ethereumjs-tx-unsign')
const ethUtil = require('ethereumjs-util');
const WebSocket = require('ws');
```
> Insert your own keyname

```javascript
const ws = new WebSocket('ws://localhost:16384/');
const keyname = 'test1@6de493f01bf590c0';
```
This is a simple example of how to integrate KeyChain through WebSocket.

```javascript
let fromAdd;
const send = (command) => {
  ws.send(JSON.stringify(command));
}

const getPublicKey = (keyname) => {
  return { 
    command: "public_key",
    params: {
      keyname: keyname
    }
  }
}

ws.onopen = async () => {
  send(getPublicKey(keyname));
}

ws.on('message', async (response) => {
  const data = JSON.parse(response);
  console.log('Public key: ', `0x${data.result}`)
  fromAdd = `0x${ethUtil.publicToAddress(`0x${data.result}`).toString('hex')}`;
  console.log(fromAdd);
});
```
```javascript
const ethHex = 'e315843b9aca0082520894e8899ba12578d60e4d0683a596edacbc85ec18cc6480038080';

const signHex = (keyname, hexraw) => {
  return {
    "command": "sign_hex",
    "params": {
      "transaction": hexraw,
      "blockchain_type": "ethereum",
      "keyname": keyname
    }
  }
}

ws.onopen = async () => {
  send(signHex(keyname, ethHex));
}

ws.on('message', async (response) => {
  const data = JSON.parse(response);
  console.log('Signature: ', data.result);
});


const resHex = 'f86315843b9aca0082520894e8899ba12578d60e4d0683a596edacbc85ec18cc64802aa0c3b68e20527f8f304801986720de1aeb9ab126c55570942420361cedeec1199ca03002d853eb2089e4e63848fcdb7af0fbc46e8f5c856ef0eb849a2270fd621bdb';
let { txData, signature } = unsign(resHex);
console.log('txData:',    txData,    "\n")
console.log('signature:', signature, "\n")
```

- First, you need to get the necessary libraries and state the localhost address that you will connect to through the WebSocket.



- Then you need to get a public key that you will need to sign a transaction and unsign it afterwards.



- Finally, you can sign an Ethereum transaction in hex format and unsign it, passing the result to KeyChain




<aside class="notice">
You must replace <code>test1@6de493f01bf590c0</code> with your personal keyname.
</aside>

## How to use 

For extensive documentation on KeyChain, refer to the [Wiki](https://github.com/arrayio/array-io-keychain/wiki).

There you will find:

- [Home](https://github.com/arrayio/array-io-keychain/wiki): how to navigate in our Wiki. 
- [How to sign an Ethereum transaction](https://github.com/arrayio/array-io-keychain/wiki/How-to-sign-Ethereum-transaction-via-KeyChain): a simple and precise tutorial.
- Installation guides for [macOS](https://github.com/arrayio/array-io-keychain/wiki/Installation-guide-for-macOS), [Windows](https://github.com/arrayio/array-io-keychain/wiki/Installation-guide-for-Windows), [Linux](https://github.com/arrayio/array-io-keychain/wiki/Installation-guide-for-Linux).
- [KeyChain Protocol](https://github.com/arrayio/array-io-keychain/wiki/KeyChain-Protocol): full comprehensive descriptions of the KeyChain commands.
- [KeyChain sample commands](https://github.com/arrayio/array-io-keychain/wiki/KeyChain-sample-commands): shortcut to using the commands.
- [Pipe API](https://github.com/arrayio/array-io-keychain/wiki/Pipe-API): integrating KeyChain through pipe.
- [Security](https://github.com/arrayio/array-io-keychain/wiki/Security): why KeyChain is highly secure.
- [Troubleshooting](https://github.com/arrayio/array-io-keychain/wiki/Troubleshooting): error handling, log files, debugging.
- [Useful reference](https://github.com/arrayio/array-io-keychain/wiki/Useful-reference): external links.
- [WebSocket API](https://github.com/arrayio/array-io-keychain/wiki/WebSocket-API): integrating KeyChain through WebSocket.

## Contact

If you need help interacting with KeyChain, please do not hesitate to contact us:

- [Twitter](https://twitter.com/ProjectArray)

- [Telegram](https://t.me/arrayio)

- [Stackoverflow](https://stackoverflow.com/users/10429540/array-io)

- Or you can write us an email to support@array.io. 

If you want to report a security issue, include the word "security" in the subject field.

We take security issues very seriously and we'll be looking forward to hearing from you. Still, we hope you enjoy using KeyChain and the integration goes smooth! 

## License

This project is licensed under the terms of the MIT license.


# Protocol
## Generate a key pair
### Command
create

> JSON Request

```json
{
  "command": "create",
  "params":
   {
      "keyname": "my key",
      "encrypted": true,
      "curve": "secp256k1",
      "cipher": "aes256"
  }
}
```

### Query parameters
**Parameter**|**Type**|**Description**|
---|---|---
encrypted|```string```|Requests encryption of the key.
curve|```string```|Keychain uses elliptic curve algorithm.
keyname|```string```|Create a name for your key. This will be a mnemonic label of the key - not the actual key.
cipher|```string```|Specifies encryption type (we use ```aes```) and the size of the key in bits. 

### Response format

When you pass a short key name in the create command, you get an exended name which consists of the prefix and first 8 bytes of the hash. After that, you will need to pass it as a value for the ```keyname``` parameter.

> Response example

```json
{"result":"my key@f9a1554e3f5e30c8"}
```
**Field name**|**Type**|**Description**
---|---|---
result|`string`|extended key name.

## Sign transaction in hex format

**NB:** You can sign transactions in hex or hash format. 
For example, for Ethereum transaction, you do not need to pass `chainid` as a parameter because it is incorporated in the hex. 

**NB:** Please remember that you need to **insert your own key name** in the parameters when copying the requests!
### Command
sign_hex

> JSON Request

```json
{
  "command": "sign_hex",
  "params":
  {
    "chainid": "de5f4d8974715e20f47c8bb609547c9e66b0b9e31d521199b3d8d6af6da74cb1",
    "transaction": "871689d060721b5cec5a010080841e00000000000011130065cd1d0000000000000000",
    "blockchain_type": "array",
    "keyname": "my key@f9a1554e3f5e30c8",
    "unlock_time": 45 
  }
}
```

### Query parameters
**Parameter**|**Type**|**Description**
---|---|---
chainid | ```string```|Optional parameter for Array and Bitshares-like blockchains. Chainid is the hash of a genesis file used to identify the chain.
transaction | ```string```|Hex representation of the transaction.
blockchain_type | ```string```|Inserts the name of blockchain you’re using. Possible options: Array, Bitcoin, Ethereum, Bitshares.
keyname | ```string```|Inserts the mnemonic label of your key.
unlock_time  | ```integer```|This parameter is experimental and optional! If this parameter is defined and if it is greater than zero, it unlocks the key for a set number of seconds. While the key is unlocked, the transactions will be signed without the user's approval.

### Response format

> Response example

```json
{"result":"6cc47faa3778d15efeb470cd4154fdceb80633beaed15f0816d93951ffd7629a5fae3fe83c030f5f8a0cea82c1907f85418b93e820ea3b043c116053afae20a300"}
```
**Field name**|**Type**|**Description**
---|---|---
result|`hex string`|65-byte singature in hex-string format.

## Sign hash of transaction

This request is suited best for advanced users who are eager to work on a low level. You should have knowledge of how to calculate hash of transaction, given the type of blockchain, and know the type of signature and its packaging.

**NB:** Please remember that you need to **insert your own key name** in the parameters when copying the requests!
### Command
sign_hash

> JSON Request

```json
{
  "command": "sign_hash",
  "params":
  {
    "sign_type": "VRS_canonical",
    "hash": "fe5e4a8974715e20f47c8bb609547c9e66b0b9e31d521199b3d8d6af6da74cb1",
    "keyname": "my key@f9a1554e3f5e30c8"
  }
}
```

### Query parameters
**Parameter**|**Type**|**Description**
---|---|---
sign-type |```string```|Customizes the way secp256 library is used by choosing one of its arguments through sign-type parameter. It has two possible value options: RSV_noncanonical and VRS_canonical. Default value is RSV_noncanonical. Prefix RSV/VRS means signature struct: [R, S, v] or [v, R, S].
hash |```string```|Hash calculated from the transaction. It can be the result of the first calculation or the second - depending on the type of the blockchain. For example, Bitcoin uses two calculations: to sign a bitcoin transaction you need to transmit the final (second) hash. Ethereum and Array make only one calculation to get the hash.
keyname|```string```|Inserts the mnemonic label of your key. 

### Response format

> Response example

```json 
{"result":"62826b9c7b6bbfcd89456c1e8068e141d6a46b2c1c0166ed25ba8ad6ede320f4454ff116d13f4e679e8224fcca49f49d50c279ed88513a1db7185946e26811ab01"}
```
**Field name**|**Type**|**Description** 
---|---|---
result|`hex string`|65-byte signature in hex format.

## List all key names

All full key names of the private keys that are kept on your computer.
### Command
list
> JSON Request

```json
{
  "command": "list"
}
```

### Query parameters
No

### Response format

> Response example

```json
{"result":"my key@47f926e22f376478","my key@e67871253c263de0","my key@e755d5b98b6ed747","my key@f9a1554e3f5e30c8"}
```
**Field name**|**Type**|**Description**
---|---|---
result|`JSON array of strings`|lists all your key names.

## Calculate public key from private key

**NB:** Please remember that you need to **insert your own key name** in the parameters when copying the requests!
### Command
public_key

> JSON Request

```json
{ 
  "command": "public_key",
  "params": 
  {
    "keyname": "my key@f9a1554e3f5e30c8"
  }
}
```

### Query parameters
**Parameter**|**Type**|**Description**
---|---|---
keyname|```string```|Inserts the name of the key from which you want to calculate a public key.

### Response format

64-byte public key. The length of the public key depends on the type of the blockchain. For example, in Ethereum, the length of a public key is 64 bytes.

> Response example

```json
{"result":"a7aea4bd112706655cb7014282d2a54658924e69c68f5a54f2cd5f35c6fcba9b610d6ae8549f960ae96e23ffc017f305c1d8664978c8ba8a1cc656fd9d068ef5"}
```

**Field name**|**Type**|**Description**
---|---|---
result|`hex string`| 64-byte public key.

## Lock all unlocked keys 
This command protects you from any hostile intervention into the KeyChain while you have left your computer without supervision.

### Command
lock

> JSON Request

```json
{
  "command": "lock"
}
```

### Query parameters
No
### Response format

> Response example

```json
{"result":"true"}
```
**Field name**|**Type**|**Description**
---|---|---
result|`bool`|bool result.

## Unlock private key

Unlock your key when you are ready to use it. 

**NB:** This parameter is experimental! Allowing the user to sign transactions without extra authorization is not secure and can be harmful. If `unlock_time` is greater than zero, you will see an insecure action warning. We do not recommend to use it. Although it might be needed if you want to speed up signing transactions.

**NB:** `Sign_hex` and `sign_hash` commands assume implicit unlocking of your keys. 

**NB:** Please remember that you need to **insert your own key name** in the parameters when copying the requests!

### Command
unlock 

> JSON Request

```json
{
  "command": "unlock"
  "params":
  {
    "keyname": "my key@be5f6e75878b84ba",
    "unlock_time": 45
  }
}
```
### Query parameters

**Parameter**|**Type**|**Description**
---|---|---
keyname|```string```|Inserts the name of the key you want to unlock.
unlock_time|```integer```|When this parameter is specified, it unlocks the key for a set number of seconds. While the key is unlocked, the pass entry window will not appear and the transactions will be signed without the user's approval.

### Response format

> Response example

```json
{"result":"true"}
```

**Field name**|**Type**|**Description**
---|---|---
result|`bool`|bool result.

## Get information about the current KeyChain version

You can request the details of the current KeyChain version you are using.

### Command
about

> JSON Request

```json
{"command":"about"}
```
### Query parameters
No

### Response format

> Response example

```json
{"result":{"version":"0.9.114","git_revision_sha":"59861769dca634d08d5442cb0074d40d8f544e66","git_revision_age":"9 minutes ago","compile_date":"compiled on Dec 12 2018 at 08:11:44","boost_version":"1.66","openssl_version":"OpenSSL 1.1.1  11 Sep 2018","build":"linux 64-bit"}}
```

**Field name**|**Type**|**Description**
---|---|---
result|`json object`|version details as a json object with the following parameters.
version|`string`|number of the current version.
git_revision_sha|`string`|hash of the commit.
git_revision_age|`string`|age of the commit.
compile_date|`string`|time of compilation.
boost_version|`string`|required version of the boost library.
openssl_version|`string`|required openssl version.
build|`string`|required operating system.

## Get the number of the current KeyChain version

You can request the number of the current version you are using.
### Command
version

> JSON Request

```json
{"command":"version"}
```

### Query parameters
No

### Response format

> Response example

```json
{"result":"0.9.114"}
```

**Field name**|**Type**|**Description**
---|---|---
result|`string`|current version number which has the form of "[major].[minor].[build number]".

# Installation guide for macOS

## System requirements

- macOS 10.12 or newer.

## Download macOS installer [here](https://github.com/arrayio/array-io-keychain/releases/download/0.10/KeyChain.Installer.v0.10.zip).

Follow the steps of the graphic installer. 

1. Click "next" to start installation.

<img width="562" alt="2018-12-13 19 42 06" src="https://user-images.githubusercontent.com/34011337/50005299-fbe6b180-ffba-11e8-97a9-0d8fc5145db8.png">

2. Accept the terms of the License Agreement.

<img width="562" alt="2018-12-13 19 42 08" src="https://user-images.githubusercontent.com/34011337/50005305-fee1a200-ffba-11e8-9373-409a58a8f8a6.png">

3. Choose a folder and click "next".

<img width="562" alt="2018-12-13 19 42 10" src="https://user-images.githubusercontent.com/34011337/50005310-030dbf80-ffbb-11e8-967c-d5eadf1cfec0.png">

4. Click "install" for installation to start.

<img width="562" alt="2018-12-13 19 42 13" src="https://user-images.githubusercontent.com/34011337/50005315-0608b000-ffbb-11e8-8dea-0fd9375b4aa7.png">

5. You will need to authorize the installation.

<img width="555" alt="mac-4" src="https://user-images.githubusercontent.com/34011337/49649378-0db7da00-fa3a-11e8-95b3-3ae9f8152204.png">

6. Wait until the setup is complete.

<img width="518" alt="2018-12-13 19 42 24" src="https://user-images.githubusercontent.com/34011337/50005317-086b0a00-ffbb-11e8-8d81-7ecebd54dabb.png">

7. Congratulations! You have installed KeyChain.

<img width="532" alt="mac-6" src="https://user-images.githubusercontent.com/34011337/49649380-0e507080-fa3a-11e8-8645-b86dbd139dbc.png">

## Check if the installation was successful

After installation, connect to the demo-page: http://localhost:16384/ to check if the installation was successful and to test the KeyChain commands. In case everything went well, you will see the following page and you will be able to see responses to the commands in the "Response" box when you click on them.

![screenshot from 2018-12-10 15-57-27](https://user-images.githubusercontent.com/34011337/49734247-be211a80-fc94-11e8-8d85-c70b738ecae3.png)

#How to sign an Ethereum transaction with KeyChain


Here you can find an instruction on how to sign an Ethereum transaction with KeyChain.

### Demo page

[Here](https://arrayio.github.io/array-io-keychain/eth_signer/) you can try out signing Ethereum transactions with KeyChain.

### 1. Download and install KeyChain for [macOS](https://github.com/arrayio/array-io-keychain/releases/download/0.10/KeyChain.Installer.v0.10.zip)

### 2. Generate the key 

Start with the command `wscat -c ws://localhost:16384/`
and send the `create` command to generate the key

```json
{
  "command": "create",
  "params":
   {
      "keyname": "my key",
      "encrypted": true,
      "curve": "secp256k1",
      "cipher": "aes256"
  }
}
```
You will get an **extended name** of the key which consists of the name you have given it as a prefix and the first 8 bytes of the hash. Note that from now on you should insert the extended key! 

### NB: 

**Insert your own extended name of the key!** Do not copy the following code together with the key name that we use because it is specified here only as an example of how your key name might look like. **Use your own key name!**

Then request a public key via

```json
{ 
  "command": "public_key",
  "params": 
  {
    "keyname": "my key@f9a1554e3f5e30c8"
  }
}
```

### 3. Calculate the address from the publicKey

```javascript
const ethUtil = require('ethereumjs-util');
const publicKey = 'YOUR_PUBLIC_KEY';
const fromAdd = ethUtil.publicToAddress(publicKey).toString('hex');
```

### 4. Transfer money to the address corresponding to the public key

In case you work with ropsten - 
https://faucet.ropsten.be/

### 5. Check the balance on the address - it should have enough ether for a successful transfer.

```javascript
web3.eth.getBalance(fromAdd) 
.then(console.log);

```

### 6. Sign transaction with the key that you have generated

You can find an example of the code [here](https://gist.github.com/cypherpunk99/3e1314f8cc62cd675fa5c8f7bbe97923)

### 7. [Check Etherscan](https://ropsten.etherscan.io/address/0x1ba05dad1abe91fdea3afffe9676b59076ce0ece)

![screen shot 2018-11-30 at 19 19 52](https://user-images.githubusercontent.com/34011337/49658738-d35b3680-fa53-11e8-9d49-7739492ecab7.png)

# WebSocket API

**KeyChain** contains a WebSocket API. This API can be used to stream information from a KeyChain instance to any client that implements WebSockets. Implementations in different languages:

- NodeJS
- Python
- JavaScript/HTML
- etc

Install KeyChain for [macOS](https://github.com/arrayio/array-io-keychain/releases/download/0.10/KeyChain.Installer.v0.10.zip), [Windows](https://github.com/arrayio/array-io-keychain/wiki/Installation-guide-for-Windows), [Linux](https://github.com/arrayio/array-io-keychain/wiki/Installation-guide-for-Linux) and connect to the demo page here http://localhost:16384/.

## Demo pages in JavaScript

### For testing the KeyChain commands

- On this [demo page](https://arrayio.github.io/array-io-keychain/demo/) you can test all the KeyChain commands.

- [Here](https://github.com/arrayio/array-io-keychain/tree/master/docs/demo) you will find the **code** of the KeyChain demo page. It will be automatically installed together with the KeyChain.

### For signing transactions

- [Here](https://arrayio.github.io/array-io-keychain/eth_signer/) you can try out signing Ethereum transactions with KeyChain.

- You can find an example of the code [here](https://gist.github.com/cypherpunk99/3e1314f8cc62cd675fa5c8f7bbe97923).

## Message format

> For example, here is a request for generating keys: 

```json
{
  "command": "create",
  "params":
   {
      "keyname": "my key",
      "encrypted": true,
      "curve": "secp256k1",
      "cipher": "aes256"
  }
}
```

Each WebSocket API message is a json serialized object containing a command with the corresponding parameters.

For full comprehensive descriptions of the commands, acceptable parameters and values, go to the [Protocol](https://github.com/arrayio/array-io-keychain/wiki/KeyChain-Protocol). 

## WebSocket integration guide

### NodeJS integration example

The following will show the usage of websocket connections. We make use of the `wscat` application available via npm:

```
$ npm install -g wscat
```

A simple call would take the form:

```
$ wscat -c ws://127.0.0.1:16384
> { "command": "list"}
```

#### Successful Calls
The API will return a properly JSON formated response carrying the same id as the request to distinguish subsequent calls.

```json
{"result":  ..data..}
```

#### Errors

In case of an error, the resulting answer will carry an error attribute and a description in human-readable format:

```json
{"error":"Error: keyfile could not found by keyname"}
```

### JavaScript integration example

Before you proceed with the integration, you need to install KeyChain for [macOS](https://github.com/arrayio/array-io-keychain/releases/download/0.10/KeyChain.Installer.v0.10.zip), [Windows](https://github.com/arrayio/array-io-keychain/wiki/Installation-guide-for-Windows), [Linux](https://github.com/arrayio/array-io-keychain/wiki/Installation-guide-for-Linux).

#### Test in the web

After installing KeyChain, open the browser and connect to the demo page via http://localhost:16384/. 

When the connection is established, you will see the following KeyChain page. If you click on one of the commands on the left panel, you will see its json request and response in the white boxes below:

![screenshot from 2018-12-10 15-57-27](https://user-images.githubusercontent.com/34011337/49734247-be211a80-fc94-11e8-8d85-c70b738ecae3.png)

#### Test from a terminal application:

You can see if the installation was successful by going to the terminal app, opening KeyChain and trying one of the commands that you can take from the [Protocol](https://github.com/arrayio/array-io-keychain/wiki/KeyChain-Protocol):

```
c:\Users\User-1>cd "C: Program Files\WsKeychain"
c:\Program Files\WSKeychain>keychain 
{"command":"list"}
```
A successful response will take the following format:

#### Response format

> Response example

```json
{"result":"my key@47f926e22f376478","my key@e67871253c263de0","my key@e755d5b98b6ed747","my key@f9a1554e3f5e30c8"}
```

**Field name**|**Type**|**Description**|**Value example**
---|---|---|---
result|`JSON array of strings`|list of key names|```"my key@47f926e22f376478","my key@e67871253c263de0","my key@e755d5b98b6ed747","my key@f9a1554e3f5e30c8"```


### Build a page that connects to WebSocket

[Here](https://github.com/arrayio/array-io-keychain/blob/master/docs/demo/index.html) you will find an example of how to build a web-page that connects to WebSocket.

Save this to the folder  where you will be running the `websocket` command.

Open this page via localhost: http://localhost:16384/

----
You can find the code for the demo page and the page itself up in the [Demo pages in JavaScript](#demo-pages-in-javascript) section.


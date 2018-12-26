

---
title: KeyChain Documentation

language_tabs: # must be one of https://git.io/vQNgJ

  - json
  - javascript
  
toc_footers:
  - <a href='http://keychain.array.io'>KeyChain official website</a>

includes:
  - errors

search: true
---

# KeyChain Documentation
## Introduction

**KeyChain** is a standalone app for signing transactions and generating key pairs. It stores private keys in an isolated environment where no logger, debugger or spyware can intercept them because of the [three-layer security](#three-security-layers-of-keychain) protecting each action of the system.
**KeyChain** supports transactions from and to various blockchains, including Ethereum and Ethereum classic, Litecoin, Bitcoin, Bitcoin Cash, and Bitshares. 

## Installation

Download and install KeyChain for [macOS](https://github.com/arrayio/array-io-keychain/releases/download/0.11/KeyChain.Installer.v0.11.zip). Windows and Linux installers are coming soon.

*Try out KeyChain on the [demo page](https://arrayio.github.io/array-io-keychain/demo/).*

After installation, connect to the demo-page: [http://localhost:16384/](http://localhost:16384/) to check if the installation was successful and to test the KeyChain commands. In case everything went well, you will see the following page and you will be able to see responses to the commands in the "Response" box when you click on them.

![screenshot from 2018-12-10 15-57-27](https://user-images.githubusercontent.com/34011337/49734247-be211a80-fc94-11e8-8d85-c70b738ecae3.png)

If you are having trouble connecting to the page, [contact us](#contact) and we will do our best to help you.

Below you can find comprehensive installation guides for [macOS](#macos), [Windows](#windows), [Linux](#linux).

## Getting started

> Require libraries setup

```json
//go to javascript
```

```javascript
const unsign = require('@warren-bank/ethereumjs-tx-unsign')
const ethUtil = require('ethereumjs-util');
const WebSocket = require('ws');
```

> Insert your own `keyname`

```json
//go to javascript
```

```javascript
const ws = new WebSocket('ws://localhost:16384/');
const keyname = 'test1@6de493f01bf590c0';
```

This is a simple example of how to integrate KeyChain through WebSocket.

> First, we wrap the `public_key` command into a function to send it through a websocket.

```json
//go to javascript
```

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
```

> When the websocket is open, we send the `public_key` command to the KeyChain

```json
//go to javascript
```

```javascript
ws.onopen = () => {
  send(getPublicKey(keyname));
}
```

> Next, we display the public key and calculate the address from it.

```json
//go to javascript
```

```javascript
ws.on('message', (response) => {
  const data = JSON.parse(response);
  console.log('Public key: ', `0x${data.result}`)
  fromAdd = `0x${ethUtil.publicToAddress(`0x${data.result}`).toString('hex')}`;
  console.log(fromAdd);
});
```

> Then we sign a raw transaction in hex form via KeyChain

```json
//go to javascript
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

ws.on('open') = () => {
  send(signHex(keyname, ethHex));
}

ws.on('message', (response) => {
  const signature = JSON.parse(response).result;
// For eth signature contains those 3 parameters: r, s, v 
  const r = signature.slice(0, 64);
  const s = signature.slice(64, 128);
  const v = signature.slice(128, 130);
  console.log(`Signature: \nr: ${r}\ns: ${s}\nv: ${v}\n`);
});
```

> Finally, we use an Ethereumjs-tx library to unsign the transaction and check the result against the one we got from the KeyChain.

```json
//go to javascript
```

```javascript

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


## Contact

If you have questions or enquiries about KeyChain, please do not hesitate to contact us:

- [Twitter](https://twitter.com/ProjectArray)

- [Telegram](https://t.me/arrayio)

- [Stackoverflow](https://stackoverflow.com/users/10429540/array-io)

- Or you can write us an email to support@array.io. 

If you want to report a security issue, include the word "security" in the subject field.

We take security issues very seriously and we'll be looking forward to hearing from you. We hope you enjoy using KeyChain! 

## License

MIT License

Copyright (c) 2018 KeyChain

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.


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
```javascript
//go to json
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
{
  "result":"my key@f9a1554e3f5e30c8"
}
```
```javascript
//go to json
```
**Field name**|**Type**|**Description**
---|---|---
result|`string`|extended key name.

## Sign transaction in hex format

<aside class="notice">
You can sign transactions in hex or hash format. 
For example, for Ethereum transaction, you do not need to pass <code>chainid</code> as a parameter because it is incorporated in the hex. 
</aside>

<aside class="notice">
Please remember that you need to insert your own <code>key name</code> in the parameters when copying the requests!
</aside>

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
```javascript
//go to json
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
{
  "result":"6cc47faa3778d15efeb470cd4154fdceb80633beaed15f0816d93951ffd7629a5fae3fe83c030f5f8a0cea82c1907f85418b93e820ea3b043c116053afae20a300"
}
```
```javascript
//go to json
```
**Field name**|**Type**|**Description**
---|---|---
result|`hex string`|65-byte signature in hex-string format.

## Sign hash of transaction

This request is suited best for advanced users who are eager to work on a low level. You should have knowledge of how to calculate hash of transaction, given the type of blockchain, and know the type of signature and its packaging.

<aside class="notice">
Please remember that you need to insert your own <code>key name</code> in the parameters when copying the requests!
</aside>

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
```javascript
//go to json
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
{
  "result":"62826b9c7b6bbfcd89456c1e8068e141d6a46b2c1c0166ed25ba8ad6ede320f4454ff116d13f4e679e8224fcca49f49d50c279ed88513a1db7185946e26811ab01"
}
```
```javascript
//go to json
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
```javascript
//go to json
```
### Query parameters
No

### Response format

> Response example

```json
  {"result":"my key@47f926e22f376478","my key@e67871253c263de0","my key@e755d5b98b6ed747","my key@f9a1554e3f5e30c8"}
```
```javascript
//go to json
```
**Field name**|**Type**|**Description**
---|---|---
result|`JSON array of strings`|lists all your key names.

## Calculate the public key 

<aside class="notice">
Please remember that you need to insert your own <code>key name</code> in the parameters when copying the requests!
</aside>

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
```javascript
//go to json
```
### Query parameters
**Parameter**|**Type**|**Description**
---|---|---
keyname|```string```|Inserts the name of the key from which you want to calculate a public key.

### Response format

64-byte public key. The length of the public key depends on the type of the blockchain. For example, in Ethereum, the length of a public key is 64 bytes.

> Response example

```json
{
  "result":"a7aea4bd112706655cb7014282d2a54658924e69c68f5a54f2cd5f35c6fcba9b610d6ae8549f960ae96e23ffc017f305c1d8664978c8ba8a1cc656fd9d068ef5"
}
```
```javascript
//go to json
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
```javascript
//go to json
```
### Query parameters
No
### Response format

> Response example

```json
{
  "result":"true"
}
```
```javascript
//go to json
```
**Field name**|**Type**|**Description**
---|---|---
result|`bool`|bool result.

## Unlock private key

Unlock your key when you are ready to use it. 

<aside class="notice">
This parameter is experimental! Allowing the user to sign transactions without extra authorization is not secure and can be harmful. If <code>unlock_time</code> is greater than zero, you will see an insecure action warning. We do not recommend to use it. Although it might be needed if you want to speed up signing transactions.
</aside>

<aside class="notice">
<code>Sign_hex</code> and <code>sign_hash</code> commands assume implicit unlocking of your keys. 
</aside>

<aside class="notice">
Please remember that you need to insert your own <code>key name</code> in the parameters when copying the requests!
</aside>


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
```javascript
//go to json
```
### Query parameters

**Parameter**|**Type**|**Description**
---|---|---
keyname|```string```|Inserts the name of the key you want to unlock.
unlock_time|```integer```|When this parameter is specified, it unlocks the key for a set number of seconds. While the key is unlocked, the pass entry window will not appear and the transactions will be signed without the user's approval.

### Response format

> Response example

```json
{
  "result":"true"
}
```
```javascript
//go to json
```
**Field name**|**Type**|**Description**
---|---|---
result|`bool`|bool result.

## KeyChain version details

You can request the details of the current KeyChain version you are using.

### Command
about

> JSON Request

```json
{
  "command":"about"
}
```
```javascript
//go to json
```
### Query parameters
No

### Response format

> Response example

```json
{
  "result":
  {
    "version":"0.9.114","git_revision_sha":"59861769dca634d08d5442cb0074d40d8f544e66","git_revision_age":"9 minutes ago","compile_date":"compiled on Dec 12 2018 at 08:11:44","boost_version":"1.66","openssl_version":"OpenSSL 1.1.1  11 Sep 2018","build":"linux 64-bit"
  }
}
```
```javascript
//go to json
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

## KeyChain version number

You can request the number of the current version you are using.

### Command
version

> JSON Request

```json
{
  "command":"version"
}
```
```javascript
//go to json
```
### Query parameters
No

### Response format

> Response example

```json
{
  "result":"0.9.114"
}
```
```javascript
//go to json
```
**Field name**|**Type**|**Description**
---|---|---
result|`string`|current version number which has the form of "[major].[minor].[build number]".

# Installation guides

## macOS

### System requirements

- macOS [10.12](#http://support.apple.com/downloads/DL1917/en_US/macosupd10.12.5.dmg) or [newer](https://support.apple.com/downloads/macos).

### How to install 

Download [KeyChain](https://github.com/arrayio/array-io-keychain/releases/download/0.11/KeyChain.Installer.v0.11.zip) and follow the steps of the graphic installer. 


1. Click "next" to start installation. <button class="show">show</button>

<img width="562" alt="2018-12-13 19 42 06" src="https://user-images.githubusercontent.com/34011337/50005299-fbe6b180-ffba-11e8-97a9-0d8fc5145db8.png">

2. Accept the terms of the License Agreement.<button class="show">show</button>

<img width="562" alt="2018-12-13 19 42 08" src="https://user-images.githubusercontent.com/34011337/50005305-fee1a200-ffba-11e8-9373-409a58a8f8a6.png">

3. Choose a folder and click "next".<button class="show">show</button>

<img width="562" alt="2018-12-13 19 42 10" src="https://user-images.githubusercontent.com/34011337/50005310-030dbf80-ffbb-11e8-967c-d5eadf1cfec0.png">

4. Click "install" for installation to start.<button class="show">show</button>

<img width="562" alt="2018-12-13 19 42 13" src="https://user-images.githubusercontent.com/34011337/50005315-0608b000-ffbb-11e8-8dea-0fd9375b4aa7.png">

5. You will need to authorize the installation.<button class="show">show</button>

<img width="555" alt="mac-4" src="https://user-images.githubusercontent.com/34011337/49649378-0db7da00-fa3a-11e8-95b3-3ae9f8152204.png">

6. Wait until the setup is complete.<button class="show">show</button>

<img width="518" alt="2018-12-13 19 42 24" src="https://user-images.githubusercontent.com/34011337/50005317-086b0a00-ffbb-11e8-8d81-7ecebd54dabb.png">
  
7. Congratulations! You have installed KeyChain.<button class="show">show</button>

<img width="532" alt="mac-6" src="https://user-images.githubusercontent.com/34011337/49649380-0e507080-fa3a-11e8-8645-b86dbd139dbc.png">

<script
  src="https://code.jquery.com/jquery-3.3.1.min.js"
  integrity="sha256-FgpCb/KJQlLNfOu91ta32o/NMZxltwRo8QtmkMRdAu8="
  crossorigin="anonymous"></script>

<script>

  var button = $(".show");
  var image = $("img");
  image.hide();
  button.on("click", function(){
    $(this).next(image).show();
  })
</script>

### Check if KeyChain is installed

After installation, connect to the demo-page: [http://localhost:16384/](http://localhost:16384/) to check if the installation was successful and to test the KeyChain commands. In case everything went well, you will see the following page and you will be able to see responses to the commands in the "Response" box when you click on them.

![screenshot from 2018-12-10 15-57-27](https://user-images.githubusercontent.com/34011337/49734247-be211a80-fc94-11e8-8d85-c70b738ecae3.png)

## Windows
Windows installer will be accessible at v.1.0. 

We are passionate about KeyChain and seek to make it as soon as possible, so that you could enjoy its wonderful features on any operating system you like. 

### System requirements

- Windows 7 or newer.

### How to install

Download KeyChain and follow the steps of the graphic installer. 

1 Click "next" to prepare installation <button id="loadImage10" data-image="https://user-images.githubusercontent.com/34011337/49864523-b76ce180-fe13-11e8-8fa9-7e1421295bd9.png">View</button>


<img id='image10' class='hide' width="" alt="" src="">

<script>
  $("#image10").hide();
  $("#loadImage10").on('click', function(){
  $("#image10").show();
  $("#image10").attr("src", $("#loadImage10").data('image10'));
});
</script>


2 Accept the terms of the License and click "next" <button id="loadImage10" data-image="https://user-images.githubusercontent.com/34011337/49864529-b89e0e80-fe13-11e8-814d-71b2723a41d0.png">View</button>


<img id='image10' class='hide' width="" alt="" src="">

<script>
  $("#image10").hide();
  $("#loadImage10").on('click', function(){
  $("#image10").show();
  $("#image10").attr("src", $("#loadImage10").data('image10'));
});
</script>

3 Choose a folder, click "next" <button id="loadImage10" data-image="https://user-images.githubusercontent.com/34011337/49864528-b89e0e80-fe13-11e8-8671-11845874f077.png">View</button>


<img id='image10' class='hide' width="" alt="" src="">

<script>
  $("#image10").hide();
  $("#loadImage10").on('click', function(){
  $("#image10").show();
  $("#image10").attr("src", $("#loadImage10").data('image10'));
});
</script>

4 Click "install" for installation to start <button id="loadImage10" data-image="https://user-images.githubusercontent.com/34011337/49864527-b8057800-fe13-11e8-910c-914dbcf6d8b3.png">View</button>


<img id='image10' class='hide' width="" alt="" src="">

<script>
  $("#image10").hide();
  $("#loadImage10").on('click', function(){
  $("#image10").show();
  $("#image10").attr("src", $("#loadImage10").data('image10'));
});
</script>

5 Wait until the setup is complete <button id="loadImage10" data-image="https://user-images.githubusercontent.com/34011337/49864526-b8057800-fe13-11e8-88cf-64f722b9b277.png">View</button>


<img id='image10' class='hide' width="" alt="" src="">

<script>
  $("#image10").hide();
  $("#loadImage10").on('click', function(){
  $("#image10").show();
  $("#image10").attr("src", $("#loadImage10").data('image10'));
});
</script>

6 Congratulaions! You have installed KeyChain. <button id="loadImage10" data-image="https://user-images.githubusercontent.com/34011337/49864525-b76ce180-fe13-11e8-9034-c6aaec14fcf4.png">View</button>


<img id='image10' class='hide' width="" alt="" src="">

<script>
  $("#image10").hide();
  $("#loadImage10").on('click', function(){
  $("#image10").show();
  $("#image10").attr("src", $("#loadImage10").data('image10'));
});
</script>

### Check if KeyChain is installed

After installation, connect to the demo-page: [http://localhost:16384/](http://localhost:16384/) to check if the installation was successful and to test the KeyChain commands. In case everything went well, you will see the following page and you will be able to see responses to the commands in the "Response" box when you click on them.

![screenshot from 2018-12-10 15-57-27](https://user-images.githubusercontent.com/34011337/49734247-be211a80-fc94-11e8-8d85-c70b738ecae3.png)

## Linux 

Windows installer will be accessible at v.1.0.

We are passionate about KeyChain and seek to make it as soon as possible, so that you could enjoy its wonderful features on any operating system you like.

### System requirements

- Ubuntu 16.04 or newer

- Debian 9 or newer

- Linux Mint 18.3 or newer

### How to install

Coming soon

# Sign an Ethereum transaction

Here you can find an instruction on how to sign an Ethereum transaction with KeyChain.

On this [demo page](https://arrayio.github.io/array-io-keychain/eth_signer/) you can try out signing Ethereum transactions with KeyChain.

## Step-by-step guide

### 1. Install KeyChain for macOS

[Download KeyChain installer](https://github.com/arrayio/array-io-keychain/releases/download/0.11/KeyChain.Installer.v0.11.zip).

### 2. Generate the key

Start with the command `wscat -c ws://localhost:16384/`
and send the `create` command to generate the key.

> Create a key pair

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
```javascript
//go to json
```
You will get an **extended name** of the key which consists of the name you have given it as a prefix and the first 8 bytes of the hash. Note that from now on you should insert the extended key! 

<aside class="notice">
Insert your own extended name of the key! Do not copy the following code together with the key name that we use because it is specified here only as an example of how your key name might look like. 
</aside>
 
> Then request a public key via

```json
{ 
  "command": "public_key",
  "params": 
  {
    "keyname": "my key@f9a1554e3f5e30c8"
  }
}
```
```javascript
//go to json
```

### 3. Get the address 

The address can be calculated from the public key.

> Get address from public key

```json
//go to javascript
```
```javascript
const ethUtil = require('ethereumjs-util');
const publicKey = 'YOUR_PUBLIC_KEY';
const fromAdd = ethUtil.publicToAddress(publicKey).toString('hex');
```

### 4. Make a transaction 

Now you can transfer some money to the address corresponding to the public key.

In case you work with ropsten - 
https://faucet.ropsten.be/

### 5. Check the balance 

Check the balance of the address - it should have enough ether for a successful transfer.

> Check balance

```json
//go to javascript
```
```javascript
web3.eth.getBalance(fromAdd) 
.then(console.log);
```

### 6. Sign transaction 

You can now use the key that you have generated to sign a transaction.

You can find an example of the code [here](https://gist.github.com/cypherpunk99/3e1314f8cc62cd675fa5c8f7bbe97923).

###  7. Check Etherscan

[Etherscan](https://ropsten.etherscan.io/address/0x1ba05dad1abe91fdea3afffe9676b59076ce0ece)

![screen shot 2018-11-30 at 19 19 52](https://user-images.githubusercontent.com/34011337/49658738-d35b3680-fa53-11e8-9d49-7739492ecab7.png)

# WebSocket API

**KeyChain** contains a WebSocket API. This API can be used to stream information from a KeyChain instance to any client that implements WebSockets. Implementations in different languages:

- NodeJS
- Python
- JavaScript/HTML
- etc

Install KeyChain for [macOS](https://github.com/arrayio/array-io-keychain/releases/download/0.11/KeyChain.Installer.v0.11.zip), [Windows](#windows), [Linux](#linux) and connect to the demo page via [http://localhost:16384/](http://localhost:16384/).

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

```javascript
//go to json
```

Each WebSocket API message is a json serialized object containing a command with the corresponding parameters.

For full comprehensive descriptions of the commands, acceptable parameters and values, go to the [Protocol](#generate-a-key-pair). 

## WebSocket integration guide

### NodeJS integration example

The following will show the usage of websocket connections. 

> We make use of the `wscat` application available via npm:

```json
//go to javascript
```

```javascript
$ npm install -g wscat
```

> A simple call would take the form:

```json
//go to javascript
```

```javascript
$ wscat -c ws://127.0.0.1:16384
> { "command": "list"}
```

#### Successful Calls
The API will return a properly JSON formated response carrying the same id as the request to distinguish subsequent calls.

```json
{"result":  ..data..}
```

```javascript
//go to json
```

#### Error

In case of an error, the resulting answer will carry an error attribute and a description in human-readable format:

```json
{"error":"Error: keyfile could not found by keyname"}
```

```javascript
//go to json
```

### JavaScript integration example

Before you proceed with the integration, you need to install KeyChain for [macOS](https://github.com/arrayio/array-io-keychain/releases/download/0.11/KeyChain.Installer.v0.11.zip), [Windows](#windows), [Linux](#linux).

#### Test in the web

After installing KeyChain, open the browser and connect to the demo page via [http://localhost:16384/](http://localhost:16384/). 

When the connection is established, you will see the following KeyChain page. If you click on one of the commands on the left panel, you will see its json request and response in the white boxes below:

![screenshot from 2018-12-10 15-57-27](https://user-images.githubusercontent.com/34011337/49734247-be211a80-fc94-11e8-8d85-c70b738ecae3.png)

#### Test from a terminal application:

You can see if the installation was successful by going to the terminal app, opening KeyChain and trying one of the commands that you can take from the [Protocol](#generate-a-key-pair):


c:\Users\User-1>cd "C: Program Files\WsKeychain"
c:\Program Files\WSKeychain>keychain 
{"command":"list"}

A successful response will take the following format:

#### Response format

> Response example

```json
{"result":"my key@47f926e22f376478","my key@e67871253c263de0","my key@e755d5b98b6ed747","my key@f9a1554e3f5e30c8"}
```

```javascript
//go to json
```

**Field name**|**Type**|**Description**|**Value example**
---|---|---|---
result|`JSON array of strings`|list of key names|```"my key@47f926e22f376478","my key@e67871253c263de0","my key@e755d5b98b6ed747","my key@f9a1554e3f5e30c8"```


### Build a page that connects to WebSocket

[Here](https://github.com/arrayio/array-io-keychain/blob/master/docs/demo/index.html) you will find an example of how to build a web-page that connects to WebSocket.

Save this to the folder  where you will be running the `websocket` command.

Open this page via localhost: [http://localhost:16384/](http://localhost:16384/)

You can find the code for the demo page and the page itself up in the [Demo pages in JavaScript](#demo-pages-in-javascript) section.

# Pipe API

**KeyChain** has an I/O stream (pipe) integration option. Interaction with **KeyChain** through pipes requires a terminal application as an input module that sends requests (STDIN) to the security layer that shows you the details of your request. When you approve the request through an interative gialogue window, the request goes back through the signing module and gives you an answer (STDOUT). 

For detailed description of the process, see [Three security layers](#three-security-layers-of-keychain) section.

## Message format

Each Pipe API message is a json serialized object containing a command with the corresponding parameters.

For full comprehensive descriptions of the commands, acceptable parameters and values, go to the [Protocol](#generate-a-key-pair).

## Pipe integration guide

Before you proceed with the integration, you need to install KeyChain for [macOS](https://github.com/arrayio/array-io-keychain/releases/download/0.11/KeyChain.Installer.v0.11.zip), [Windows](#windows), [Linux](#linux).

When the installation is complete, you can open stream input to start sending json requests through STDIN - STDOUT pipes.
 
> Here you will find an example of KeyChain integration using Node.js

```json
//go to javascript
```

```javascript
const { spawn } = require('child_process');
const path = require('path');
const keychain = spawn(path.join(__dirname, 'keychain'));

keychain.stdout.on('data', (data) => {
  console.log(`keychain data: ${data.toString()}`);
});

keychain.stderr.on('data', (data) => {
  console.log(`keychain stderr: ${data}`);
});

keychain.on('close', (code) => {
  if (code !== 0) {
    console.log(`keychain process exited with code ${code}`);
  }
});
```

### See all your keys

```json
//go to javascript
```

```javascript 
const listCommand = { command: "list" };
keychain.stdin.write(JSON.stringify(listCommand));
```

### Sign transaction

To sign a transaction, first you need to set the timeout value for your key. While it is unlocked, you can sign the transaction without entering a passphrase during this visit. Upon finishing the transaction, lock your keys.

```json
//go to javascript
```

```javascript 
const unlockCommandTime = {
  "command": "set_unlock_time", 
  "params": 
  {
    "seconds": 10000
  }
}; 
keychain.stdin.write(JSON.stringify(unlockCommandTime));

const unlockCommand = {  
  "command": "unlock", 
  "params": 
  { 
    "keyname": "test1@d073edf782dec758"
  }
};
keychain.stdin.write(JSON.stringify(unlockCommand));

const signCommand = {
  "command": "sign_hex",
  "params":
  {
    "chainid": "de5f4d8974715e20f47c8bb609547c9e66b0b9e31d521199b3d8d6af6da74cb1",
    "transaction": "871689d060721b5cec5a010080841e00000000000011130065cd1d0000000000000000",
    "blockchain_type": "array",
    "keyname": "test1@d073edf782dec758"
  }
};

keychain.stdin.write(JSON.stringify(signCommand));

const lockCommand = {  
  "command": "lock"
};
keychain.stdin.write(JSON.stringify(lockCommand));

keychain.stdin.write(JSON.stringify(signCommand));
``` 

For descriptions of all the commands and parameters, go to the [Protocol](#generate-a-key-pair).

# KeyChain security

## About KeyChain security

Choosing a means of storing keys is an important and responsible task that everybody needs to address when considering making **transactions** - irrespective of the type of  blockchain they use. That is why here we show you the **detailed structure** of our KeyChain - so that your choice of key storage is an informed decision based on real knowledge of what stands behind the system.

## How KeyChain ensures safe key storage

KeyChain encrypts private keys using AES256 algorithm and stores the keys in an isolated environment that is protected with [three security layers](#three-security-layers-of-keychain). 

**AES256** was first adopted by the U.S. government and now is used worldwide as a secure and reliable way of protecting information. It stands for **Advanced Encryption Standard** which handles **256-bit keys**. This is a symmetric encryption algorithm that creates an output (ciphertext) from the input (plaintext) in 14 rounds which involve several steps of encryption. These steps combine the procedures of other symmetric encryption algorithms: substitution cipher with a reference table, adding round key,  shifting rows, and mixing columns - all performed multiple times. 

## OS-specific KeyChain security

### Unix-like operating systems

For Linux, we use a unique mechanism created by our team.

Typically, Linux offers the following algorithm of interacting with the user:


**1)** Client app = > connects => to X-server

**2)** Client app => sends some X-proto specified commands => X-server receives the commands => X-server renders an interface window

**3)** User enters a passphrase into the window => X-server catch the passphrase from the user => sends X-proto commands to the client => client app processes the passphrase from the user


However, around 1984, at the time when X11 was created, there existed no such task as performing secure operations via the Internet. The developers of X11 did not set out to protect the user’s data from someone capturing it. Even now, there is still no real mechanism against this kind of attack.
To solve the problem of protecting the data, we have decided to look past the standard solutions. Instead of receiving a passphrase from a user through the X-server, we have chosen to receive the passphrase from the keyboard driver. This serves as a shortcut that allows KeyChain to work without connecting to the X-server, thus minimizing the risk of someone stealing the passphrase.

Therefore, now instead of the following sequence…


- The user enters the passphrase into the window => X-server catches the passphrase => it sends the passphrase through X-proto => Client app processes the request


… we have:


- The user enters the passphrase => KeyChain catches the keyboard’s events directly


The shorter the path, the fewer weak points can be found. We exclude the weakest link (X-server) from the process of entering the passphrase. Thus, for the third party to compromise the passphrase, they will need to intercept it right at the keyboard level, which requires to have root access and hence makes it almost impossible.

You might be asking yourself: if KeyChain functions without connecting to the X-server, why does the user see the dialogue window?

The answer is simple and is motivated by our concern with the user experience. Working through the command line is rather inconvenient for most people. That is why we use an emulator program that imitates the process of inputting the passphrase by receiving events from the KeyChain daemon. Note that no secret data goes into the emulator – like a mirror that only reflects light without absorbing any of it. This allows KeyChain to minimize the risks to the minimum.

### Windows

Users of **Windows 10 Enterprise Edition** can benefit from a new security feature - **Isolated User Mode (IUM)**. It employs a set of modes called **Virtual Trusted Levels (VTL)** to run processes separately, without accessing each other’s memory. 
We launch KeyChain on VTL1 (SecureMode). Any malware that is launched on VTL0 (NormalMode) does not have access to KeyChain. The mechanism of isolating the kernels is executed as a Windows OS process. Learn more about IUM processes [here](https://docs.microsoft.com/en-us/windows/desktop/procthread/isolated-user-mode--ium--processes).

To ensure secure passphrase entry, **Windows Vista/7/8** and **Windows 10 (not Enterprise Edition)** use a mechanism similar to the one used for **User Access Control (UAC)**. In particular, UAC is used at a program setup to avoid giving a malware access to the system context.

KeyChain gets access to the system environment when it is being installed. 
A malware can only access the user context (unless it is installed in the system as a service). The processes that are launched in the user context do not have access to the applications that are launched in the isolated context.Hence, a malware cannot get access to KeyChain data because of the mechanism of separating the access between the levels of the OS. For more information, please refer to [Microsoft documentation](https://docs.microsoft.com/en-us/windows/security/identity-protection/user-account-control/how-user-account-control-works).

### macOS

macOS has in innate security mechanism that does not allow any other program to interfere with a process if it was not the one that started it. Therefore, all we needed to do was to take care of the intermediary step that takes place at the passphrase entry on the keyboard. That is why we incorporated the `EnableSecureEventInput` function that provides a means for a process to protect sensitive data from being intercepted by other processes. Learn more on the [Apple developers portal](https://developer.apple.com/library/archive/technotes/tn2150/_index.html#//apple_ref/doc/uid/DTS10004249).

## Three security layers of KeyChain

![diagram keychain fin 1](https://user-images.githubusercontent.com/34011337/49658582-719acc80-fa53-11e8-97bc-e957ef604075.png)

Apps or websites send requests to the KeyChain through two types of communication - standard I/O streams (mostly called pipes), and the WebSocket. 
The architecture of the KeyChain software consists of the three independent layers:

1. **API layer** which integrates with your app, website or any external application. It is language-neutral. The protocol for the terminal application operates with the JSON format in synchronous request/response way. The main function of the **API layer** is to transmit and parse commands for given API. 
Each request carries information about commands, the type of key user wants to use to sign transactions and other relevant parameters which you can find in the [Protocol](#generate-a-key-pair). 

2. **Security layer** receives the commands from the API layer and acts as an OS-specific  protection mechanism for the **interface window** (third layer). It serves as a shield from potential attacks at sensitive data and information. **Security layer** is tailored for the Mac OS, Linux, and Windows OS and operates only with permitted files (through admin access). 
The request, transmitted to the **Signing module** which holds the private keys, works simultaneously with the Secured input module that uses OS-specific mechanism. The **Secured input module** protects the passphrase from key grabbers and malware.

3. **Representation layer** is the **UI window** which notifies the user about the details of transactions and necessary actions. The **interface window** is initiated from **Security layer**. Once the user inputs the correct passphrase, it sends the permission to the **Signing module** to unlock the demanded key. Passphrase input field is protected by the secured input module. **Security layer** decrypts the given key with the correct passphrase entered by the user.  In this instance **Signing module** can operate with the open private key, for example it can extract information, sign transactions, therefore responding to given requests.

# Useful links

This page contains links to the scholarly and publicistic sources that we find useful concerning KeyChain.

- Building a Transaction By Hand: [guide](https://klmoney.wordpress.com/bitcoin-dissecting-transactions-part-2-building-a-transaction-by-hand/)

- Cryptography Docs: [documentation of cryptographic algorithms used in banking](https://www.adjoint.io/docs/cryptography.html)

- Default nonce function: [blog thread on the topic "What is RFC6979?"](https://www.reddit.com/r/Bitcoin/comments/1mwzo2/what_is_rfc6979/)

- Guide to Elliptic Curve Digital Signatures: [guide with thorough explanations of technical aspects](http://royalforkblog.github.io/2014/09/04/ecc/)

- Доступно о криптографии на эллиптических кривых: [about elliptic curves in Russian](https://habr.com/post/335906/)

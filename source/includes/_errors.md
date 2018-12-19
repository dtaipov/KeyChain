# Errors

<aside class="notice">
If you receive an error message, then the request could not be successfully processed. Here you can find out more about errors, so that you can prevent them next time. If you have trouble dealing with them, please refer to the [log files](#log-files) or [contact us](#developer-debug-information).
</aside>

## Error Handling

In case of an error, the resulting answer will carry an error attribute and a description in human-readable form.
All errors have the following format: 

```json
{"error": ..description.. }
```

Below you will find some examples of the error messages.

#### Error examples

1. The key was not found. You inserted the key that is not listed on KeyChain. You might need to create a key with this name.

**Field name**|**Type**|**Description**|**Value example**
---|---|---|---
error|`string`|simple error message|`Error: keyfile could not be found by this keyname`

```json
{"error":"Error: keyfile could not be found by this keyname"}
```

2. Syntactic error - unsupported symbols. 

**Field name**|**Type**|**Description**|**Value example**
---|---|---|---
error|`string`|simple error message|`Parsing Error`

```json
{"error":"Parsing Error"}
```

## Log files

If you are experiencing any trouble working with KeyChain, here you can find locations of the log files:

For macOS and Linux:

```/var/keychain/logs```

For Windows, log file will be located in the same directory as KeyChain:

```./logs```

## Developer Debug Information
 
If you need help fixing the bugs, please do not hesitate to contact us:

- [Twitter](https://twitter.com/ProjectArray)

- [Facebook](https://www.facebook.com/Array.IO/)

- [Telegram](https://t.me/arrayio)

- [Stackoverflow](https://stackoverflow.com/users/10429540/array-io)

- Or you can write us an email to support@array.io. 

If you want to report a security issue, include the word "security" in the subject line.

We take security issues very seriously and we'll be looking forward to hearing from you. Still, we hope you enjoy using KeyChain and the integration goes smooth! 

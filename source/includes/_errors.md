# Errors

<aside class="notice">
If you receive an error message, then the request could not be successfully processed. Here you can find out more about errors, so that you can prevent them next time. If you have trouble dealing with them, please refer to the [log files](#log-files), create an [issue](https://github.com/arrayio/array-io-keychain/issues/new) with a "bug" tag or [contact us](#developer-debug-information).
</aside>


## Error Handling

In case of an error, the resulting answer will carry an error attribute and the code of an error together with other parameters listed below. When you receive an error code, you can look the error up in the [table](#exception-codes).

All errors have the following format: 

```json
{"id":0,
"error":
{
"code": ..error code number.. ,
"name": ..error name.. ,
"message": ..description.. ,
"trace": 
[{"level":"error","file": ..file name.. ,"line": ..line number.. ,"method": ..name of the function from the source code which carries the error.. ,"timestamp": ..exact time of the error.. },
{"level":"error","file":..file name.. ,"line": ..line number.. ,"method": ..name of the function from the source code which carries the error.. ,"timestamp": ..exact time of the error.. }]
}}
```
## Exception codes

### KeyChain exceptions

**Error code**|**Error name**|**Comment**
---|---|---
1|json_parse_error_code|error in json format of rpc command
2|rpc_command_parse_code|error while parsing the structure of rpc command (invalid set of fields or invalid value type)|
3|command_not_implemented_code|command is not implemented
4|command_depreciated_code|command is depreciated
5|invalid_arg_exception_code|invalid command arguments: occurs in case a parameter's type or value was passed incorrectly
6|privkey_not_found_code|private key could not be found by this keyname
7|privkey_invalid_unlock_code|cannot unlock private key: occurs when the user provided a wrong password from a private key
8|password_input_error_code|error while getting password: all the other errors that occur while receiving a password from a user
9|internal_error_code|some unspecified internal error: might occur if there are errors in the program. If you get this error, it is advisable to contact support.

### Internal errors

**Error code**|**Error name**
---|---
10|parse_error_exception_code
11|timeout_exception_code            
12|file_not_found_exception_code     
13|key_not_found_exception_code      
14|bad_cast_exception_code           
15|assert_exception_code             
16|encryption_error_code             
17|null_optional_code                
18|overflow_code                     
19|underflow_code                    
20|divide_by_zero_code               
21|out_of_range_exception_code       
22|eof_exception_code      

### Third party exceptions

**Error code**|**Error name**|**Comments**
---|---|---
23|std_exception_code|for std::exceptions (3rd party): standard exceptions that occurred in the third party components
24|unhandled_exception_code|for unhandled 3rd party exceptions: all the other exceptions that occurred in the third patry components


## Error example

### Parsing error - the command was incorrect.

Error format

**Field name**|**Type**|**Description**
---|---|---
error|`json object`|error specifics|
code|`integer`|error code number
name|`string`|error name
message|`string`|description of the error starting at the lower level where it occured and showing the higher level
trace|`list of arrays`|trace of the error, starting from the lower level and moving to the higher levels

JSON example

```json
{"id":0,
"error":
{
"code":9,
"name":"parse_error_exception_code",
"message":"Parse Error: invalid index '123' in enum 'keychain_app::command_te' => cannot parse command",
"trace":
[{"level":"error","file":"exception.cpp","line":230,"method":"fc_light::throw_bad_enum_cast","timestamp":"2018-12-25T16:38:41"},
{"level":"error","file":"keychain.cpp","line":90,"method":"keychain_app::keychain::operator ()","timestamp":"2018-12-25T16:38:41"}]
}}
```

## Log files

If you are experiencing any trouble working with KeyChain, here you can find locations of the log files:

For macOS and Linux:

```/var/keychain/logs```

For Windows:

```
Win10: %USERPROFILE%\AppData\Local\Keychain\Logs 
```

//where %USERPROFILE% is a user's folder, e.g. C:\Users\Alice

```
Win7: %SystemRoot%\Logs\Keychain\Logs 
```

//where %SystemRoot% — the folder where the system is installed; usually it is C:\Windows

## Developer Debug Information
 
If you need help fixing the bugs, please do not hesitate to contact us:

- [Telegram](https://t.me/arrayio)
- [Stackoverflow](https://stackoverflow.com/users/10429540/array-io)
- [Twitter](https://twitter.com/ProjectArray)

- Or you can write us an email to support@array.io. 

If you want to report a security issue, include the word "security" in the subject line.

We take security issues very seriously and we'll be looking forward to hearing from you. Still, we hope you enjoy using KeyChain and the integration goes smooth! 
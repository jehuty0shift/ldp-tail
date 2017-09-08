[![Build Status](https://travis-ci.org/ovh/ldp-tail.svg?branch=master)](https://travis-ci.org/ovh/ldp-tail)
[![Go Report Card](https://goreportcard.com/badge/github.com/ovh/ldp-tail)](https://goreportcard.com/report/github.com/ovh/ldp-tail)

Logs Data Platform - Tail
=========================

Installation
------------

To install cli, simply run:
```
$ go get github.com/ovh/ldp-tail
```

Usage
-----

```sh
ldp-tail --address <URI>
```
Demo
----
```sh
ldp-tail --address wss://gra1.logs.ovh.com/tail/?tk=demo --pattern "{{ .short_message }}"
```

Parameters
----------
* Server
  * `address` URI of the websocket
* Filtering
  * `match` Display only messages matching the condition. Example: `_method=verifyPassword`. You may specify an operator like: `_method.begin=verify` or negate its meaning like: `_method.not.begin=verify`. Available operators are:
    * `present` The field is present
    * `begin` The field begins with the value
    * `contain` The field contains the value
    * `lt` The field is less than the value
    * `le` The field is less than or equal to the value
    * `eq` The field is equal to the value
    * `ge` The field is greater than or equal to the value
    * `gt` The field is greater than the value
* Formatting
  * `raw` Display raw message instead of parsing it
  * `pattern` Template to apply on each message to display it. Default: `{{._appID}}> {{.short_message}}`. Custom available functions are:
    * `color` Set text color. Available colors are: `green` `white` `yellow` `red` `blue` `magenta` `cyan`
    * `bColor` Set background color. Available colors are: `green` `white` `yellow` `red` `blue` `magenta` `cyan`
    * `noColor` Disable text and background color
    * `date` Transform a timestamp in a human readable date. Default format is `2006-01-02 15:04:05` but can be customized with the second optional argument
    * `join` Concatenates strings passed in argument with the first argument used as separator
    * `concat` Concatenates strings passed in argument
    * `duration` Transform a value in a human readable duration. First argument must be a parsable number. The second argument is the multiplier coefficient to be applied based on nanoseconds. Ex: 1000000 if the value is in milliseconds.
    * `int` Converts a string in int64
    * `float` Converts a string in float64
    * `get` Return the value under the key passed in the second argument of the map passed first argument. Useful for accessing keys containing a period. Ex: `{{ get . "foo.bar" }}`
    * `column` Formats input into multiple columns. Columns are delimited with the characters supplied in the first argument. Ex: `"{{ column " | " (date .timestamp) (concat ._method " " ._path ) ._httpStatus_int }}`
    * `begin` Return true if the first argument begins with the second
    * `contain` Return true if the second argument is within the first
    * `level` Transform a Gelf/Syslog level value (0-7) to a syslog severity keyword
* Forwarding
  * `forwardURL` The Logs Data Platform GELF Input URL to forward logs to. ldp-tail can forward matching logs to another Logs Data Platform instance by using the URL of the SSL GELF Input. 
  * `forwardToken` The token of the stream you forward logs to. Using a generic input ask you to specify the token of the stream you want to send logs to.  
  * `silent` Disable the print of messages when forwarding. When forwarding is enabled, you can use this option to not display the messages ldp-tail forwards. 
* Config
  * `config` Config file loaded before parsing parameters, so parameters will override the values in the config file (except for `match` where parameters will add more criteria instead of replacing them). The config file use the [TOML](https://github.com/toml-lang/toml) file format. The structure of the configuration file is:
```
Address string
ForwardURL string
ForwardToken string
Match   []{
    Key      string
    Operator string
    Value    interface{}
    Not      bool
}
Pattern string
Raw     bool
```
Exemple:
```
Address = "wss://gra1.logs.ovh.com/tail/?tk=demo"
ForwardURL = "gra2.logs.ovh.com:12202"
ForwardToken = "7489e9b1-8f86-43a2-c9af-0b878687a364"
Pattern = "{{date .timestamp}}: {{if ne ._title \"\"}}[ {{._title}} ] {{end}}{{ .short_message }}"
```

# Contributing

You've developed a new cool feature? Fixed an annoying bug? We'd be happy
to hear from you! Make sure to read [CONTRIBUTING.md](./CONTRIBUTING.md) before.

# License

This work is under the BSD license, see the [LICENSE](LICENSE) file for details.

---
title: CasparCG 2.1 AMCP Protocol
permalink: CasparCG_2.1_AMCP_Protocol\
layout: wiki
tags:
 - Communication Protocol
---

Communication
=============

The *Advanced Media Control Protocol* (AMCP) is the main communication
protocol used to control and query CasparCG Server.

-   All communication is presumed to be encoded in UTF-8.
-   Each command has to be terminated with both a carriage return and a
    linefeed character. For example:
    -   `\r\n`
    -   <CR><LF>
    -   `<0x0D><0x0A>`
    -   `<13><10>`
-   The whole command string is case insensitive.
-   Since the parameters in a command is separated by spaces, you need
    to enclose the parameter with quotation marks if you want it to
    contain spaces.

Backwards Compatibility
=======================

The AMCP 2.1 protocol implementation is mostly backward compatible with
the previous [CasparCG 1.7.1 AMCP
Protocol](CasparCG_1.7.1_AMCP_Protocol "wikilink"), [CasparCG 1.8.0 AMCP
Protocol](CasparCG_1.8.0_AMCP_Protocol "wikilink") and [CasparCG 2.0
AMCP Protocol](CasparCG_2.0_AMCP_Protocol "wikilink")

Breaking Changes (Since 2.0)
----------------------------

Special sequences
=================

Since bare quotation marks are used to keep parameters with spaces in
one piece, there has to be another way to indicate a quotation mark in a
string. Enter special sequences. They behave as in most programming
languages. The escape character is the backslash **`\`** character. In
order to get a quotation mark you enter **`\`"** in the command.

Valid sequences:

-   `\"` Quotation mark
-   `\\` Backslash
-   `\n` New line

These sequences apply to all parameters, it doesn't matter if it's a
file name or a long string of XML data.

Return Codes
============

Information
-----------

-   `100 [action]` - Information about an event.
-   `101 [action]` - Information about an event. A line of data is being
    returned.

Successful
----------

-   `200 [command] OK` - The command has been executed and several lines
    of data (seperated by \\r\\n) are being returned (terminated with an
    additional \\r\\n)
-   `201 [command] OK` - The command has been executed and data
    (terminated by \\r\\n) is being returned.
-   `202 [command] OK` - The command has been executed.

Client Error
------------

-   `400 ERROR` - Command not understood and data (terminated by \\r\\n)
    is being returned.
-   `401 [command] ERROR` - Illegal video\_channel
-   `402 [command] ERROR` - Parameter missing
-   `403 [command] ERROR` - Illegal parameter
-   `404 [command] ERROR` - Media file not found

Server Error
------------

-   `500 FAILED` - Internal server error
-   `501 [command] FAILED` - Internal server error
-   `502 [command] FAILED` - Media file unreadable
-   `503 [command] FAILED` - Access error

Command Specification
=====================

Required Value
--------------

`[my_value_name:my_type,my_type2]`

Required value with one of the comma-separated types `my_type`*`x`*.

Examples: A required value for `transition` which can be either `CUT`,
`MIX`, `PUSH` or `WIPE`.

`[transition:CUT,MIX,PUSH,WIPE]`  
`>> CUT`  
`<< CUT`

A required value for `video_channel` which must a signed integer.

`[video_channel:int]`  
`>> 1`  
`<< 1`

Optional Expression
-------------------

`{my_expr|my_default_expr}`

Optional expression where `my_def_expr` is used if no expression is
provided. If `|my_def_expr` is not provided then it is assumed to
evaluate to `|""`.

Examples: An optional expression which will be evaluated to `-0` if not
provided.

`{[layer:int]|-0}`  
`>> -2`  
`<< -2`  
`>>`  
`<< -0`

An optional expression will be evaluated to an emptry string if not
provided.

`{[tween:string]}`  
`>> easeinsine`  
`<< easeinsine`  
`>>`  
`<<`

[Category:CasparCG Server](Category:CasparCG_Server "wikilink")

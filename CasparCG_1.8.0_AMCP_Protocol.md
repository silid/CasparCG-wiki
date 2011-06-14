---
title: CasparCG 1.8.0 AMCP Protocol
permalink: CasparCG_1.8.0_AMCP_Protocol\
layout: wiki
tags:
 - Communication Protocol
---

**Document history**

-   **1.7** 2009-04-15 Niklas P Andersson
-   **1.7.1** 2010-03-08 Niklas P Andersson
-   **1.8.0** 2010-04-07 Niklas P Andersson

Advanced Media Control Protocol - AMCP
======================================

-   All communication is presumed to be encoded in UTF-8.
-   Each command has to be terminated with both a carriage return and''
    ''a linefeed character. (\\r\\n, <CR><LF>, &lt;0x0D&gt;&lt;0x0A&gt;
    or &lt;13&gt;&lt;10&gt;, whichever your programming language likes
    best).
-   The whole command string is **case insensitive.**
-   And since the parameters in a command is separated by spaces, you
    need to enclose the parameter with quotation marks if you want it to
    contain spaces.

Change Log
----------

The AMCP Protocol has not been updated or modified at all between
version 1.7.1 and 1.8.0. However, since 1.8.0 is the first public
release of the source code for CasparCG, the version number has been
bumped up to 1.8.0.

Special sequences
-----------------

Since bare quotation marks are used to keep parameters with spaces in
one piece, there has to be another way to indicate a quotation mark *in*
a string. Enter special sequences. Thay behave as in most programming
languages. The escape character is backslash (’\\’). In order to get a
quotation mark you enter \\”. The full list of valid sequences is just
below.

|              |                 |
|--------------|-----------------|
| **sequence** | **result**      |
| \\”          | Quotation mark. |
| \\\\         | Backslash.      |
| \\n          | New line        |

  
These sequences apply to all parameters, it doesn’t matter if it’s a
file name or a long string of xml-data.

Channels
--------

Channels are referred to using a one-based index. That is, the first
channel is Channel 1 and so on.

Play-out commands
=================

LOAD
----

**Syntax** <font face="Courier New, monospace">LOAD
</font><font face="Courier New, monospace">*channel*</font><font face="Courier New, monospace">
</font><font face="Courier New, monospace">*file*</font><font face="Courier New, monospace">
\[loop\]</font>

  
LOAD loads and prepares a clip for playout. Load stops any currently
playing clip and displays the first frame of the new clip. Supply the
LOOP parameter if you want the clip to loop.

LOADBG
------

**Syntax** <font face="Courier New, monospace">LOADBG
</font><font face="Courier New, monospace">*channel*</font><font face="Courier New, monospace">
</font><font face="Courier New, monospace">*file*</font><font face="Courier New, monospace">
\[loop\]
\[</font><font face="Courier New, monospace">*transition*</font><font face="Courier New, monospace">\]
\[</font><font face="Courier New, monospace">*duration*</font><font face="Courier New, monospace">\]
\[</font><font face="Courier New, monospace">*direction*</font><font face="Courier New, monospace">\]
\[</font><font face="Courier New, monospace">*border*</font><font face="Courier New, monospace">\]
\[</font><font face="Courier New, monospace">*border-width*</font><font face="Courier New, monospace">\]</font>

  
LOADBG loads and prepares a clip for playout *in the background*. It
does not affect the currently playing clip in anyway. This is how you
prepare a transition between to clips. Supply the LOOP parameter if you
want the clip to loop.

### Parameters

|               |                                              |                                              |
|---------------|----------------------------------------------|----------------------------------------------|
| **Parameter** | **valid values**                             | **Comment**                                  |
| transition    | Cut (default), Mix, Push, Slide, Wipe        | The type of transition                       |
| duration      | 0 (default) - 65535                          | The length of the transition in frames       |
| direction     | Right / Fromleft (default), Left / Fromright | Push, slide and wipe needs a direction       |
| border        | *filename* / *\#aarrggbb*                    | Push, slide and wipe can have a border       |
| border-width  | 0 (default) - 65535                          | The width of the border if it’s not an image |

PLAY
----

**Syntax** <font face="Courier New, monospace">PLAY
</font><font face="Courier New, monospace">*channel*</font>

  
Starts the playout on a channel. If a transition is prepared it will
execute and then the new clip will keep playing.

STOP
----

**Syntax** <font face="Courier New, monospace">STOP
</font><font face="Courier New, monospace">*channel*</font>

  
Stops the playout on a channel. Nothing is done to prevent flickering if
the channel is operating in a fields-based videomode.

CLEAR
-----

**Syntax** <font face="Courier New, monospace">CLEAR
</font><font face="Courier New, monospace">*channel*</font>

  
Stops the playout if running and removes anything visible (by loading an
transparent black frame). Please note that this DOES NOT AFFECT any
template graphics that happens to be visible.

  

Template data
=============

Format
------

Data to templates is sent in xml. The xml is formated like this:

<font face="Courier New, monospace"><templateData>

<componentData id="f0">

<data id="text" value="Niklas P Andersson" /> </componentData>

<componentData id="f1">

<data id="text" value="Developer" />

</componentData>

<componentData id="f2">

<data id="text" value="Providing an example" />

</componentData>

</templateData> </font>  
The node under each componentData is sent directly into the specified
component. This makes it possible to provide completely custom data to
templates. The data-nodes in this example is just the way the default
CasparCG textfield wants its data. More information about this will be
provided with the tools and actionscript classes required to build your
own templates.

  
**A complete call***' to CG ADD (see below), correctly escaped and with
the data above would look like this:*'

<font face="Courier New, monospace">CG 1 ADD 0
“</font><font face="Courier New, monospace">demo/test” 1
“<templateData><componentData id=\"f0\"><data id=\"text\" value=\"Niklas P Andersson\"></data>
</componentData><componentData id=\"f1\"><data id=\"text\" value=\"developer\"></data></componentData><componentData id=\"f2\"><data id=\"text\" value=\"Providing an example\"></data>
</componentData></templateData>”</font>

DATA commands
-------------

The DATA commands are convenient to use when you have large data sets
that might not be available at broadcast-time. DATA allows you to store
a data set on the CasparCG Server and assign it to a much shorter name.
This name can then be used to recall the data when displaying a template
graphic.

### STORE

**Syntax** <font face="Courier New, monospace">DATA STORE
</font><font face="Courier New, monospace">*name data*</font>

Stores the data set *data* under the name *name.*

### RETRIEVE

**Syntax** <font face="Courier New, monospace">DATA RETRIEVE
</font><font face="Courier New, monospace">*name*</font>

Returns the data saved under the name *name.*

### LIST

**Syntax** <font face="Courier New, monospace">DATA LIST</font>

Returns a list of all saved data sets*.*

  

Commands for template graphics
==============================

**Syntax** <font face="Courier New, monospace"> CG
</font><font face="Courier New, monospace">*channel*</font><font face="Courier New, monospace">
</font><font face="Courier New, monospace">*command*</font><font face="Courier New, monospace">
\[</font><font face="Courier New, monospace">*parameters*</font><font face="Courier New, monospace">\]</font>

CG commands
-----------

### ADD

**Syntax** <font face="Courier New, monospace">CG
</font><font face="Courier New, monospace">*channel*</font><font face="Courier New, monospace">
ADD </font><font face="Courier New, monospace">*layer template
\[play-on-load\] \[data\]*</font>

**Example** <font face="Courier New, monospace">CG 1 ADD 10 svtnews/info
1</font>

Prepares a template for displaying. It won’t show until you call CG PLAY
(unless you supply the play-on-load flag, which is simply a ‘1’. ‘0’ for
“don’t play on load”). ''data '' is either inline xml or a reference to
a saved dataset. Please see the **Template data / Format** section above
for a complete example with data.

### REMOVE

**Syntax** <font face="Courier New, monospace">CG
</font><font face="Courier New, monospace">*channel*</font><font face="Courier New, monospace">
REMOVE </font><font face="Courier New, monospace">*layer*</font>

Removes the visible template from a specific layer.

### CLEAR

**Syntax** <font face="Courier New, monospace">CG
</font><font face="Courier New, monospace">''channel
''</font><font face="Courier New, monospace">CLEAR</font>

Clears all layers and any state that might be stored. What this actually
does behind the scene is to create a new instance of the Adobe Flash
player ActiveX controller in memory.

### PLAY

**Syntax** <font face="Courier New, monospace">CG
</font><font face="Courier New, monospace">''channel
*</font><font face="Courier New, monospace">PLAY
</font><font face="Courier New, monospace">*layer''</font>

Plays / displays the template in the specified layer

### STOP

**Syntax** <font face="Courier New, monospace">CG
</font><font face="Courier New, monospace">''channel
*</font><font face="Courier New, monospace">STOP
</font><font face="Courier New, monospace">*layer''</font>

Stops and removes the template from the specified layer. This is
different than REMOVE in that the template gets a chance to animate out
when it is stopped.

### NEXT

**Syntax** <font face="Courier New, monospace">CG
</font><font face="Courier New, monospace">*channel*</font><font face="Courier New, monospace">
NEXT </font><font face="Courier New, monospace">*layer*</font>

Triggers a ”continue” in the template on the specified layer. This is
used to control animations that has multiple discreet steps.

### GOTO

**Syntax** <font face="Courier New, monospace">CG
</font><font face="Courier New, monospace">''channel
*</font><font face="Courier New, monospace">GOTO
</font><font face="Courier New, monospace">*layer label''</font>

Jumps to the specified label in the template on the specified layer.

### UPDATE

**Syntax** <font face="Courier New, monospace">CG
</font><font face="Courier New, monospace">''channel
*</font><font face="Courier New, monospace">UPDATE</font><font face="Courier New, monospace">*
layer data''</font>

Sends new data to the template on specified layer. *data* is either
inline xml or a reference to a saved dataset.

### INVOKE

**Syntax** <font face="Courier New, monospace">CG
</font><font face="Courier New, monospace">*channel*</font><font face="Courier New, monospace">
INVOKE </font><font face="Courier New, monospace">*layer method*</font>

Calls a custom method in the document class of the template on the
specified layer. The method must return void and take no parameters.

Commands for statistics and status
==================================

CINF
----

**Syntax** <font face="Courier New, monospace">CINF
</font><font face="Courier New, monospace">*filename*</font>

  
Returns information about a mediafile

CLS
---

**Syntax** <font face="Courier New, monospace">CLS</font>

  
Lists all mediafiles

TLS
---

**Syntax** <font face="Courier New, monospace">TLS
\[</font><font face="Courier New, monospace">*folder*</font><font face="Courier New, monospace">\]</font>

  
Lists all templates. Lists only templates in the specified folder, if
provided.

VERSION
-------

**Syntax** <font face="Courier New, monospace">VERSION</font>

  
Returns the version of the server.

INFO
----

**Syntax** <font face="Courier New, monospace">INFO
\[</font><font face="Courier New, monospace">*channel*</font><font face="Courier New, monospace">\]</font>

  
Returns information about the channels on the server. Use this without
parameters to check how many channels a server has.

Misc commands
=============

BYE
---

**Syntax** <font face="Courier New, monospace">BYE</font>

  
Disconnects from the server.

Return codes
============

1xx (Informational)
-------------------

These return codes are reserved for future use as parts of a
client-to-client status update system. The idea is to facilitate
monitoring from a client by means of sending a command, for example
MONITOR. This could be used to keep a client up to date on, for example,
which clips are loaded on a monitored server.

|                                                                                      |                                                                                                       |
|--------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------|
| <font color="#000000"><font face="Verdana, sans-serif">100 \[action\] </font></font> | <font face="Verdana, sans-serif">Information about an event.</font>                                   |
| <font color="#000000"><font face="Verdana, sans-serif">101 \[action\] </font></font> | <font face="Verdana, sans-serif">Information about an event. A line of data is being returned.</font> |

2xx (Successful)
----------------

|                                                                                          |                                                                                                                                                                                |
|------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <font color="#000000"><font face="Verdana, sans-serif">202 \[command\] OK </font></font> | <font face="Verdana, sans-serif">The command has been executed </font>                                                                                                         |
| <font face="Verdana, sans-serif">201 \[command\] OK </font>                              | <font face="Verdana, sans-serif">The command has been executed and a line of data is being returned</font>                                                                     |
| <font face="Verdana, sans-serif">200 \[command\] OK </font>                              | <font color="#000000"><font face="Verdana, sans-serif">The command has been executed and several lines of data are being returned (terminated by an empty line.)</font></font> |

4xx (Client error)
------------------

|                                                                |                                                                 |
|----------------------------------------------------------------|-----------------------------------------------------------------|
| <font face="Verdana, sans-serif">400 ERROR </font>             | <font face="Verdana, sans-serif">Command not understood </font> |
| <font face="Verdana, sans-serif">401 \[command\] ERROR </font> | <font face="Verdana, sans-serif">Illegal channel </font>        |
| <font face="Verdana, sans-serif">402 \[command\] ERROR </font> | <font face="Verdana, sans-serif">Parameter missing </font>      |
| <font face="Verdana, sans-serif">403 \[command\] ERROR </font> | <font face="Verdana, sans-serif">Illegal parameter </font>      |
| <font face="Verdana, sans-serif">404 \[command\] ERROR </font> | <font face="Verdana, sans-serif">Media file not found </font>   |

5xx (Server error)
------------------

|                                                                 |                                                                |
|-----------------------------------------------------------------|----------------------------------------------------------------|
| <font face="Verdana, sans-serif">500 FAILED </font>             | <font face="Verdana, sans-serif">Internal server error</font>  |
| <font face="Verdana, sans-serif">501 \[command\] FAILED </font> | <font face="Verdana, sans-serif">Internal server error</font>  |
| <font face="Verdana, sans-serif">502 \[command\] FAILED </font> | <font face="Verdana, sans-serif">Media file unreadable </font> |

  


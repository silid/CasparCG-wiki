---
title: CasparCG 2.0 AMCP Protocol
permalink: CasparCG_2.0_AMCP_Protocol\
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

The AMCP 2.0 protocol implementation is mostly backward compatible with
the previous [CasparCG 1.7.1 AMCP
Protocol](CasparCG_1.7.1_AMCP_Protocol "wikilink") and [CasparCG 1.8.0
AMCP Protocol](CasparCG_1.8.0_AMCP_Protocol "wikilink"). This is
achieved by providing default values for parameters used by the AMCP 2.0
protocol.

Breaking Changes
----------------

The `CLEAR` command will also clear any visible template graphic in the
specified container.

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

-   `400 ERROR` - Command not understood
-   `401 [command] ERROR` - Illegal video\_channel
-   `402 [command] ERROR` - Parameter missing
-   `403 [command] ERROR` - Illegal parameter
-   `404 [command] ERROR` - Media file not found

Server Error
------------

-   `500 FAILED` - Internal server error
-   `501 [command] FAILED` - Internal server error
-   `502 [command] FAILED` - Media file unreadable

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

Basic Commands
==============

LOADBG
------

`LOADBG [channel:int]{-[layer:int]} [clip:string] {[loop:LOOP]} {[transition:CUT,MIX,PUSH,WIPE,SLIDE] [duration:uint] `  
`{[tween:string]|linear} {[direction:LEFT,RIGHT]|RIGHT}|CUT 0} {SEEK [frame:uint]} {LENGTH [frames:uint]} `  
`{FILTER [filter:string]} {[auto:AUTO]}`

Loads a producer in the background and prepares it for playout. If no
layer is specified the default layer index will be used.

*clip* will be parsed by available registered producer factories. If a
successfully match is found, the producer will be loaded into the
background.

If a file with the same name (extension excluded) but with the
additional postfix `_a` is found this file will be used as key for the
main `clip`.

*loop* will cause the clip to loop.

When playing and looping the clip will start at *frame*.

When playing and loop the clip will end after *frames* number of frames.

*auto* will cause the clip to automatically start when foreground clip
has ended (without play). The clip is considered “started” after the
optional transition has ended.

Examples:

`LOADBG 1-1 MY_FILE PUSH 20 easeinesine LOOP SEEK 200 LENGTH 400 AUTO FILTER hflip`

`LOADBG 1 MY_FILE PUSH 20 EASEINSINE`

`LOADBG 1-1 MY_FILE SLIDE 10 LEFT`

`LOADBG 1-0 MY_FILE`

To automatically clear a layer or channel after a video file has been
played to the end:

`PLAY 1-1 MY_FILE`  
`LOADBG 1-1 EMPTY MIX 20 AUTO`

See [Animation Types](CasparCG_Server#Animation_Types "wikilink") for
supported values for `tween`.

See [1](http://libav.org/libavfilter.html) for supported values for the
`filter` command.

Note: Filters are currently only supported by some producers (FFMPEG and
Decklink). Use *deinterlace* or *deinterlace\_bob* as short-hands for
deinterlacing filters.

LOAD
----

`LOAD [video_channel:int]{-[layer:int]|-0}  [clip:string] {`“`additional`` ``parameters`”`}`

Loads a clip to the foreground and plays the first frame before pausing.
If any clip is playing on the target foreground then this clip will be
replaced.

Examples:

`LOAD 1 MY_FILE`

`LOAD 1-1 MY_FILE`

Note: See [LOADBG](CasparCG_2.0_AMCP_Protocol#LOADBG "wikilink") for
additional details.

PLAY
----

`PLAY [video_channel:int]{-[layer:int]|-0} {[clip:string]} {`“`additional`` ``parameters`”`}`  
`    `

Moves clip from background to foreground and starts playing it. If a
transition (see [LOADBG](CasparCG_2.0_AMCP_Protocol#LOADBG "wikilink"))
is prepared, it will be executed.

If additional parameters (see
[LOADBG](CasparCG_2.0_AMCP_Protocol#LOADBG "wikilink")) are provided
then the provided clip will first be loaded to the background.

Examples:

`PLAY 1 MY_FILE PUSH 20 EASEINSINE`

`PLAY 1-1 MY_FILE SLIDE 10 LEFT`

`PLAY 1-0 MY_FILE`

Note: See [LOADBG](CasparCG_2.0_AMCP_Protocol#LOADBG "wikilink") for
additional details.

PAUSE
-----

`PAUSE [video_channel:int]{-[layer:int]|-0}`

Pauses foreground clip, from version 2.0.7 the [
RESUME](CasparCG_2.0_AMCP_Protocol#RESUME "wikilink") command can be
used to start playback after this command has been used.

Examples:

`PAUSE 1`

`PAUSE 1-1`

RESUME
------

Added in version 2.0.7.

`RESUME [video_channel:int]{-[layer:int]|-0}`

Resumes playback of a foreground clip previously pause with the [
PAUSE](CasparCG_2.0_AMCP_Protocol#PAUSE "wikilink") command

Examples:

`RESUME 1`

`RESUME 1-1`

STOP
----

`STOP [video_channel:int]{-[layer:int]|-0}`

Removes foreground clip.

Examples:

`STOP 1`

`STOP 1-1`

CLEAR
-----

`CLEAR [video_channel:int]{-[layer:int]}`

Removes all clips (both foreground and background). If no layer is
specified then all layers in the specified `video_channel` are cleared.

Examples:

`CLEAR 1`

Clears everything from the entire channel 1.

`CLEAR 1-3`

Clears only layer 1 of channel 3.

CALL
----

`CALL [video_channel:int]{-[layer:int]|-0} [param:string]`

Calls method on the specified producer with the provided `param` string.

Examples:

`CALL 1 LOOP`

`CALL 1-2 SEEK 25`

SWAP
----

`SWAP [channel1:int]{-[layer1:int]} [channel2:int]{-[layer2:int]}`

Swaps layers between channels (both foreground and background will be
swapped). If layers are not specified then all layers in respective
`video_channel` will be swapped.

Examples:

`SWAP 1 2`

`SWAP 1-1 2-3`  
`   `

ADD
---

`ADD [video_channel:int] [consumer:string] [parameters:string]`

Adds a consumer to the video channel specified, the string `consumer`
will be parsed by available registered consumer factories. If a
successful match is found a consumer will be created and added to the
`video_channel`. Different consumers require different parameters, some
examples are below. Consumers can alternatively be specified by adding
them to [the CasparCG config
file](CasparCG_Server#Consumers_Configuration "wikilink").

Examples:

`ADD 1 DECKLINK 1`

`ADD 1 BLUEFISH 2`

`ADD 1 SCREEN`

`ADD 1 AUDIO`

`ADD 1 IMAGE filename`

`ADD 1 FILE filename.mov`

`ADD 1 FILE filename.mov SEPARATE_KEY`

The streaming consumer is an implementation of the ffmpeg\_consumer and
supports many of the same arguments, available from version 2.0.7 Beta
2.

`ADD 1 STREAM `[`udp://localhost:5004`](udp://localhost:5004)` -vcodec libx264 -tune zerolatency -preset ultrafast -crf 25 -format mpegts -vf scale=240:180`

REMOVE
------

`REMOVE [video_channel:int] [parameters:string]`

Removes an existing consumer from `video_channel`.

Examples:

`REMOVE 1 DECKLINK 1`

`REMOVE 1 BLUEFISH 2`

`REMOVE 1 SCREEN`

`REMOVE 1 AUDIO`

`REMOVE 1 FILE`

PRINT
-----

`PRINT [video_channel:int]`

Saves an RGBA PNG bitmap still image of the contents of the specified
channel in the **media** folder.

Examples:

`PRINT 1`

Will produce a PNG image with the current date and time as the filename
for example 20130620T192220.png

LOG
---

SET
---

`SET [video_channel:int] MODE [video_format]`

Changes the [video format](CasparCG_Server#Video_Formats "wikilink") of
the specified channel.

Examples:

`SET 1 MODE PAL`

...changes the video mode on channel “1” to “PAL”.

Data Commands
=============

The `DATA` commands are convenient to use when you have large datasets
that might not be available at broadcast-time. `DATA` allows you to
store a dataset on the CasparCG Server and assign it to a much shorter
name. This name can then be used to recall the data when displaying a
template graphic.

DATA STORE
----------

`DATA STORE [name:string] `[`2`](data:string)

Stores the dataset data under the name name.

Since [v2.0.3](CasparCG_Server#CasparCG_Server_2.0.3_Stable "wikilink"),
adds support for creating directories that does not exist.

Example:

`DATA STORE my_data `“`Some`` ``useful`` ``data`”  
`DATA STORE Folder1\\my_data `“`Some`` ``useful`` ``data`”

DATA RETRIEVE
-------------

`DATA RETRIEVE [name:string] `[`3`](data:string)

Returns the data saved under the name `name`.

Example:

`DATA RETRIEVE my_data`  
`DATA RETRIEVE Folder1\\my_data`

DATA LIST
---------

`DATA LIST`

Returns a list of all saved datasets.

DATA REMOVE
-----------

Available since
[v2.0.3](CasparCG_Server#CasparCG_Server_2.0.3_Stable "wikilink"),
allows removal of the dataset under the name `name`.

`DATA REMOVE [name:string]`

Example:

`DATA REMOVE my_data`  
`DATA REMOVE Folder1\\my_data`

Template Graphics Commands
==========================

Commands that are sent to Flash templates.

Template Data
-------------

Data to templates is sent as xml, formatted as follows:

<templateData>  
`   `<componentData id="f0">  
`       `<data id="text" value="Niklas P Andersson" />` `</componentData>  
`   `<componentData id="f1">  
`       `<data id="text" value="Developer" />  
`   `</componentData>  
`   `<componentData id="f2">  
`       `<data id="text" value="Providing an example" />  
`   `</componentData>  
</templateData>

The node under each componentData is sent directly into the specified
component. This makes it possible to provide completely custom data to
templates. The data-nodes in this example is just the way the default
CasparCG textfield wants its data. More information about this will be
provided with the tools and ActionScript classes required to build your
own templates.

A complete call to `CG ADD` (see below), correctly escaped and with the
data above would look like this:

`CG 1 ADD 0 `“`demo/test`”` 1 "`<templateData><componentData id=\"f0\">  
<data id=\"text\" value=\"Niklas P Andersson\">  
</data>` `</componentData><componentData id=\"f1\">  
<data id=\"text\" value=\"developer\">  
</data></componentData><componentData id=\"f2\">  
<data id=\"text\" value=\"Providing an example\"></data>` `</componentData></templateData>`"`

CG ADD
------

`CG [video_channel:int]{-[layer:int]|-0} `  
`ADD [flash_layer:uint] [template:string] [play-on-load:0,1] [data]`

Prepares a template for displaying. It won't show until you call
`CG PLAY` (unless you supply the play-on-load flag, `1` for true). Data
is either inline xml or a reference to a saved dataset.

Examples:

`CG 1 ADD 10 svtnews/info 1`

CG REMOVE
---------

`CG [video_channel:int]{-[layer:int]|-0} REMOVE [flash_layer:uint]`

Removes the visible template from a specific layer.

CG CLEAR
--------

`CG [video_channel:int]{-[layer:int]|-0} CLEAR`

Clears all layers and any state that might be stored. What this actually
does behind the scene is to create a new instance of the Adobe Flash
player ActiveX controller in memory.

CG PLAY
-------

`CG [video_channel:int]{-[layer:int]|-0} PLAY [flash_layer:uint]`

Plays and displays the template in the specified layer.

CG STOP
-------

`CG [video_channel:int]{-[layer:int]|-0} STOP [flash_layer:uint]`

Stops and removes the template from the specified layer. This is
different than `REMOVE` in that the template gets a chance to animate
out when it is stopped.

CG NEXT
-------

`CG [video_channel:int]{-[layer:int]|-0} NEXT [flash_layer:uint]`

Triggers a “continue” in the template on the specified layer. This is
used to control animations that has multiple discreet steps.

CG GOTO
-------

Deprecated and replaced with [CG
INVOKE](CasparCG_2.0_AMCP_Protocol#CG_INVOKE "wikilink").

CG UPDATE
---------

`CG [video_channel:int]{-[layer:int]|-0} UPDATE [flash_layer:uint] `[`4`](data:string)

Sends new data to the template on specified layer. Data is either inline
xml or a reference to a saved dataset.

CG INVOKE
---------

`CG [video_channel:int]{-[layer:int]|-0} INVOKE [flash_layer:uint] [method:string]`

Calls a custom method in the document class of the template on the
specified layer. The method must return void and take no parameters.

Can be used to jump the playhead to a specific label.

Mixer Commands
==============

MIXER KEYER
-----------

`MIXER [video_channel:int]{-[layer:int]|-0} KEYER {keyer:0,1|0}`

Replaces layer ***n+1***'s alpha channel with the **R** (red) channel of
layer **n**, and hides the RGB channels of layer **n**. If `keyer`
equals `1` then the specified layer will not be rendered, instead it
will be used as the key for the layer above.

Examples:

`MIXER 1-0 KEYER 1`

MIXER CHROMA
------------

`MIXER [video_channel:int]{-[layer:int]|-0} CHROMA [color:none|green|blue] [threshold-lower:float] [threshold-upper:float]`

Enables chroma keying on the specified video layer.

Examples:

`>> MIXER 1-1 CHROMA green 0.10 0.20`  
`>> MIXER 1-1 CHROMA none`

MIXER BLEND
-----------

`MIXER [video_channel:int]{-[layer:int]|-0} BLEND {[blend:string]|normal}`

Every layer in the Mixer module can be set to a [blend
mode](http://en.wikipedia.org/wiki/Blend_modes) over than the default
*Normal* mode, similar to applications like Photoshop. Some common uses
are to use **screen** to make a all the black image data become
transparent, or to use **add** to selectively lighten highlights.

Example:

`MIXER 1-1 BLEND OVERLAY`  
`   `

See [Blend Modes](CasparCG_Server#Blend_Modes "wikilink") for supported
values for `blend`.

MIXER OPACITY
-------------

`MIXER [video_channel:int]{-[layer:int]|-0} VIDEO OPACITY [opacity:float] {[duration:uint] {[tween:string]|linear}|0 linear}`

Changes the opacity of the specified layer. The value is a float between
0 and 1.

Examples:

`>> MIXER 1-0 OPACITY 0.5 25 easeinsine`  
`   `

See [Animation Types](CasparCG_Server#Animation_Types "wikilink") for
supported values for `tween`.

MIXER BRIGHTNESS
----------------

`MIXER [video_channel:int]{-[layer:int]|-0} BRIGHTNESS [brightness:float] {[duration:uint] {[tween:string]|linear}|0 linear}`

Changes the brightness of the specified layer. The value is a float
between 0 and 1.

Examples:

`>> MIXER 1-0 BRIGHTNESS 0.5 25 easeinsine`  
`   `

See [Animation Types](CasparCG_Server#Animation_Types "wikilink") for
supported values for `tween`.

MIXER SATURATION
----------------

`MIXER [video_channel:int]{-[layer:int]|-0} SATURATION [saturation:float] {[duration:uint] {[tween:string]|linear}|0 linear}`

Changes the saturation of the specified layer. The value is a float
between 0 and 1.

Examples:

`>> MIXER 1-0 SATURATION 0.5 25 easeinsine`  
`   `

See [Animation Types](CasparCG_Server#Animation_Types "wikilink") for
supported values for `tween`.

MIXER CONTRAST
--------------

`MIXER [video_channel:int]{-[layer:int]|-0} CONTRAST [contrast:float] {[duration:uint] {[tween:string]|linear}|0 linear}`

Changes the contrast of the specified layer. The value is a float
between 0 and 1.

Examples:

`>> MIXER 1-0 CONTRAST 0.5 25 easeinsine`  
`   `

See [Animation Types](CasparCG_Server#Animation_Types "wikilink") for
supported values for `tween`.

MIXER LEVELS
------------

`MIXER [video_channel:int]{-[layer:int]|-0} LEVELS [min_input:float] [max_input:float] [gamma:float] [min_output:float] [max_output:float] {[duration:uint] {[tween:string]|linear}|0 linear}`

Changes the contrast of the specified layer. The value is a float
between 0 and 1.

Examples:

`>> MIXER 1-0 LEVELS 0.1 0.1 1.0 0.9 0.9 25 easeinsine`  
`   `

See [Animation Types](CasparCG_Server#Animation_Types "wikilink") for
supported values for `tween`.

MIXER FILL
----------

`MIXER [video_channel:int]{-[layer:int]|-0} FILL [x:float] [y:float] [x-scale:float] [y-scale:float] {[duration:uint] {[tween:string]|linear}|0 linear}`

Scales the video stream on the specified layer. The concept is quite
simple; it comes from the ancient DVE machines like ADO. Imagine that
the screen has a size of 1x1 (not in pixel, but in an abstract measure).
Then the coordinates of a full size picture is 0 0 1 1, which means left
edge is at coordinate 0, top edge at coordinate 0, width full size =&gt;
1, heigh full size =&gt; 1.

If you want to crop the picture on the left side (for wipe left to
right) You set the left edge to full right =&gt; 1 and the width to 0.
So this give you the start-coordinates of 1 0 0 1.

End coordinates of any wipe are allways the full picture 0 0 1 1.

With the FILL command it can make sense to have values between 1 and 0,
if you want to do a smaller window. If, for instance you want to have a
window of half the size of your screen, you set with and height to 0.5.
If you want to center it you set left and top edge to 0.25 so you will
get the arguments 0.25 0.25 0.5 0.5

-   `x` The left edge of the new fillSize, 0 = left edge of monitor, 0.5
    = middle of monitor, 1.0 = right edge of monitor. Higher and lower
    values allowed.
-   `y` The top edge of the new fillSize, 0 = top edge of monitor, 0.5 =
    middle of monitor, 1.0 = bottom edge of monitor. Higher and lower
    values allowed.
-   `x-scale` The size of the new fillSize, 1 = 1x the screen size, 0.5
    = half the screen size. Higher and lower values allowed.
-   `y-scale` The size of the new fillSize, 1 = 1x the screen size, 0.5
    = half the screen size. Higher and lower values allowed.

Examples:

`>> MIXER 1-0 FILL 0.25 0.25 0.5 0.5 25 easeinsine`

See [Animation Types](CasparCG_Server#Animation_Types "wikilink") for
supported values for `tween`.

MIXER CLIP
----------

`MIXER [video_channel:int]{-[layer:int]|-0} CLIP [x:float] [y:float] [x-scale:float] [y-slace:float] {[duration:uint] {[tween:string]|linear}|0 linear}`

Masks the video stream on the specified layer. The concept is quite
simple; it comes from the ancient DVE machines like ADO. Imagine that
the screen has a size of 1x1 (not in pixel, but in an abstract measure).
Then the coordinates of a full size picture is 0 0 1 1, which means left
edge is at coordinate 0, top edge at coordinate 0, width full size =&gt;
1, heigh full size =&gt; 1.

If you want to crop the picture on the left side (for wipe left to
right) You set the left edge to full right =&gt; 1 and the width to 0.
So this give you the start-coordinates of 1 0 0 1.

End coordinates of any wipe are allways the full picture 0 0 1 1.

-   `x` The left edge of the new clipSize, 0 = left edge of monitor, 0.5
    = middle of monitor, 1.0 = right edge of monitor. Higher and lower
    values allowed.
-   `y` The top edge of the new clipSize, 0 = top edge of monitor, 0.5 =
    middle of monitor, 1.0 = bottom edge of monitor. Higher and lower
    values allowed.
-   `x-scale` The size of the new clipSize, 1 = 1x the screen size, 0.5
    = half the screen size.
-   `y-scale` The size of the new clipSize, 1 = 1x the screen size, 0.5
    = half the screen size.

Examples:

`>> MIXER 1-0 CLIP 0.25 0.25 0.5 0.5 25 easeinsine`  
`   `

See [Animation Types](CasparCG_Server#Animation_Types "wikilink") for
supported values for `tween`.

MIXER ANCHOR
------------

Added in version 2.0.7 Stable

`MIXER [video_channel:int]{-[layer:int]|-0} ANCHOR {[x:float]} {[y:float]}`

Returns (if no arguments are specified) or changes the anchor point
around which fill\_translation, fill\_scale and
[ROTATION](CasparCG_2.0_AMCP_Protocol#MIXER_ROTATION "wikilink") will be
done from.

-   `x` The left anchor point, 0 = left edge of monitor, 0.5 = middle of
    monitor, 1.0 = right edge of monitor. Higher and lower values
    allowed.
-   `y` The top anchor point, 0 = top edge of monitor, 0.5 = middle of
    monitor, 1.0 = bottom edge of monitor. Higher and lower values
    allowed.

Examples:

`>> MIXER 1-10 ANCHOR 0.5 0.6`  
`#202 MIXER OK`  
`>> MIXER 1-10 ANCHOR`  
`#201 MIXER OK`  
`0.5 0.6`

MIXER CROP
----------

Added in version 2.0.7 Stable

`MIXER [video_channel:int]{-[layer:int]|-0} CROP [left-edge:float] [top-edge:float] [right-edge:float] [bottom-edge:float] {[duration:uint] {[tween:string]|linear}|0 linear}`

Returns (if no arguments are specified) or modify how textures on a
layer will be cropped.

MIXER ROTATION
--------------

Added in version 2.0.7 Stable

`MIXER [video_channel:int]{-[layer:int]|-0} ROTATION {[angle:int]}`

Returns or modifies the angle of which a layer is rotated by (clockwise
degrees) around the point specified by
[ANCHOR](CasparCG_2.0_AMCP_Protocol#MIXER_ANCHOR "wikilink").

MIXER PERSPECTIVE
-----------------

Added in version 2.0.7 Stable

`MIXER [video_channel:int]{-[layer:int]|-0} PERSPECTIVE [top_left-x:float] [top_left-y:float] [top_right-x:float] [top_right-y:float] [bottom_right-x:float] [bottom_right-y:float] [bottom_left-x:float] [bottom_left-y:float]`

Returns or modifies the corners of the perspective transformation for a
layer.

Example:

`>>MIXER 1-10 PERSPECTIVE 0.4 0.4 0.6 0.4 1 1 0 1`

MIXER VOLUME
------------

Syntax:

`MIXER [video_channel:int]{-[layer:int]|-0} VOLUME [volume:float] {[duration:uint] {[tween:string]|linear}|0 linear}`

Changes the volume of the specified layer.

-   Examples:

`>> MIXER 1-0 VOLUME 0.5 25 easeinsine`  
`   `

See [Animation Types](CasparCG_Server#Animation_Types "wikilink") for
supported values for `tween`.

MIXER MASTERVOLUME
------------------

Syntax:

`MIXER [video_channel:int] MASTERVOLUME [volume:float]`

Changes the volume of an entire channel.

-   Examples:

`>> MIXER 1 MASTERVOLUME 0`  
`>> MIXER 1 MASTERVOLUME 1`  
`>> MIXER 1 MASTERVOLUME 0.5`

MIXER STRAIGHT\_ALPHA\_OUTPUT
-----------------------------

Syntax:

`MIXER [video_channel:int] STRAIGHT_ALPHA_OUTPUT {active:0|1}`

If enabled, causes RGB values to be divided with the alpha for the given
video channel before the image is sent to consumers.

Examples:

`>> MIXER 1 STRAIGHT_ALPHA_OUTPUT 1`  
`>> MIXER 1 STRAIGHT_ALPHA_OUTPUT 0`

MIXER CLEAR
-----------

Syntax:

`MIXER [video_channel:int]{-[layer:int]|-0} CLEAR`

Reset all transformations.

-   Examples:

`>> MIXER 1 CLEAR`

`>> MIXER 1-1 CLEAR`

MIXER GRID
----------

`MIXER [video_channel:int] GRID [resolution:uint] {[duration:uint] {[tween:string]|linear}|0 linear}`

Creates a grid of video streams in ascending order of the layer index,
i.e. if `resolution` equals 2 then a 2x2 grid of layers will be created.

Examples:

`>> MIXER 1 GRID 2`  
`   `

See [Animation Types](CasparCG_Server#Animation_Types "wikilink") for
supported values for `tween`.

CHANNEL\_GRID
-------------

`CHANNEL_GRID`

Opens a new channel and displays a grid with the contents of all the
existing channels.

Thumbnail Commands
==================

THUMBNAIL LIST
--------------

Lists all thumbnails.

Examples:

`<< THUMBNAIL LIST`  
`>> `“`AMB`”` 20130301T124409 1149`  
`>> `“`foo/bar`”` 20130523T234001 244`

Where the columns are **filename**, **modified date** and **file size**

THUMBNAIL RETRIEVE
------------------

`THUMBNAIL RETRIEVE [filename:string]`

Retrieves a thumbnail as a base64 encoded PNG-image.

Examples:

`>> THUMBNAIL RETRIEVE foo/bar`  
`<< iVBORw0KGgoAAAANSUhEUgAAAQAAAACQCAYAAADnY7WRAAAACXBIWXMAAAsTAAALEwEAmpwYAAAA`  
`<< pklEQVR4nO3BgQAAAADDoPlTX+AIVQEAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA`  
`<< AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA`  
`<< AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAfANArgAB`  
`<< XQri0gAAAABJRU5ErkJ==`

THUMBNAIL GENERATE
------------------

`THUMBNAIL GENERATE [filename:string]`

Regenerate a thumbnail.

THUMBNAIL GENERATE\_ALL
-----------------------

`THUMBNAIL GENERATE_ALL`

Regenerate all thumbnails.

Query Commands
==============

CINF
----

`CINF [filename:string]`

Returns information about a media file. Return parameters: **type**
(MOVIE, STILL or AUDIO), **file size** in bytes and **date modified**
(YYYYMMDDHHMMSS.)

-   Examples:

`>> CINF EXAMPLE`

`<< `“`example`”` MOVIE 4100882 20061117041444`

CLS
---

`CLS `

Lists all media files in the **media** folder. Use the command [INFO
PATHS](#INFO "wikilink") to get the path to the **media** folder.

TLS
---

`TLS {folder:string|./} `

Lists all templates in **templates** folder. Use the command [INFO
PATHS](#INFO "wikilink") to get the path to the **templates** folder.

With the optional folder parameter, you can limit the results to a
specified subfolder.

VERSION
-------

Syntax:

`VERSION {[component:SERVER,FLASH,TEMPLATEHOST]|SERVER}`

Returns the version of specified component.

INFO
----

Syntax:

`INFO ([channel:int]{-[layer:int]}|TEMPLATE [filename:string])`

### INFO Examples

`INFO`

Returns information about all the video formats and state
(playing/stopped etcetera) of all current CasparCG channels.

-   Example:

`>> INFO`  
  
`1 720p5000 PLAYING`  
`2 720p5000 PLAYING`  
`3 576p2500 PLAYING`

`INFO 1`

Returns information about CasparCG channel 1 and all its layers,
including video formats and their state (playing/stopped etcetera.)

`INFO 1-2`

Returns information about layer 2 of CasparCG channel 1, including video
formats and its state (playing/stopped etcetera.)

`INFO 1-2 F`

Returns information about the foreground of layer 2 of CasparCG channel
1.

`INFO 1-2 B`

Returns information about the background of layer 2 of CasparCG channel
1.

`INFO 1 DELAY`

Returns delay measurements for channel 1 (producers - mixer - consumers)

`INFO 1-2 DELAY`

Returns delay measurements for the producer running on channel 1 layer 2

`INFO CONFIG`

Returns the current *casparcg.config* file used last time CasparCG
Server was started.

`INFO PATHS`

Returns information about the paths to the **templates** folder (Flash
templates,) the **media** folder (videos and images,) and to the
**TemplateHost**.

`INFO SYSTEM`

Returns information about the current hardware, the OS and version of
libraries and the Flash Player.

### INFO SERVER

Returns information about all the current CasparCG channels and
consumers, based on the *caspar.config* file.

-   Example:

`>> INFO SERVER`  
  

<?xml version="1.0" encoding="utf-8"?>
<channels>  
`   `<channel>  
`       `<video-mode>`1080i5000`</video-mode>  
`       `<stage/>  
`       `<mixer/>  
`       `

<output>
`          `<consumers>  
`             `<consumer>  
`                 `<type>`decklink-consumer`</type>  
`                 `<key-only>`false`</key-only>  
`                 `<device>`1`</device>  
`                 `<low-latency>`0`</low-latency>  
`                 `<embedded-audio>`true`</embedded-audio>  
`                 `<low-latency>`0`</low-latency>  
`                 `<index>`301`</index>  
`             `</consumer>  
`         `</consumers>  
`       `

</output>
`       `<index>`1`</index>  
`   `</channel>  
`   `<channel>  
`       `<video-mode>`576p2500`</video-mode>  
`       `<stage/>  
`       `<mixer/>  
`       `

<output/>
`       `<index>`2`</index>  
`   `</channel>  
</channels>

### INFO THREADS

Available from version 2.0.7, lists the known threads and their
descriptive names. Can be matched against the thread id column of log
entries.

-   Example:

`>> INFO THREADS`  
`#200 INFO THREADS OK`  
`424     output 2`  
`744     mixer 4`  
`1136    AMCPCommandQueue Channel 2 for Console`  
`1628    AMCPCommandQueue General Queue for Console`  
`1668    stage 1`  
`2180    mixer 3`  
`2232    ogl_device`  
`2620    destroyer`  
`2756    stage 3`  
`3912    cef`  
`3916    diagnostics`  
`3920    stage 4`  
`3992    AMCPCommandQueue Channel 3 for TCP Port 5250`  
`4384    output 1`  
`4440    stage 2`  
`4484    mixer 0`  
`4664    AMCPCommandQueue Channel 3 for Console`  
`4684    AMCPCommandQueue Channel 1 for Console`  
`4700    AMCPCommandQueue Channel 4 for TCP Port 5250`  
`4832    output 3`  
`5084    polling_filesystem_monitor`  
`5152    AMCPCommandQueue General Queue for TCP Port 5250`  
`5224    mixer 2`  
`5292    stdin-thread`  
`5300    AMCPCommandQueue Channel 4 for Console`  
`5316    asio-thread`  
`5452    mixer 1`  
`5724    main-thread`  
`5792    AMCPCommandQueue Channel 2 for TCP Port 5250`  
`5984    output 4`  
`6132    AMCPCommandQueue Channel 1 for TCP Port 5250`

### INFO QUEUES

Available from version 2.0.7, useful mainly for debugging AMCP command
queues and overflows, where a command is deadlocked. Hopefully always
accessible via console, even though the TCP command queue may be full.

-   Example:

`>> INFO QUEUES`  
`#201 INFO QUEUES OK`  

<?xml version="1.0" encoding="utf-8"?>
<queues>  
`  `<queue>  
`     `<name>`AMCPCommandQueue Channel 1 for Console`</name>  
`     `<queued>`0`</queued>  
`  `</queue>  
`  `<queue>  
`     `<name>`AMCPCommandQueue Channel 1 for TCP Port 5250`</name>  
`     `<queued>`0`</queued>  
`  `</queue>  
`  `<queue>  
`     `<name>`AMCPCommandQueue Channel 2 for Console`</name>  
`     `<queued>`0`</queued>  
`  `</queue>  
`  `<queue>  
`     `<name>`AMCPCommandQueue Channel 2 for TCP Port 5250`</name>  
`     `<queued>`0`</queued>  
`  `</queue>  
`  `<queue>  
`     `<name>`AMCPCommandQueue Channel 3 for Console`</name>  
`     `<queued>`0`</queued>  
`  `</queue>  
`  `<queue>  
`     `<name>`AMCPCommandQueue Channel 3 for TCP Port 5250`</name>  
`     `<queued>`0`</queued>  
`  `</queue>  
`  `<queue>  
`     `<name>`AMCPCommandQueue Channel 4 for Console`</name>  
`     `<queued>`0`</queued>  
`  `</queue>  
`  `<queue>  
`     `<name>`AMCPCommandQueue Channel 4 for TCP Port 5250`</name>  
`     `<queued>`0`</queued>  
`  `</queue>  
`  `<queue>  
`     `<name>`AMCPCommandQueue General Queue for Console`</name>  
`     `<queued>`0`</queued>  
`     `<running>  
`        `<command>`InfoCommand`</command>  
`        `<params>`queues `</params>  
`        `<elapsed>`0`</elapsed>  
`     `</running>  
`  `</queue>  
`  `<queue>  
`     `<name>`AMCPCommandQueue General Queue for TCP Port 5250`</name>  
`     `<queued>`0`</queued>  
`  `</queue>  
</queues>

STATUS
------

**This command was deprecated with the release of CasparCG Server 2.0
and is replaced by [INFO](#INFO "wikilink") (above.)**

DIAG
----

Syntax:

` DIAG`

Opens the [diagnostics
window](CasparCG_Server#Diagnostics_3 "wikilink").

BYE
---

Syntax:

`BYE`

Disconnects from the server if connected remotely, if interacting
directly with the console on the machine Caspar is running on then this
will achieve the same as the [
KILL](CasparCG_2.0_AMCP_Protocol#KILL "wikilink") command.

KILL
----

Syntax:

`KILL`

Shuts the server down.

RESTART
-------

Syntax:

`RESTART`

Shuts the server down. But exits with return code 5 instead of 0.
Intended for use in combination with `casparcg_auto_restart.bat`

Disk Consumer
=============

Please see the [Disk Consumer](CasparCG_Server#Disk_Consumer "wikilink")
section for information about the commands used for recording output to
disk.

[Category:CasparCG Server](Category:CasparCG_Server "wikilink")

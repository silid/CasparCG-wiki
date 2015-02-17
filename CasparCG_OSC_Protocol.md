---
title: CasparCG OSC Protocol
permalink: CasparCG_OSC_Protocol\
layout: wiki
tags:
 - CasparCG Server
---

Introduction
------------

From v2.0.4 onwards, CasparCG Server adds support for the [Open Sound
Control (OSC)](http://en.wikipedia.org/wiki/Open_Sound_Control) protocol
which sends real-time data and events from CasparCG Server over UDP to a
client.

In CasparCG Server, the OSC implementation provides one way support
only, CasparCG Server provides a client interface, sending status
information to an OSC server but does not listen for OSC datagrams from
remote clients.

Connection
----------

When a client establishes a connection to the AMCP port configured for
the server, an OSC client is started by CasparCG Server against the AMCP
client's IP, using a default UDP port configurable in casparcg.config
(see below), this OSC client will expire when the AMCP connection that
initiated it ends. Persistent OSC clients can be set-up by adding them
to the list of persistent clients in CasparCG config (as below).

Clients receive events by connecting to the OSC client interface
provided by CasparCG Server, and then issuing a **RegisterMethod**
command to subscribe to different properties and actions made available
by the CasparCG Server. Looking at the source code for the Caspar
Monitor [example client](#Example_Clients "wikilink"), in particular
Form1.cs would be a good place to start in understanding CasparCG Server
and OSC.

CasparCG.config
---------------

The following is an example of the configuration entries which relate to
the OSC implementation, as described above, persistent OSC clients can
be added to the list of predefined clients by adding a
<predefined-client> element, in the example below, an OSC client is
defined for an OSC server on localhost port 5253.

<osc>  
` `<default-port>`6250`</default-port>  
` `<predefined-clients>  
`   `<predefined-client>  
`     `

<address>
127.0.0.1

</address>
`     `<port>`5253`</port>  
`   `</predefined-client>  
` `</predefined-clients>  
</osc>

OSC Messages
------------

The OSC protocol uses a fairly simple message structure containing an
address pattern and various arguments corresponding to each pattern, the
protocol also includes other detail as described on the [Wikipedia
entry](http://en.wikipedia.org/wiki/Open_Sound_Control). The below is a
list of address patterns and details of what response subscribing to
this will trigger, this list is currently a work in progress and does
not represent all of the addresses available, the example client by Andy
Mace at the bottom of this page will show you what your CasparCG Server
is doing live. It is important to note that OSC sends messages for
things that are active only, if CasparCG Server is not doing anything
you will only see a small number of lines.

Number ranges such as \[0-9\] should be substituted for the appropriate
channel, layer or output port you are trying to monitor. More than one
digit is acceptable in all of these cases but not specified for brevity.

### Output and Channel setting Messages

Output messages contain information about CasparCG Server
[Consumers](CasparCG_Server#Consumers_.28Output_Modules.29 "wikilink"),
this largely contains information about which Consumers are active,
alongside this are other messages such has format and profiler messages
which contain more information about what the channel is doing.

| Address                    |                | Example Arguments                                                                                                                                                                                                                                     | Description                                                                                                                                                                                        |
|----------------------------|----------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| /channel/\[0-9\]/          | format         | PAL                                                                                                                                                                                                                                                   | The \[\[CasparCG\_Server\#Video\_Formats                                                                                                                                                           |
| profiler/time              | 0.041 <nowiki> | </nowiki> 0.04                                                                                                                                                                                                                                        | The amount of time that CasparCG Server is spending rendering the frame, two arguments are sent in this message, what it is and what it should be as shown in the example.                         |
| output/port/*\[0-9\]*/type | screen         | A message like this will exists for each of the outputs in use, with the default CasparCG config file in use this will result in two rows; one of type screen, and one of type system-audio. Current types are \[\[CasparCG\_Server\#Screen\_Consumer | screen\]\], system-audio, \[\[CasparCG\_Server\#DeckLink\_Consumer                                                                                                                                 |
| output/port/\[0-9\]/frame  | 200 <nowiki>   | </nowiki> 922222222888836854                                                                                                                                                                                                                          | The number of frames that have been created by the consumer on this port, the example indicates that 200 frames have been written to disk and that a maximum of 922222222888836854 can be written. |

### Stage Messages

Stage messages contain properties related to CasparCG Server layers and
the
[producers](CasparCG_Server#Producers_.28Input_Modules.29 "wikilink")
that are contained within them.

##### General Stage messages

The following messages do not directly relate to a producer.

| Address                               |               | Example Arguments                             | Description                       |
|---------------------------------------|---------------|-----------------------------------------------|-----------------------------------|
| /channel/\[0-9\]/stage/layer/\[0-9\]/ | time          | 101.24                                        | Seconds the layer has been active |
| frame                                 | 2531          | Time in frames that the layer has been active |
| type                                  | transition    |                                               |
| background/type                       | empty         |                                               |
| profiler/time                         | 0.39 <nowiki> | </nowiki> 0.4                                 | Actual <nowiki>                   |
| paused                                | True/False    | Whether the layer is paused or not            |

##### FFMPEG Producer

The messages below may be produced when an object utilising the [FFMPEG
Producer](CasparCG_Server#FFmpeg_Producer "wikilink") is in use on the
stage.

| Address                                    |                                              | Example Arguments                                                                                                                                              | Description                                                                                                       |
|--------------------------------------------|----------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| /channel/\[0-9\]/stage/layer/\[0-9\]/file/ | time                                         | 12 <nowiki>                                                                                                                                                    | </nowiki> 400                                                                                                     |
| frame                                      | 300 <nowiki>                                 | </nowiki> 10000                                                                                                                                                | Frames elapsed on file playback <nowiki>                                                                          |
| fps                                        | 25                                           | Framerate of the file being played                                                                                                                             |
| path                                       | AMB.mp4                                      | Filename and path (if file is in a sub-folder) of the media file, paths relative to the media folder defined in the \[\[CasparCG\_Server\#Paths\_Configuration | config file\]\]                                                                                                   |
| video/width                                | 1920                                         | Frame width of the video file                                                                                                                                  |
| video/height                               | 1080                                         | Frame height of the video file                                                                                                                                 |
| video/field                                | progressive                                  | Scan type of the video file, progressive or interlaced                                                                                                         |
| video/codec                                | H.264 /AVC                                   | Codec of the video file                                                                                                                                        |
| audio/sample-rate                          | 48000                                        | Audio [sample rate](http://en.wikipedia.org/wiki/Sampling_rate#Audio) of this files audio track                                                                |
| audio/channels                             | 2                                            | Number of channels in this files audio track                                                                                                                   |
| audio/format                               | s16                                          | Audio compression format, in this case uncompressed 16 bit PCM audio                                                                                           |
| audio/codec                                | Audio codec for the audio track in this file |                                                                                                                                                                |
| /channel/\[0-9\]/stage/layer/\[0-9\]/      | loop                                         | 1                                                                                                                                                              | Whether the file is set to loop playback or not, only applies to ffmpeg inputs of type file not stream or device. |

##### Flash Producer

The messages below may be produced when an object utilising the [Flash
Producer](CasparCG_Server#Flash_Producer "wikilink") is in use on the
stage.

| Address                                    |        | Example Arguments | Description |
|--------------------------------------------|--------|-------------------|-------------|
| /channel/\[0-9\]/stage/layer/\[0-9\]/host/ | path   | template\_file.ft |             |
| width                                      | 1920   |                   |
| height                                     | 1080   |                   |
| fps                                        | 50     |                   |
| /channel/\[0-9\]/stage/layer/\[0-9\]/      | buffer |                   |             |

### Mixer Messages

The majority of information from [ CasparCG's compositing
module](CasparCG_Server#Mixer_.28Transform.2FCompositing_Module.29 "wikilink")
is not yet made available via OSC (an
[issue](https://github.com/CasparCG/Server/issues/37) is open for this),
the only information available currently is audio related as listed
below.

| Address                       |              | Example Arguments   | Description                                                            |
|-------------------------------|--------------|---------------------|------------------------------------------------------------------------|
| /channel/\[0-9\]/mixer/audio/ | nb\_channels | 2                   | Number of audio channels in use on this \[\[CasparCG\_Server\#Channels |
| \[0-9\]/dBFS                  | -20          | Audio level in dBFS |

Example Clients
---------------

A couple of members of the CasparCG community have constructed simple
OSC example clients to demonstrate its use, these are available from the
links below. Pleaes note that in both cases these are basic examples of
how to use the OSC protocol and intended to help understand how it
works.

| Client                | Description                                                                                                                                                                                                                                                                                            | Forum post                                                                 | Download Links                                                                                                      |
|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------|
| Caspar CG OSC Monitor | Same name, different author to the below one, this application shows an implementation of the OSC Server in C\# allowing UDP messages generated from Caspar CG Server to be shown, a few filter options are provided to limit which messages are shown. This is tested against Caspar CG Server v2.0.7 | N/A                                                                        | <https://github.com/duncanbarnes/Caspar-CG-OSC-Monitor>                                                             |
| CasparCG OSC Monitor  | Andy Mace's OSC Monitor shows all OSC messages provided by CasparCG Server 2.1. Please note that this client was built when Caspar used TCP communication for OSC, as Caspar now uses UDP communication, this client currently does not work.                                                          | [CasparCG Forum](http://casparcg.com/forum/viewtopic.php?f=3&t=987&p=5656) | <https://github.com/CasparCG/Tools/tree/master/csharp/OSCMonitor>                                                   |
| Caspar Monitor        | This example by forum user [Interpreter](http://casparcg.com/forum/memberlist.php?mode=viewprofile&u=776) shows what's currently playing on channel 1 layer 1 on a Caspar server running on localhost.                                                                                                 | [CasparCG Forum](http://casparcg.com/forum/viewtopic.php?f=3&t=967&p=6748) | <http://www.monoscopio.se/downloads/CasparMonitor.zip> <http://www.monoscopio.se/downloads/CasparMonitorSource.zip> |

[Category:For Developers](Category:For_Developers "wikilink")
[Category:Communication
Protocol](Category:Communication_Protocol "wikilink")

---
title: Content and Media
permalink: Content_and_Media\
layout: wiki
---

Video
=====

Pre-rendered video and audio is normally played via the [FFmpeg
Producer](CasparCG_Server#FFmpeg_Producer "wikilink") directly from the
file system.

An alternative is to embed audio and/or video in an FT file and play
with the [Flash Producer](CasparCG_Server#Flash_Producer "wikilink").

Video Codecs
------------

The [FFmpeg Producer](CasparCG_Server#FFmpeg_Producer "wikilink") can
play all the codecs that [FFmpeg](http://ffmpeg.org/) supports.

Sample Videos
-------------

The [CasparCG Download page](http://casparcg.com/download/) has links to
several packages with sample video files.

------------------------------------------------------------------------

Flash Templates
===============

Dynamic media is played by the CasparCG Server’s [Flash
Producer](CasparCG_Server#Flash_Producer "wikilink"), so everything that
the [Adobe FlashPlayer](http://adobe.com/products/flashplayer/) can
handle can be played. This includes Flash animations
[SWF](#SWF "wikilink") and Flash video [FLV](#FLV "wikilink") as well as
audio, all via the [FT](#Generate_FT_File "wikilink") file (which is
just a [SWF](#SWF "wikilink") that has gone through
**[TemplateGenerator](TemplateGenerator "wikilink")**.)

Since you have full control over everything in Flash, you can
dynamically change texts and control individual animations, and all
[ActionScript](#Creating_Advanced_Flash_Templates "wikilink") execution
works (unlike when you import [SWF](#SWF "wikilink"):s into other tools
such as [Adobe After Effects](http://adobe.com/products/aftereffects/).)

Many existing [SWF](#SWF "wikilink"):s are built on the assumption that
they will be stored on a web server and embedded in a web page. If the
[SWF](#SWF "wikilink") requires any sort of interaction such as mouse
clicks, it won’t be very suited for CasparCG playback. However, all
features of the Flash Player (such as
[ActionScripted](#Creating_Advanced_Flash_Templates "wikilink")
downloading of RSS feeds, for example) is fully functional when played
by CasparCG.

An [FT](#Generate_FT_File "wikilink") file is just a Flash
*[SWF](#SWF "wikilink")* file that has gone through automated checks and
been renamed.

To prepare a Flash [SWF](#SWF "wikilink") file for playback by the
CasparCG Server, it normally goes through an automated process in the
[TemplateGenerator](#TemplateGenerator "wikilink") extension inside
[Adobe's Flash Professional](http://adobe.com/products/flash/) authoring
application.

Requirements for Creating Flash Templates
-----------------------------------------

To create a Flash template for CasparCG you will need the [Flash
Professional](http://adobe.com/products/flash) authoring environment
(CS4 and above recommended) and the
[TemplateGenerator](#TemplateGenerator "wikilink") panel.

Typical Workflow
----------------

-   Create your design in Adobe Photoshop, Adobe After Effects or some
    similar software.
-   Import the .psd into Flash
-   Select the text fields you wish to expose to CasparCG and give them
    instance names (make sure not to begin with the letter “x” since
    this will make CasparCG ignore this particular instance.)
-   Create your animations
-   Press the generate button in the
    [TemplateGenerator](#TemplateGenerator "wikilink") panel (make sure
    that you have entered your name in the panel.)

If everything is correct, the [.ft file](#Generate_FT_File "wikilink")
will bee generated and you are ready to test it in CasparCG Server.

Special Properties of Flash Templates
-------------------------------------

You can create labels in your main time line that can be accessed from
CasparCG with the `Invoke` command. If you create a label named “outro”,
CasparCG will automatically go to and play this label when a `Stop`
command is received. This is used to create outro animations. The
[\#TemplateGenerator](#TemplateGenerator "wikilink") will look for any
`stop()` command placed after the outro label. If found, the generator
will inject a `removeTemplate()` call on the same keyframe which will
remove the template from the TemplateHost. If there is no `Stop()`
command after the outro label, the removeTemplate() call will be
injected in the last frame of the layer that contains the outro label.
This is important to know, so that you make sure that this layer has its
last keyframe on the frame you want the template to be removed.

Interlacing via the [Flash Producer](CasparCG_Server#Flash_Producer "wikilink")
-------------------------------------------------------------------------------

By changing the [frame rate](#Frame_Rates "wikilink") in the [\#Flash
Document Properties](#Flash_Document_Properties "wikilink") inside
[Adobe's Flash Professional](http://adobe.com/products/flash/) to
*twice* the *normal* rate, the [Flash
Producer](CasparCG_Server#Flash_Producer "wikilink") and a
[consumer](CasparCG_Server#Consumers_.28Output_Modules.29 "wikilink")
will output interlaced dynamic output. Please see the tables in the
[\#Frame Rates](#Frame_Rates "wikilink") section for examples and
guidelines.

Best Practices for Flash Templates
----------------------------------

-   Try to avoid several instances with the same instance name since it
    can be hard to keep track of the project. If you create a timeline
    animation for a text field; convert the instance to a movie clip
    before you create the keyframes.
-   If you need to use
    [ActionScript](#Creating_Advanced_Flash_Templates "wikilink") in
    your template (other then basic `stop()` commands and alike), you
    should follow the guide lines in [\#Creating Advanced Flash
    Templates](#Creating_Advanced_Flash_Templates "wikilink").

Improving Performance of Flash Templates
----------------------------------------

Flash Player may be a CPU bottleneck when playing high resolution
channels. You might notice a major drop in performance between 720p5000
and 1080i5000 channels. One common solution is to let Flash Player run
at a lower resolution and then upscale the final render from Flash in
Caspar GPU Mixer. To do this, just override the TemplateHost section of
your config file. Here you can specify what TemplateHost to load for
each resolution. Below is an example of forcing 720p5000 Flash content
to be rendered at any 1080i5000 channel. The GPU upscaling is
automatically maintained.

<template-hosts>  
`   `<template-host>  
`       `<video-mode>`1080i5000`</video-mode>  
`       `<filename>`cg20.fth.720p5000`</filename>  
`       `<width>`1280`</width>  
`       `<height>`720</720>`  
`   `</template-host>  
</template-hosts>

You can actually override several template hosts this way within the
same config. Below is a complete section for overriding every
1080-format to corresponding 720-TemplateHost.

<template-hosts>  
`  `<template-host>  
`      `<video-mode>`1080i5000`</video-mode>  
`      `<filename>`cg20.fth.720p5000`</filename>  
`      `<width>`1280`</width>  
`      `<height>`720</720>`  
`  `</template-host>  
`  `<template-host>  
`      `<video-mode>`1080i5994`</video-mode>  
`      `<filename>`cg20.fth.720p5994`</filename>  
`      `<width>`1280`</width>  
`      `<height>`720</720>`  
`  `</template-host>  
`  `<template-host>  
`      `<video-mode>`1080i6000`</video-mode>  
`      `<filename>`cg20.fth.720p6000`</filename>  
`      `<width>`1280`</width>  
`      `<height>`720</720>`  
`  `</template-host>  
`  `<template-host>  
`      `<video-mode>`1080p2398`</video-mode>  
`      `<filename>`cg20.fth.720p2398`</filename>  
`      `<width>`1280`</width>  
`      `<height>`720</720>`  
`  `</template-host>  
`  `<template-host>  
`      `<video-mode>`1080p2400`</video-mode>  
`      `<filename>`cg20.fth.720p2400`</filename>  
`      `<width>`1280`</width>  
`      `<height>`720</720>`  
`  `</template-host>  
`  `<template-host>  
`      `<video-mode>`1080p2500`</video-mode>  
`      `<filename>`cg20.fth.720p2500`</filename>  
`      `<width>`1280`</width>  
`      `<height>`720</720>`  
`  `</template-host>  
`  `<template-host>  
`      `<video-mode>`1080p2997`</video-mode>  
`      `<filename>`cg20.fth.720p2997`</filename>  
`      `<width>`1280`</width>  
`      `<height>`720</720>`  
`  `</template-host>  
`  `<template-host>  
`      `<video-mode>`1080p3000`</video-mode>  
`      `<filename>`cg20.fth.720p3000`</filename>  
`      `<width>`1280`</width>  
`      `<height>`720</720>`  
`  `</template-host>  
`  `<template-host>  
`      `<video-mode>`1080p5000`</video-mode>  
`      `<filename>`cg20.fth.720p5000`</filename>  
`      `<width>`1280`</width>  
`      `<height>`720</720>`  
`  `</template-host>  
`  `<template-host>  
`      `<video-mode>`1080p6000`</video-mode>  
`      `<filename>`cg20.fth.720p6000`</filename>  
`      `<width>`1280`</width>  
`      `<height>`720</720>`  
`  `</template-host>  
`  `<template-host>  
`      `<video-mode>`1080i6000`</video-mode>  
`      `<filename>`cg20.fth.720p6000`</filename>  
`      `<width>`1280`</width>  
`      `<height>`720</720>`  
`  `</template-host>  
</template-hosts>

Generate FT File
----------------

To create the [.ft file](#Generate_FT_File "wikilink") file, you use the
[\#TemplateGenerator](#TemplateGenerator "wikilink").

3D in Flash
-----------

Using [Adobe Flash Player 10](http://adobe.com/products/flashplayer/)
and later, you can use Flash's built-in cameras and 2.5D abilities in
your Flash templates.

There are also 3rd party Flash 3D engines such as
[Papervision3D](http://papervision3d.org/) that can be used.

FLV
---

You can use [FLV](http://www.adobe.com/devnet/flv/) videos in [Flash
Templates](Category%3AFlash_Templates "wikilink") and by using the alpha
channel in the FLV you can have transparent pre-rendered video
controlled by Flash.

### FLV Pixel Aspect Ratio

Flash content played by [CasparCG\_Server\#Flash
Producer](CasparCG_Server#Flash_Producer "wikilink") is scaled
on-the-fly at render time (matched to the
[consumer](CasparCG_Server#Consumers_.28Output_Modules.29 "wikilink")
when the [Flash Producer](CasparCG_Server#Flash_Producer "wikilink") is
instanced,) so an [FT](#Generate_FT_File "wikilink") Flash template can
have either a **square pixel aspect ratio** or a **non-square pixel
aspect ratio** used in common broadcast resolutions for SD output.

If you are outputting to SDI via the [Bluefish
Consumer](CasparCG_Server#Bluefish_Consumer "wikilink"), you can choose
to set up your [FT](#Generate_FT_File "wikilink")'s [\#Flash Document
Properties](#Flash_Document_Properties "wikilink") to match:

-   *either* the final output resolution (for SD normally a **non-square
    pixel aspect ratio**)
-   *or* the **square pixel aspect ratio equivalent** and have the
    [Flash Producer](CasparCG_Server#Flash_Producer "wikilink") scale
    it.

This choice of Flash's [\#Flash Document
Properties](#Flash_Document_Properties "wikilink") resolution should be
made before choosing how to render the FLV video clips that will be
played in the [FT](#Generate_FT_File "wikilink"). We recommend working
with a **square pixel aspect ratio** size and (if necessary) have that
be squished to SD broadcast resolution on playback to SDI.

If you experience performance issues with Flash content, note that the
on-the-fly scaling comes at a performance penalty. To avoid scaling at
render time you can resize the Flash content (by scaling the [document
resolution](#Flash_Document_Properties "wikilink") and then scaling and
re-saving all imported bitmaps, FLVs, vector graphics and such in a
program outside Flash, and reimporting it.

While it is convenient to work with *square pixel aspect ratio* content,
there is currently a special case caused by a bug in
[http://adobe.com/products/flashplayer/ Flash
Player](http://adobe.com/products/flashplayer/_Flash_Player "wikilink").
It has a rounding calculation error that causes on-the-fly scaled output
that is animated horizontally to “jump” at certain intervals, making
movements such as ticker texts to appear less smooth.

Recommendation for PAR: Video non-square Flash square (with exception)

### Recommended FLV Render Settings

To increase the performance you should use lower compression in the FLV
since decompression is harder than reading a larger file from disk.

### FLV Limitations

Audio embedded in an [FLV](#FLV "wikilink") can currently only be
encoded with 44.1 kHz sampling rate, meaning that it will not match the
standard broadcast sampling rate of 48 kHz. For short sound effects,
this shouldn't cause any real problems, but beware when using longer FLV
videos with sound. An alternative solution would be the import the 48
kHz audio file into *Flash*, and sync it to the silent FLV video on the
timeline.

TemplateGenerator
-----------------

![The TemplateGenerator extension palette in
Flash](Ftgenerator.jpg "The TemplateGenerator extension palette in Flash")

### What the TemplateGenerator Does

The TemplateGenerator executes a script
[brew1\_8.jsf](http://github.com/CasparCG/Tools/blob/master/as3/TemplateGenerator/trunk/build/jsfl/brew2_0.jsfl)
that injects code into the current Flash project to make it compatible
with CasparCG Server. The project is then compiled and the
[SWF](#SWF "wikilink") is renamed to **.ft** which is a confirmation
that the file is prepared to be played by CasparCG Server.

It will scan the main timeline and the timeline of any MovieClip items
in the library and insert the following code at the end of each frame
where it finds an element of type 'text' which is dynamic and has an
instance name that doesn't begin with 'x'. The code will register the
component as a CasparTextField which is a CasparComponent.

From line 735 of
[brew1\_8.jsf](http://github.com/CasparCG/Tools/blob/master/as3/TemplateGenerator/trunk/build/jsfl/brew2_0.jsfl)

`thisFrame.actionScript +=  `“`\n(root`` ``as`` ``CasparTemplate).registerComponent(new`` ``CasparTextField(`”` + allElements[elementNr].name + `“`,`` ``Number(`”`+ allElements[elementNr].name + `“`.getTextFormat().letterSpacing)));`”

Note that he
[CasparTextField](http://sourceforge.net/p/casparcg/code/3801/tree/framework/as3/TemplateHost/trunk/src/se/svt/caspar/template/components/CasparTextField.as)
class will clear any text that is already set in the textfield when it
is constructed. This means if you want to add an instance name to a text
field so that you can manage it with ActionScript but don't want to be
able to update it from CasparCG Server with
[UPDATE](CasparCG_2.0_AMCP_Protocol#CG_UPDATE "wikilink"), you should
always add the **x** at the beginning of the instance name, so that this
code is not injected.

### TemplateGenerator Installation

The TemplateGenerator is distributed as an Adobe Flash extension (.mxp).
You will need the [Adobe Extension
Manager](http://www.adobe.com/exchange/em_download/) to be able to
install the extension. To install the TemplateGenerator just double
click on the TemplateGenerator.mxp file and it should open the Extension
Manager automatically. When you have installed the package it should
show up as “TemplateGenerator” in the Extension Manager. You will need
to restart flash (if it is open) before you can start to generate flash
templates. The TemplateGenerator panel can be found at Window -&gt;
Other Panels.

The extension will also automatically install the class library that is
needed to generate flash templates. The first time a flash template is
generated the TemplateGenerator will automatically add the class path to
this class library. If you are creating advanced flash templates and
uses an external code editor you might need to set this class path
manually. The library is installed at *$(LocalData)/Classes/Caspar*. The
$(LocalData) can be found at:

-   Windows: *Hard Disk\\Documents and Settings\\user\\Local
    Settings\\Application Data\\Adobe\\Adobe Flash
    CS4\\language\\Configuration\\*
-   Macintosh: *Hard Disk/Users/user/Library/Application
    Support/Adobe/Adobe Flash CS4/language/Configuration/*

Finally, the extension will install some project templates with some of
the common formats used in broadcast. You can use these when you create
a template from scratch by choosing
*File-&gt;New-&gt;Templates-&gt;Caspar*

### TemplateGenerator Installation on Mixed-language Systems

If you installed either the OS or Flash Professional in a non-US
language, the TemplateGenerator may install properly but you won't be
able to see it in Flash Professional. Please see [this forum thread for
a
solution](http://www.casparcg.com/forum/viewtopic.php?f=5&t=297&p=813#p810).

#### The Process

All modifications to the Flash project file is reverted when the
generation is finished. This is what happens to a Flash project during
the process:

-   All dynamic text fields in the project (any timeline) are exposed
    (converted to `CasparTextField`) so that they can be accessed from
    CasparCG. The font is automatically embedded. If there is a dynamic
    text field that has no instance name the script will throw an error.
    If the instance name begins with the letter “x” the field will not
    be exposed to CasparCG and it will be treated as a normal text
    field.
-   All CasparCG Flash components are exposed so that they can be
    accessed from CasparCG.
-   The document class is set to `se.svt.caspar.template.CasparTemplate`
    unless you have entered your own. In that case; be sure to use the
    `CasparTemplate` as the super class. Have a look at [\#Creating
    Advanced Flash
    Templates](#Creating_Advanced_Flash_Templates "wikilink") for more
    info.
-   A description of the template will be generated from the content in
    the document and the info in the panel.
-   Depending on your choices in the panel, certain objects will be
    optimized for CasparCG. (read more about this further down).

##### The Panel

There are some settings in the panel you should know about:

###### Generate FT File

When you press this button the process will begin with the settings
provided in the panel.

###### Author

Here you enter your name (required) and your e-mail (optional) for easy
backtracking. This information will be stored in the template
description.

###### Template info

Here you can enter information, e.g. a short description of the
template, that will be stored in the template description. This is
optional.

=======Optimization======= These are automatic optimization of different
objects that uses the best settings for rendering on CasparCG.

**Optimize images** If this option is checked (default) all images in
the project will have their compression set to “lossless” and the
property “allow smoothing” will be set to true.

**Optimize text fields** If this option is checked (default) all text
fields will be rendered on even pixels.

**Optimize embedded videos** If this option is checked (default) all
embedded videos will have the properties smoothing set to true and
deblocking set to 4.

###### Text Anti-aliasing

The global thickness and sharpness for all the text fields in the
document.

###### Limitations

-   If some unexpected error occurs when the script is executed there
    may be a possibility that the execution is halted before the project
    is reverted (since there is no try catch in jsfl). If you have a
    project where the generation does not work, check in the timeline(s)
    for injected code that may interfere with the process.

<!-- -->

-   Avoid working with scenes in flash since this is untested.

###### To do

Create a cache file for the template info.

###### Notes

This extension used to be called the **Brewer**, **CasparTools** and
**FT Generator** in previous versions.

------------------------------------------------------------------------

SWF
---

The CasparCG Server's [Flash
Producer](CasparCG_Server#Flash_Producer "wikilink") supports and plays
all types of **[SWF](#SWF "wikilink")** files, since it uses [Adobe's
FlashPlayer](http://adobe.com/products/flashplayer/) for rendering. That
includes all types of
[ActionScript](#Creating_Advanced_Flash_Templates "wikilink") methods
and commands.

Many existing [SWF](#SWF "wikilink")'s are built on the assumption that
they will be stored on a web server and embedded in a web page. If the
[SWF](#SWF "wikilink") requires any sort of interaction such as mouse
clicks, it won’t be very suited for CasparCG playback. However, all
features of the Flash Player (such as
[\#ActionScripted](#ActionScript "wikilink") downloading of RSS feeds,
for example) is fully functional when played by CasparCG.

To prepare a [SWF](#SWF "wikilink") file for playback by the CasparCG
Server, it normally goes through an automated process in the
[TemplateGenerator](#TemplateGenerator "wikilink") extension inside
[Adobe's Flash Professional](http://adobe.com/products/flash/) authoring
application, generating an [FT](#Generate_FT_File "wikilink") file
(which is just an *[SWF](#SWF "wikilink")* file that has gone through
[TemplateGenerator](#TemplateGenerator "wikilink") and been renamed.)

------------------------------------------------------------------------

Tutorial: Loading Dynamic Images in a Flash Template
----------------------------------------------------

Getting a Flash template to load and dynamically display bitmaps from
disk is very useful for lots of applications. Here's a quick explanation
on how to display different flags in a volleyball scoreboard.

[Download the source files used for this
tutorial](http://casparcg.com/wp-content/uploads/tutorials/Tutorial_Loading_Dynamic_Images_in_Flash_Templates.zip).

<img src="TeamStatistics.jpg" title="Preview of the Flash template with dynamically loaded flags" alt="Preview of the Flash template with dynamically loaded flags" width="686" />

Open the *Team\_Statistics.fla* file in Flash Professional, and you can
see that the dynamic text fields are just normal dynamic text with an
instance name. The flags are UILoader components:

<img src="Flash_Layout.png" title="The Flash template project" alt="The Flash template project" width="686" />

Open the *Component window* (Ctrl+F7) and create two **UILoader**s and
place and size them correctly. Remember to give them individual and
unique instance names. In the example project they are called **Image1**
and **Image2**, respectively.

Then create a new ActionScript file and let it extend *CasparTemplate*
and let the animation point to the class like this:

![Extend the CasparTemplate](Class.png "Extend the CasparTemplate")

Please refer to [Creating Advanced Flash
Templates](Content_and_Media#Creating_Advanced_Flash_Templates "wikilink")
for further details about extending *CasparTemplate*.

In the ActionScript file *TeamStatistics.as* (place it in the folder as
the .fla file) you have to have at least the following code (the text is
available in [the source
files](http://casparcg.com/wp-content/uploads/tutorials/Tutorial_Loading_Dynamic_Images_in_Flash_Templates.zip).)

![ActionScript file](Script.png "ActionScript file")

Important in the above code is line 20 and 24 respectively, where the
green texts are the variable names of the images. Use these variable
names to send the image filenames to the template. In line 22 and 26
respectively, the **UILoader** components get their source property set
to the name of the file. All the other dynamic text fields get their
data to the call on line 29.

Finally, you have to send the template variables formatted as a correct
URL. The file path

`D:\Beach_Volley\World_Flags_2013\World_Flags_BIG_40px\Switzerland.png`

...should be entered as:

[`file:///D:/Beach_Volley/World_Flags_2013/World_Flags_BIG_40px/Switzerland.png`](file:///D:/Beach_Volley/World_Flags_2013/World_Flags_BIG_40px/Switzerland.png)

------------------------------------------------------------------------

Creating Advanced Flash Templates
---------------------------------

To create an advanced Flash template for CasparCG you will need the
[Flash Professional](http://www.adobe.com/products/flash/) authoring
environment (CS4 and above recommended) and the
[TemplateGenerator](#TemplateGenerator "wikilink") panel.

For more information on how Flash templates are hosted inside CasparCG
Server, please see the
[TemplateHost](CasparCG_Server#TemplateHost "wikilink") section.

### The Document Class

The document class must extend the
`se.svt.caspar.template.CasparTemplate` class (or another class that
extends it). The `CasparTemplate` class contains methods and properties
used by CasparCG and you can override some of the default
implementation.

### Public Methods

### CasparTemplate()

= Creates a new CasparCG template.

#### Play():void

This method will be called from the
[TemplateHost](CasparCG_Server#TemplateHost "wikilink") when a `PLAY`
command is sent from CasparCG. By default it will try to play the main
time line in the Flash file (if there is no `stop()` on the first
frame). The template will not be visible until the `Play` command is
sent from CasparCG. To override this method do the following:

<code>` override public function Play():void`  
`{`  
`   //Your code here`  
`}`</code>

#### Stop():void

This method will be called from the
[TemplateHost](CasparCG_Server#TemplateHost "wikilink") when a `Stop`
command is sent from CasparCG. By default it will look in the main time
line if there is a label named “outro”. If not it will call the
`removeTemplate` method that marks the template for removal. If it finds
an outro label it will goto and play that label and when the outro is
ready the `removeTemplate` will be called. When you override this method
it is very important that you either call `super.Stop()` or
`removeTemplate()` when you are finished.

To override this method do the following:

*example 1:*

`override public function Stop():void `  
`{`  
`   //Your code here`  
`   //THEN`  
`   super.Stop();`  
`   //OR`  
`   removeTemplate();`  
`}`

*example 2:*

`override public function Stop():void `  
`{`  
`   myOutroFunction();   `  
`}`  
  
`private function myOutroFunction():void`  
`{`  
`   try`  
`   {`  
`      //Do some codebased animation…`  
`      myAnimationEngine.doMyOutroAnimation();`  
`      myAnimationEngine.onComplete(myCompleteFunction);`  
`   }`  
`   catch(e:Error)`  
`   {`  
`      myCompleteFunction();`  
`   }`  
`}`  
  
`private function myCompleteFunction():void`  
`{`  
`   super.Stop();`  
`   //OR`  
`   removeTemplate();`  
`}`

#### Next():void

This method will be called from the
[TemplateHost](CasparCG_Server#TemplateHost "wikilink") when a `Next`
command is sent from CasparCG. It is used to step forward in the
template. By default this method will make the template continue from
the current frame. The command will be ignored if the current frame
label is “outro”, then `Stop()` must be called. To override this method
do the following:

`override public function Next():void `  
`{`  
`  //Your code here`  
`}`

#### SetData(xmlData:XML):void

This method will be called from the
[TemplateHost](CasparCG_Server#TemplateHost "wikilink") when a `SetData`
command is sent from CasparCG. `SetData` is used to set data to the
components or parameters that are exposed in the
[FT](#Generate_FT_File "wikilink") file. The data is buffered by the
`ComponentDataBuffer` which makes it possible to set data to a component
that is not yet instantiated. When the component later is instantiated
it calls `registerComponent` (this is automatically done for all
CasparCG components on the stage) which then checks if there are any
buffered data for the component and it that case it sends the data to
it.

The XML format that is sent from CasparCG looks like this: <code>

<templateData>  
`  `<componentData id="instance1">  
`    `<data id="text" value="Text displayed in a CasparTextField" />  
`  `</componentData>  
`  `<componentData id="instance2">  
`    `<data id="imagePath" value="d:/caspar/_TEMPLATEMEDIA/myImage.png" />  
`    `<data id="alpha" value="0.6" />  
`  `</componentData>  
`  `<componentData id="customParameter1">  
`    `<data id="data" value="true" />  
`  `</componentData>  
</templateData></code>

“instance 1” is a regular `CasparTextField` and it takes one parameter
called “text” and sets the value in the attribute “value” to the text
parameter in the CasparTextField. “instance2” demonstrates a possible
CasparCG component that takes an image path and and an alpha value.
“customParameter1” is a custom parameter defined in the document class
and it will receive the value “true”.

Some of the reasons to override `SetData`:

-   You want to do stuff before data is set to the components
-   You want to know when a specific component is populated with data
-   You have code based custom parameters (read more about this in
    “Custom paramers”)

Here are some examples of overriding `SetData`:

*example 1*

`//This override checks if instance1 is getting data`  
`public override function SetData(xmlData:XML):void `  
`{    `  
`   //loop trough the xml `  
`   for each (var element:XML in xmlData.children())`  
`   {   `  
`      //check if the componentData atrribute `“`id`”` is `“`instance1`”  
`      if (element.@id == `“`instance1`”`)    `  
`      {`  
`         //if found and not a empty string…`  
`         if (element.data.@value != "")`  
`         { `  
`            //...call a function `“`setCurrentState`”` that accepts two parameters: the current state and the text received.               `  
`            setCurrentState(`“`Text`` ``received`”`, element.data.@value);       `  
`            break;`  
`         }`  
`      }    `  
`   }`  
`   //pass on the xmlData to the default implementation which will send it to the ComponentDataBuffer.`  
`   super.SetData(xmlData);   `  
`}`

*example 2*

`//This override check for data to the custom parameter `“`customParameter1`”  
`public override function SetData(xmlData:XML):void `  
`{    `  
`   //loop trough the xml `  
`   for each (var element:XML in xmlData.children())`  
`   {   `  
`      //check if the componentData atrribute `“`id`”` is `“`customParameter1`”  
`      if (element.@id == `“`customParameter1`”`)     `  
`      {`  
`         //if found, check if true or false`  
`         if (element.data.@value == `“`true`”`)`  
`            {  `  
`            //call a function `“`myFunction`”` and pass `“`true`”`.`  
`            myFunction(true);`  
`         }`  
`         else if(element.data.@value == `“`false`”`)`  
`         {`  
`            //call a function `“`myFunction`”` and pass `“`false`”`.`  
`            myFunction(false);`  
`         } `  
`         break;`  
`      }    `  
`   }`  
`   //pass on the xmlData to the default implementation which will send it to the ComponentDataBuffer.`  
`   super.SetData(xmlData);   `  
`}`

#### GotoLabel(label:String):void

\[final\] This method will be called from the
[TemplateHost](CasparCG_Server#TemplateHost "wikilink") when a `Invoke`
command is sent from CasparCG. It will first try this method, if it
generates an error the
[TemplateHost](CasparCG_Server#TemplateHost "wikilink") will try
`ExecuteMethod` instead. `GotoLabel` takes one parameter, a label, and
it will try to go to and play that label on the main timeline. If there
is no label with that name it will throw an error.

#### ExecuteMethod(methodName:String):void

\[final\] This method will be called from the
[TemplateHost](CasparCG_Server#TemplateHost "wikilink") when a `Invoke`
command is sent from CasparCG and the `GotoLabel` command generated an
error. It will try to execute a method on the template with the name
specified in “methodName”. If there is no method with that name it will
throw an error. To expose a method that can be executed via
`Invoke/ExecuteMethod` you simply create a public function in the
document class. The method does not take any parameters. If you need to
receive specific parameters you should use “Custom Parameters” that is
described further down.

#### GetDescription():String

\[final\] This method can be called by CasparCG and returns an XML
description of the template. The description is automatically generated
by the [TemplateGenerator](#TemplateGenerator "wikilink").

#### initialize(context:ITemplateContext):void

\[final\] This method is called when a template is instantiated and
provides the template with an instance of the `CommunicationManager` and
what layer the template is loaded on to.

#### postInitialize():void

This method is called by initialize right after initialization is
complete. Override this method to be able to access the
`CommunicationManager` and/or the layer.

*example 1*

`override public function postInitialize():void {`  
`{`  
`   communicationManager.sharedData.addSubscriber(this, `“`testData`”`));`  
`}`

#### registerComponent(instance:ICasparComponent):void

\[final\] This method is called by a component when initialized. It will
tell the `ComponentDataBuffer` that the instance is ready to receive
data.

#### dispose():void

\[final\] This method is called by the
[TemplateHost](CasparCG_Server#TemplateHost "wikilink") before the
template is removed. It will dispose all the loaded components.

#### preDispose():void

This method is called by dispose just before the components are
disposed. Override this method to be able to clean up everything you
need before the template is removed.

*example 1*

`override public function preDispose():void `  
`{`  
`   removeAllMyListeners();`  
`   stopAllTimers();`  
`   disposeAllMyObjects();`  
`   makeFinalNotifications();`  
`}`

### public properties

#### movieClip:MovieClip

\[final\]\[read-only\] A reference to the template as a `MovieClip`.
Used by the [TemplateHost](CasparCG_Server#TemplateHost "wikilink").

#### originalWidth:Number

\[final\] The original width of the template. This is set by
[TemplateGenerator](#TemplateGenerator "wikilink") and should normally
*not* be set manually. This property is used by the
[TemplateHost](CasparCG_Server#TemplateHost "wikilink").

#### originalHeight:Number

\[final\] The original height of the template. This is set by
[TemplateGenerator](#TemplateGenerator "wikilink") and should normally
*not* be set manually. This property is used by the
[TemplateHost](CasparCG_Server#TemplateHost "wikilink").

#### originalFrameRate:Number

\[final\] The original frame rate of the template. This is set by
[TemplateGenerator](#TemplateGenerator "wikilink") and should normally
*not* be set manually. This property is used by the
[TemplateHost](CasparCG_Server#TemplateHost "wikilink").

#### stopOnFirstFrame:Boolean

\[final\] A property that is true if there is a `stop()` on the first
frame on the main timeline. This is set by
[TemplateGenerator](#TemplateGenerator "wikilink") and should normally
*not* be set manually. This property is used by the
[TemplateHost](CasparCG_Server#TemplateHost "wikilink").

#### communicationManager:ICommunicationManager

\[final\]\[read-only\] This is a reference to the communication manager
and it is accessible in the `postInitialize` method.

#### description:XML

\[final\] This is a reference to the description XML that describes the
template. This is set by
[TemplateGenerator](#TemplateGenerator "wikilink") and should normally
*not* be set manually. This property is used by the
[TemplateHost](CasparCG_Server#TemplateHost "wikilink").

#### version:String

\[final\]\[read-only\] The version of the template. This is set by
[TemplateGenerator](#TemplateGenerator "wikilink") and should normally
*not* be set manually. This property is used by the
[TemplateHost](CasparCG_Server#TemplateHost "wikilink").

#### layer:int

\[final\]\[read-only\] The layer that the template is playing on. This
is set by the [TemplateHost](CasparCG_Server#TemplateHost "wikilink")
and it is accessible in the `postInitialize` method.

### Custom Parameters

If you need to use custom parameters you can expose them by adding the
following constant in your document class:

`private const customParameterDescription:XML =   `  
<parameters>  
`   `<parameter id="customParameter1" type="boolean" info="Description of the first parameter" />  
`   `<parameter id="customParameter2" type="number" info="Description of the second parameter" />  
</parameters>`;`

You will have to use the exact syntax described above for the
[TemplateGenerator](#TemplateGenerator "wikilink") to recognize it. The
parameters will be included in the template description and can then be
externally accessed. The parameter types should be set to either
boolean, number, string or xml.

*example 1*

`private const customParameterDescription:XML =   `  
<parameters>  
`   `<parameter id="resultData" type="xml" info="The data that is used to fill the result table" />  
</parameters>`;`  
`.`  
`.`  
`.`  
`override public function SetData(xmlData:XML):void `  
`{      `  
`   for each (var element:XML in xmlData.children())`  
`   {`  
`      if (element.@id == `“`resultData`”`)     `  
`      {`  
`         populateResultTable(element.data);    `  
`      }    `  
`   }`  
`   super.SetData(xmlData);   `  
`}`

### Creating A Custom Caspar Component

You can pass custom XML to your template. But it's suggested sticking to
the form of componentData and then putting your custom xml inside the
<componentData> tags because then in the flash template you can still
use the default setData implementation and still have the option to make
custom caspar component's, which would then receive your custom xml.
Instead of putting everything on the stage put it in a movieclip instead
which would then implement ICasparComponent (or you could do everything
from actionscript).

Here is the interface you need to implement:
[1](http://sourceforge.net/p/casparcg/code/3801/tree/framework/as3/TemplateHost/trunk/src/se/svt/caspar/template/components/ICasparComponent.as)

Simply put the content inside a class which extends Sprite or MovieClip
and implements ICasparComponent. Then it needs to implement a SetData
function which will receive the custom xml inside the componentData tag
which has an id matching the component id, a destroy function which
cancels any timers etc so that everything can be garbage collected, and
a “get name()” function which should return the string which should be
the id that you use to send data to it in the <componentData id=?> tag.

Then in the document class you just need to do something like:

`var component:ICasparComponent = new ComponentClassName();`  
`addChild(component);`  
`registerComponent(component);`

The registerComponent function is part of the CasparTemplate class and
is what enables the relevant template data to be sent to the relevant
caspar component.

The advantages of doing it this way are that the ComponentDataBuffer
will still be used, and that it is easier to add more components to the
same template later on if you wanted, or transfer components between
different projects.

The component data buffer holds back any xml that it has received with
an id that hasn't been registered with registerComponent() and then
sends it after registerComponent() has been called. So it means if a
component isn't loaded until after a few frames and data has already
been sent to it, its SetData function will receive it immediately after
registerComponent() has been called and it won't get lost.

### Communication Manager

More information soon...

### Best Practices

-   Before you deliver a .ft file you should omit all you traces.
-   If you have custom parameters you should define them as described
    above.
-   Remove any instances that are not used.

### Generate FT File

To create the [.ft file](#Generate_FT_File "wikilink") you use the
[TemplateGenerator](#TemplateGenerator "wikilink").

------------------------------------------------------------------------

Creating Templates without using Flash Professional
---------------------------------------------------

One of the great things with CasparCG is that it is free, with the
exception that you are forced to use Adobe Flash Professional to create
your templates, until now! Well, actually, this have always been
possible to do but has not been very clear.

**WHAT YOU'LL NEED**

1. You will need [Flash Develop](http://www.flashdevelop.org/) since
this example is created in this wonderful free editor. The project is
compiled with the Flex compiler which is installed together with the
program. Just press Ctrl + Enter as usual.

2. Basic coding skills in AS3. The code is pretty well commented but you
will need to know some of the basics.

**WHAT YOU'LL GET**

1. A pretty ugly template that you can play with.

2. Hopefully a better understanding of how to create templates without
using the CasparCG TemplateGenerator.

3. An example on how to use the inbuilt component flow to create custom
components so you don't need to override SetData and doing all the
xml-parsing in the document class.

SOME NOTES As you might notice the output file is suffixed with **.ft**.
This is not the act of magic as with the CasparCG
[TemplateGenerator](TemplateGenerator "wikilink"), it is merely a
setting in the properties for the project. I have just changed the
“[SWF](#SWF "wikilink")” to “ft”.

I hope this will make CasparCG even more accessible than before! Of
course you can use any editor/IDE with this method, it's just that I
prefer Flash Develop.

[Download example file for Flash
Develop](http://sourceforge.net/projects/casparcg/files/Flash_Templates/TemplateWithoutFlashPro.zip/download)

------------------------------------------------------------------------

The HTML Producer
=================

Since CasparCG Server 2.0 there is an HTML producer. The HTML producer
is a modern alternative to flash templates and it allows CasparCG to
display any html page.

------------------------------------------------------------------------

How it works
------------

The HTML producer can either load html pages from a url but it can also
treat HTML files just like it treats Flash templates. Because there is
no template generator for HTML files you will have to program any
interaction between CasparCG Server and your template via JavaScript.

Working with JavaScript
-----------------------

CasparCG server will communicate with your template via the following
javascript functions.

### play()

This function will be called by CasparCG to instruct the template to
start playing.

### stop()

This function will be called by CasparCG to instruct the template it
should stop playing. In the flash producer the template will be removed
after this is called but in the html producer there is no such feature.

### update(templatedata)

This function will be called by CasparCG to send any templatedata as a
string to the template. The templatedata is formatted the same way as in
flash.

### next()

This function can be used by CasparCG to control animations that have
multiple discreet steps.

The advantages
--------------

A forum topic about flash vs. html can be found
[here](http://casparcg.com/forum/viewtopic.php?f=5&t=4015). If you want
to contribute to the discussion, please keep it civil!

Some advantages are:

1. The big community of web developers make it easy to receive help and
find information

2. HTML templates can depend on many open source libraries such as those
found on npm

3. HTML is very future proof and will be supported for a long time to
come

4. If you come from a web development background you will be up and
running with CasparCG in no time!

The caveats
-----------

1. The current HTML implementation in CasparCG 2.0.7 is quite old and
misses support for CSS 3d transformations

2. No support for font sharpening, although SVG fonts can be a solution.

3. If you use dependencies, templates will span multiple files. This can
be bothersome when developing for commercial parties.

4. MP3 and MP4 formates are not supported.

------------------------------------------------------------------------

Images
======

CasparCG Server 2.0 can play out all the RGB and RGBA image formats that
the [FreeImage](http://freeimage.sourceforge.net/features.html)
component can play.

Make sure that you convert CMYK images to RGB before trying to play them
with CasparCG Server.

With CasparCG Server's [Image
Scroll](CasparCG_Server#Image_Scroll "wikilink") you can automatically
animate bitmaps with horizontal or vertical scrolling.

------------------------------------------------------------------------

Audio
=====

Sampling Rate
-------------

-   CasparCG Server 2.0.3 and earlier works with 32-bit, 2-channel
    stereo sound.
-   Embedded SDI audio via BlackMagic Design's cards is 32-bit,
    2-channel stereo sound.
-   Embedded SDI audio via Bluefish cards is 16-bit, 2-channel stereo
    sound.
-   System audio (via the computer's sound card) is 16-bit, 2-channel
    stereo sound.

The preferred sampling rate of audio is 48 kHz which conforms with
broadcast standards, however other sampling rates, such as 44.1 kHz (CD
Audio) also work.

**Audio inputs that are not 48 kHz, 32-bit stereo are automatically
transcoded in real-time.**

Audio Producers
---------------

The FFmpeg producer and the DeckLink producer can both handle audio.

The FFmpeg producer tries to determine the channel layout of the source
file, but sometimes cannot do so correctly. Therefore the possibility to
specify the channel layout of a file exists. It can be done like this:

`PLAY 1-0 surround_file CHANNEL_LAYOUT SMPTE`

...or by using a custom on-the-fly generated channel layout:

`PLAY 1-0 surround_file CHANNEL_LAYOUT `“`5.1:Ls`` ``Rs`` ``L`` ``R`` ``C`` ``LFE`”

The DeckLink producer by default assumes “stereo” as the channel layout,
but can be told to use another layout:

`PLAY 1-0 DECKLINK DEVICE 2 FORMAT 720p5000 CHANNEL_LAYOUT SMPTE`

...or a custom layout with the same syntax as expected by the FFmpeg
producer:

`PLAY 1-0 DECKLINK DEVICE 2 FORMAT 720p5000 CHANNEL_LAYOUT `“`5.1:Ls`` ``Rs`` ``L`` ``R`` ``C`` ``LFE`”

Audio Consumers
---------------

All consumers receive audio data in the channel layout used by the audio
mixer, but can convert it to another layout. Here is how the relevant
(those consuming audio in any way) consumers handles incoming audio:

-   **system-audio** Always converts to stereo
-   **decklink** Defaults to stereo but can be told to use another via
    the <channel-layout> element in *casparcg.config* or the
    CHANNEL\_LAYOUT parameter. Note that the actual output on the SDI
    cable will always be 2 channels, 8 channels or 16 channels. So for
    example a 5.1 channel layout will cause 8 channels to be sent but
    with the two last channels silent. In the case of mono, channel 1
    will be duplicated to channel 2.
-   **bluefish** Defaults to stereo but can be told to use another via
    the <channel-layout> element in casparcg.config or the
    CHANNEL\_LAYOUT parameter.
-   **ffmpeg** Always uses the same audio layout as the audio mixer.
    Note that the default audio codec for a given container type might
    not support the encoding of more than 2 channels, in which case the
    FFmpeg consumer will fail to initialize. A custom **-acodec** must
    be given in those cases which supports the number of channels used
    by the video channel.

Audio Channels
--------------

2-channel stereo is supported in CasparCG Server 2.0.3 and earlier.

Mono and up to 16 channel audio input and output is supported from
CasparCG Server version 2.0.4.

### Audio Channel Layouts

The audio pipeline works with a concept called channel layouts. It is a
mapping between a numbered audio channel and the corresponding speaker
it is supposed to be directed to. The following are the predefined
layouts in CasparCG Server 2.0.4 and later:

`============================================================`  
`Name         Type          Layout`  
`------------ ------------- ---------------------------------`  
`mono         1.0           C`  
`stereo       2.0           L R`  
`dts          5.1           C L R Ls Rs LFE`  
`dolbye       5.1+stereomix L R C LFE Ls Rs Lmix Rmix`  
`dolbydigital 5.1           L C R Ls Rs LFE`  
`smpte        5.1           L R C LFE Ls Rs`  
`passthru     16ch          (will just pass everything as is)`  
`============================================================`

The channel layout type like for example “5.1” is a string selected by
convention to describe what set of channels to expect in the layout.
This means that for example dts and smpte are directly translatable,
just by rearranging the order of the channels.

The channel layout concept is used in the following areas:

-   The frames produced by a producer has a channel layout.
-   The audio mixer mixes in a specific channel layout. The mixer
    automatically converts incoming audio to the correct channel layout
    if necessary before mixing.
-   A consumer might convert the frames delivered from the audio mixer
    to any other channel layout before outputting the audio.

A channel layout can be redefined or added by adding an element in
casparcg.config in the configuration/audio/channel-layouts element. The
element name is used as the layout name:

` `<channel-layouts>  
`   `<quad>  
`     `<type>`4.0`</type>  
`     `<num-channels>`4`</num-channels>  
`     `<channels>`L R Ls Rs`</channels>  
`   `</quad>  
` `</channel-layouts>

### Rearranging and up/down mixing

If a file played by the FFmpeg producer has the channel layout
“dolbydigital” and the audio\_mixer channel layout is “smpte”, the audio
mixer will recognise that no upmixing or downmixing is required to adapt
the incoming data on the FFmpeg layer (both “dolbydigital” and “smpte”
share the same layout type “5.1”), so it just rearranges the channel
order:

` 0 => 0 (L)`  
` 1 => 2 (C)`  
` 2 => 1 (R)`  
` 3 => 4 (Ls)`  
` 4 => 5 (Rs)`  
` 5 => 3 (LFE)`

If on the other hand the layouts are of different types, a so called mix
config will be used in order to determine how to convert the audio
channels.

### Audio Mix Configs

A **mix config** specifies how to convert audio of a channel layout type
(for example “5.1”) to another layout type (for example “2.0”). The
default mix config for “5.1 =&gt; 2.0” looks like this (configurable in
casparcg.config):

` `<mix-configs>  
`   `<mix-config>  
`     `<from>`5.1`</from>  
`     `<to>`2.0`</to>  
`     `<mix>`average`</mix>  
`     `<mappings>  
`       `<mapping>`L  L 1.0`</mapping>  
`       `<mapping>`R  R 1.0`</mapping>  
`       `<mapping>`C  L 0.707`</mapping>  
`       `<mapping>`C  R 0.707`</mapping>  
`       `<mapping>`Ls L 0.707`</mapping>  
`       `<mapping>`Rs R 0.707`</mapping>  
`     `</mappings>  
`   `</mix-config>  
` `</mix-configs>

The mix-element determines what strategy should be used when multiple
source channels are mixed into a single destination channel. Two
strategies are available:

-   add -- Adds the samples together (can cause clipping).
-   average -- Uses the average of all source samples (can cause too low
    volume).\[/list\]

Each mapping defines the source channel and the destination channel as
well as the “influence” the source channel should have on the
destination channel. Lowering the “influence” can be useful to help
avoiding clipping when the “add” strategy is used.

Audio Output
------------

With the [DeckLink
Consumer](CasparCG_Server#DeckLink_Consumer "wikilink") you can decide
if you want audio output via HDMI, SDI or the audio jacks of the
card(s).

With the [Screen Consumer](CasparCG_Server#Screen_Consumer "wikilink"),
audio is played via the regular audio output of the machine.

With the [Bluefish
Consumer](CasparCG_Server#Bluefish_Consumer "wikilink"), audio is either
played via the regular audio output of the machine, or embedded in the
SDI stream.

Audio Limitations
-----------------

Audio embedded in an [FLV](#FLV "wikilink") can currently only be
encoded with 44.1 kHz sampling rate, meaning that it will not match the
standard broadcast sampling rate of 48 kHz. For short sound effects,
this shouldn't cause any real problems, but beware when using longer FLV
videos with sound. An alternative solution would be the import the 48
kHz audio file into *Flash* as a separate audio file, and sync it to the
silent video on the same timeline.

The biggest drawback of using audio inside a Flash template is that it
can only be played through the system audio output, which means it will
not be embedded in SDI/HDMI output, and it will not be in perfect sync.
If you need a Flash template to play audio and/or video, we recommend
you use the **CasparCG AS3 Server Connection** library (available from
the Download page) to have the Flash template send the appropriate AMCP
commands to load and play audio (and/or video) through the FFmpeg
producer.

------------------------------------------------------------------------

Frame Rate and Resolution
=========================

The frame rate and resolution used for a channel is primarily set in the
[CasparCG Server configuration](CasparCG_Server#Channels "wikilink"),
but it must also match the content your are playing. The frame rate and
resolution of content is changed:

-   In the [Bluefish Feature
    App](CasparCG_Server#Bluefish_Feature_App "wikilink") if you are
    using the [Bluefish
    Consumer](CasparCG_Server#Bluefish_Consumer "wikilink") for [SDI
    output](CasparCG_Server#Consumers_.28Output_Modules.29 "wikilink").
-   In the DeckLink control panel if you are using the [DeckLink
    Consumer](CasparCG_Server#DeckLink_Consumer "wikilink") for [SDI
    output](CasparCG_Server#Consumers_.28Output_Modules.29 "wikilink").
-   In the pre-rendered video file if played by the [FFmpeg
    Producer](CasparCG_Server#FFmpeg_Producer "wikilink").
-   In the Flash Professional's **Document Properties** if you are
    making an [FT](#Generate_FT_File "wikilink") for playback via the
    [Flash Producer](CasparCG_Server#Flash_Producer "wikilink").

The maximum frame rate that a CasparCG system is capable of depends on
many factors such as resolution, frame rate, bit rate,
[codec](#Video_Codecs "wikilink") and which
[producers](CasparCG_Server#Producers_.28Input_Modules.29 "wikilink")
and
[consumers](CasparCG_Server#Consumers_.28Output_Modules.29 "wikilink")
you use, plus the performance of your
[hardware](CasparCG_Server#Supported_Hardware "wikilink").

Real-time Scaling via the [Flash Producer](CasparCG_Server#Flash_Producer "wikilink")
-------------------------------------------------------------------------------------

Flash templates are automatically scaled to the resolution determined by
the
[consumer](CasparCG_Server#Consumers_.28Output_Modules.29 "wikilink")
used. That means you can work in square-pixel documents in Flash and
they are scaled properly to fit the
[consumer](CasparCG_Server#Consumers_.28Output_Modules.29 "wikilink").
This also means that as long as you are only using vector graphics and
text, you can reuse SD templates in HD outputs.

If you don't have 1 to 1 ratio between your video resolution and the
template resolution, Flash will scale it, **which costs a lot of
performance**. Instead you can use the [Mixer
Module](CasparCG_Server#Mixer_Module "wikilink") to scale it for you on
the GPU. Add the following lines to your config. Don't forget to replace
FORMAT and RESOLUTION:

<template-hosts>  
`  `<template-host>  
`           `<video-mode>`1080i5000`</video-mode>  
`           `<filename>`cg20.fth.`**`FORMAT`**</filename>  
`           `<width>**`RESOLUTION`**</width>  
`           `<height>**`RESOLUTION`**</height>  
`       `</template-host>  
</template-hosts>

**FORMAT** = One of the [CasparCG Server's supported resolution and
frame rates](CasparCG_Server#Channels_Configuration "wikilink").

**RESOLUTION** = Your template's resolution.

Flash Content Output to SDI or HDMI
-----------------------------------

For [Flash content](#Generate_FT_File "wikilink") played by the [Flash
Producer](CasparCG_Server#Flash_Producer "wikilink") and output to an
[SDI or HDMI
consumer](CasparCG_Server#Consumers_.28Output_Modules.29 "wikilink")
that is capable of [interlaced](#Interlacing "wikilink") output:

|                                                                                                                                                                    |                                                   |                        |                    |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------|------------------------|--------------------|
| **Expected Playback Frame Rate and Mode:**                                                                                                                         | **Set the Flash Document Properties Frame Rate:** | '''Render \[\[\#FLV    | FLV\]\]s as:'''    |
| **23.976p**                                                                                                                                                        | 23.976 fps                                        | 23.976 progressive fps |
| **24p** (film / movie /cinema for progressive playback)                                                                                                            | 24 fps                                            | 24 progressive fps     |
| **25p** (progressive PAL SD *576p25*&lt;                                                                                                                           | --, HD *720p25*--&gt; and HD *1080p25*)           | 25 fps                 | 25 progressive fps |
| **29.976p** (progressive NTSC SD *480p29.976*, HD *720p29.976* and HD *1080p29.976*) <small>(sometimes mistakenly called *480p30*, *720p30* and *1080p30*)</small> | 29.976 fps                                        | 29.976 progressive fps |
| **50p** (progressive PAL SD *576p50*, *720p50* and *1080p50*)                                                                                                      | 50 fps                                            | 50 progressive fps     |
| **50i** (interlaced PAL SD *576i50* and *1080i50*)                                                                                                                 | 50 fps                                            | 50 progressive fps     |
| **59.94p** (progressive NTSC SD *480p59.94*, HD *720p59.94* and HD *1080p59.94*) <small>(sometimes mistakenly called *480p60*, *720p60* and *1080p60*)</small>     | 59.94 fps                                         | 59.94 progressive fps  |
| **59.94i** (interlaced NTSC SD *480i59.94*, HD *720i59.94* and HD *1080i59.94*) <small>(sometimes mistakenly called *480i60*, *720i60* and *1080i60*)</small>      | 59.94 fps                                         | 59.94 progressive fps  |

### Flash Content Output to Screen

For [Flash content](#Generate_FT_File "wikilink") played by the [Flash
Producer](CasparCG_Server#Flash_Producer "wikilink") and output to an
non-[interlaced](#Interlacing "wikilink")
[consumer](CasparCG_Server#Consumers_.28Output_Modules.29 "wikilink")
such as the [Screen
Consumer](CasparCG_Server#Screen_Consumer "wikilink"):

|                                                                                                                                                                    |                                                   |                        |                 |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------|------------------------|-----------------|
| **Expected Playback Frame Rate and Mode:**                                                                                                                         | **Set the Flash Document Properties Frame Rate:** | '''Render \[\[\#FLV    | FLV\]\]s as:''' |
| **23.976p**                                                                                                                                                        | 23.976 fps                                        | 23.976 progressive fps |
| **24p** (film / movie /cinema for progressive playback)                                                                                                            | 24 fps                                            | 24 progressive fps     |
| **25p** (progressive PAL SD *576p25*, HD *720p25* and HD *1080p25*)                                                                                                | 25 fps                                            | 25 progressive fps     |
| **29.976p** (progressive NTSC SD *480p29.976*, HD *720p29.976* and HD *1080p29.976*) <small>(sometimes mistakenly called *480p30*, *720p30* and *1080p30*)</small> | 29.976 fps                                        | 29.976 progressive fps |
| **50p** (progressive PAL SD *576p50*, *720p50* and *1080p50*)                                                                                                      | 50 fps                                            | 50 progressive fps     |
| **59.94p** (progressive NTSC SD *480p59.94*, HD *720p59.94* and HD *1080p59.94*) <small>(sometimes mistakenly called *480p60*, *720p60* and *1080p60*)</small>     | 59.94 fps                                         | 59.94 progressive fps  |

### Video Content Output to SDI, HDMI or Screen

For video content played by the [FFmpeg
Producer](CasparCG_Server#FFmpeg_Producer "wikilink") and output to an
[SDI or HDMI
consumer](CasparCG_Server#Consumers_.28Output_Modules.29 "wikilink")
that is capable of [interlaced](#Interlacing "wikilink") output:

|                                                                                                                                                                    |                                         |                                                                                                                                               |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------|
| **Expected Playback Frame Rate and Mode:**                                                                                                                         | **Render Video as:**                    | **Render Video with Field Dominance:**                                                                                                        |
| **23.976p**                                                                                                                                                        | 23.976 fps                              | Progressive                                                                                                                                   |
| **24p** (film / movie /cinema for progressive playback)                                                                                                            | 24 fps                                  | Progressive                                                                                                                                   |
| **25p** (progressive PAL SD *576p25*&lt;                                                                                                                           | --, HD *720p25*--&gt; and HD *1080p25*) | 25 fps                                                                                                                                        |
| **29.976p** (progressive NTSC SD *480p29.976*, HD *720p29.976* and HD *1080p29.976*) <small>(sometimes mistakenly called *480p30*, *720p30* and *1080p30*)</small> | 29.976 fps                              | Progressive                                                                                                                                   |
| **50p** (progressive PAL SD *576p50*, *720p50* and *1080p50*)                                                                                                      | 50 fps                                  | Progressive                                                                                                                                   |
| **50i** (interlaced PAL SD *576i50* and *1080i50*)                                                                                                                 | 25 fps                                  | Interlaced **upper/odd** (unless rendering to a [DV](http://en.wikipedia.org/wiki/DV) codec, in which case you should render *'lower/even*.') |
| **59.94p** (progressive NTSC SD *480p59.94*, HD *720p59.94* and HD *1080p59.94*) <small>(sometimes mistakenly called *480p60*, *720p60* and *1080p60*)</small>     | 59.94 fps                               | Progressive                                                                                                                                   |
| **59.94i** (interlaced NTSC SD *480i59.94*, HD *720i59.94* and HD *1080i59.94*) <small>(sometimes mistakenly called *480i60*, *720i60* and *1080i60*)</small>      | 29.976 fps                              | Interlaced **upper/odd** (unless rendering to a [DV](http://en.wikipedia.org/wiki/DV) codec, in which case you should render **lower/even**.) |

------------------------------------------------------------------------

Interlacing
===========

Interlaced Content
------------------

You can have interlaced output of both dynamic Flash content and
pre-rendered videos and images to SDI, HD-SDI or HDMI via several
available
[consumers](CasparCG_Server#Consumers_.28Output_Modules.29 "wikilink").

De-interlacing
--------------

CasparCG Server 2.0 supports
[yadif](http://avisynth.org.ru/yadif/yadif.html) de-interlacing for the
FFmpeg producer and the DeckLink producer. Yadif de-interlacing
*“...checks pixels of previous, current and next frames to re-create the
missed field by some local adaptive method (edge-directed interpolation)
and uses spatial check to prevent most artifacts”*.

De-interlacing is automatically enabled when playing clips with
incompatible encoding, e.g. 1080i50 -&gt; 1080p50, 720p50 -&gt; 1080i50.

It is possible to manually activate de-interlacing for clips by
providing the extra arguments to the LOAD and PLAY commands with a
**FILTER YADIF=1:-1** command, for example:

`LOADBG 1-1 MYCLIP FILTER YADIF=1:-1`

Interlacing
===========

Interlaced Content
------------------

You can have interlaced output of both dynamic Flash content and
pre-rendered videos and images to SDI, HD-SDI or HDMI via several
available
[consumers](CasparCG_Server#Consumers_.28Output_Modules.29 "wikilink").

De-interlacing
--------------

CasparCG Server 2.0 supports
[yadif](http://avisynth.org.ru/yadif/yadif.html) de-interlacing for the
FFmpeg producer and the DeckLink producer. Yadif de-interlacing
*“...checks pixels of previous, current and next frames to re-create the
missed field by some local adaptive method (edge-directed interpolation)
and uses spatial check to prevent most artifacts”*.

De-interlacing is automatically enabled when playing clips with
incompatible encoding, e.g. 1080i50 -&gt; 1080p50, 720p50 -&gt; 1080i50.

It is possible to manually activate de-interlacing for clips by
providing the extra arguments to the LOAD and PLAY commands with a
**FILTER YADIF=1:-1** command, for example:

`LOADBG 1-1 MYCLIP FILTER YADIF=1:-1`

Chroma Subsampling
==================

All rendering in all
[producers](CasparCG_Server#Producers_.28Input_Modules.29 "wikilink") is
done at 8-bit [color depth](#Color_Depth "wikilink") in RGB [color
levels](#Color_Levels "wikilink") and with 4:4:4 chroma sampling.
Content with lower *chroma subsampling* will of course be played
“as-is.”

Read more about [Chroma
Subsampling](http://en.wikipedia.org/wiki/Chroma_subsampling) at
Wikipedia.

Pixel-aspect Ratio
==================

Read more about [**pixel aspect ratio** at
Wikipedia](http://en.wikipedia.org/wiki/Pixel_aspect_ratio).

Bit Depth
=========

Each
[producer](CasparCG_Server#Producers_.28Input_Modules.29 "wikilink") and
[consumer](CasparCG_Server#Consumers_.28Output_Modules.29 "wikilink")
has its own bit depth limitation. If you try to play/render content with
a higher bit depth (for example 10-bit DPX, 16-bit TIFF or 32-bit EXR)
you will not get predictable or even working results.

Currently we recommend you use 8 bpc (24-bit plus optional 8-bit alpha
channel) content for best performance and stable operation.

Producer Bit Depths
-------------------

-   The [Flash Producer](CasparCG_Server#Flash_Producer "wikilink") is
    currently limited to **8 bits per RGB channel plus an 8 bit
    alpha/key**, since this is a limitation in Adobe’s FlashPlayer.

<!-- -->

-   The [FFmpeg Producer](CasparCG_Server#FFmpeg_Producer "wikilink") is
    limited to **8 bits per RGB channel plus an 8 bit alpha/key**, since
    this is a current limitation of the [FFmpeg](http://FFmpeg.org/)
    library.

<!-- -->

-   The [Image Producer](CasparCG_Server#Image_Producer "wikilink") is
    limited to **8 bits per RGB channel plus an 8 bit alpha/key**.

<!-- -->

-   The [Color Producer](CasparCG_Server#Color_Producer "wikilink") is
    limited to **8 bits per RGB channel**.

Consumer Bit Depths
-------------------

-   The [Bluefish
    Consumer](CasparCG_Server#Bluefish_Consumer "wikilink") currently
    supports **8 bits per channel rendering for RGB/fill, plus an 8 bit
    alpha/key**, even though the Bluefish cards support 10 bit output.

<!-- -->

-   The [Screen Consumer](CasparCG_Server#Screen_Consumer "wikilink")
    currently supports 8 bits per channel rendering for RGB/fill and no
    key/alpha channel.

Color Levels
============

All rendering in all
[producers](CasparCG_Server#Producers_.28Input_Modules.29 "wikilink") is
done at 8-bit RGB levels, meaning *R0 B0 G0* is black and *R255 G255
B255* is white.

This makes it easier to work with and preview content on a computer
monitor, and facilitates using the same content for both broadcast and
web consumption.

To comply with broadcast standards such as [CCIR
601](http://en.wikipedia.org/wiki/CCIR_601) and [Rec.
709](http://en.wikipedia.org/wiki/Rec._709) the 0-255 RGB levels
rendered by the
[producers](CasparCG_Server#Producers_.28Input_Modules.29 "wikilink") is
automatically converted to the correct broadcast format (for example a
range of 16-235) by the
[consumer](CasparCG_Server#Consumers_.28Output_Modules.29 "wikilink")
based on the setting in the
[CasparCG\_Server\#Card\_Configuration](CasparCG_Server#Card_Configuration "wikilink").

Color Space
===========

All content must be in **RGB** color space. Content in **CMYK** color
space is **not** supported in any current
[producers](CasparCG_Server#Producers_.28Input_Modules.29 "wikilink").

Conversion from the internal RGB colorspace to output color space is
handled by each consumer. For broadcast colorspace YCbCr, Rec 601, Rec
709 and other broadcast colorspaces is handled by the [supported video
output cards](CasparCG_Server#SDI_and_HDMI_Cards "wikilink").

Embedded Color Profiles
-----------------------

Currently, no
[producers](CasparCG_Server#Producers_.28Input_Modules.29 "wikilink")
read or use any embedded color space profiles.

Color Depth
===========

Rendering Bit Depth Limitations
-------------------------------

Each
[producer](CasparCG_Server#Producers_.28Input_Modules.29 "wikilink") and
[consumer](CasparCG_Server#Consumers_.28Output_Modules.29 "wikilink")
has an individual bit depth limitation. If you try to play/render
content with a higher bit depth (for example 10-bit DPX, 16-bit TIFF or
32-bit EXR) you will not get predictable or even working results.

### Producer Bit Depths

-   The [Flash Producer](CasparCG_Server#Flash_Producer "wikilink") is
    currently limited to **8 bits per RGB channel plus an 8 bit
    alpha/key**, since this is a limitation in Adobe’s FlashPlayer.

<!-- -->

-   The [FFmpeg Producer](CasparCG_Server#FFmpeg_Producer "wikilink") is
    limited to **8 bits per RGB channel plus an 8 bit alpha/key**, since
    this is a current limitation of the [FFmpeg](http://FFmpeg.org/)
    library.

<!-- -->

-   The[CasparCG\_Server\#Image\_Producer](CasparCG_Server#Image_Producer "wikilink")
    is limited to **8 bits per RGB channel plus an 8 bit alpha/key**.

<!-- -->

-   The [Color Producer](CasparCG_Server#Color_Producer "wikilink") is
    limited to **8 bits per RGB channel**.

File Names
==========

You are advised to keep file names used with all the
[producers](CasparCG_Server#Producers_.28Input_Modules.29 "wikilink")
short, using only standard Latin characters and numerals. Even though
most support Unicode characters, there are some limitations in the
[FFmpeg Producer](CasparCG_Server#FFmpeg_Producer "wikilink") that
causes media not to be found if it contains less common characters and
even spaces.

**Recommended:** *Show-Titles\_A\_03\_Upper\_Alpha.mov*

**May cause problems:** *Show Titles A 03 Upper Alpha.mov*

Channels
========

Fill Channels
-------------

The **Fill** channels is another name for the color channels that make
up the image that you actually see when playing a video or image.
CasparCG Server outputs the standard three color channels **R**ed,
**G**reen and **B**lue of content at 8 bits per channel.

Key / Alpha Channel
-------------------

CasparCG Server 2.0 assumes that all content has pre-multiplied alpha.
Version 2.0.4 added support for straight alpha blending in the
compositing stage.

CasparCG Server automatically generates a key signal on the the key
output of your DeckLink or Bluefish card. If your video files have an
embedded alpha channel, that will be used. Everything that cover the
stage background in your Flash template graphics (even FLV video or PNG
sequences) will become your key signal. When you play both video (one
layer) and several Flash layers at once, CasparCG Server handles the
compositing and outputs the combined fill and key signals separately.

Content played by the [Flash
Producer](CasparCG_Server#Flash_Producer "wikilink") is always
pre-multiplied against black (no matter what the *Stage background*
color is set to.) When
[composited](CasparCG_Server#Compositing "wikilink") on top or between
other content (either Flash content in FTs or pre-rendered videos played
by the [FFmpeg Producer](CasparCG_Server#FFmpeg_Producer "wikilink"),
Flash's [Stage](#Flash_Document_Properties "wikilink") will become
transparent and reveal underlying layers. The alpha channel in videos
played by the FFmpeg Producer should be premultiplied so you can play
content from any other producer on top and have everything correctly
composited.

The alpha channel in videos played by the [FFmpeg
Producer](CasparCG_Server#FFmpeg_Producer "wikilink") should be
premultiplied so you can play content from any other producer on top and
have everything correctly
[composited](CasparCG_Server#Compositing "wikilink").

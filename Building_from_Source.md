---
title: Building from Source
permalink: Building_from_Source\
layout: wiki
tags:
 - For Developers
---

-   Get the source code from the
    [Git](https://github.com/CasparCG/Server.git).
-   Unpack the boost libaries (32-bit for CasparCG Server 2.0) or
    *dependencies64\\dependencies64.exe* (64-bit for CasparCG Server 2.1
    or later.)

**We strongly encourage you start from the CasparCG Server 2.1 source,
as it is refactored and 64-bit (only) which will make sure that any
changes you make will be easier to upgrade and maintain in the future.**

Please note that you don't have to compile anything unless you want to
modify the source code; the latest builds are always available at
[<http://builds.casparcg.com/>](http://builds.casparcg.com/)

### Building using Visual Studio C++ 2010 Express

-   You will need to get the ATL from the Windows Driver Development Kit
    (WinDDK 7.x).
-   The dependencies are available on SourceForge out of the SVN
    repository.
-   You will need to change the include “afxres.h” to include
    “windows.h” in shell/shell.rc.


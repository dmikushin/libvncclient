[![CI](https://github.com/LibVNC/libvncserver/actions/workflows/ci.yml/badge.svg)](https://github.com/LibVNC/libvncserver/actions/workflows/ci.yml)
[![Build status](https://ci.appveyor.com/api/projects/status/fao6m1md3q4g2bwn/branch/master?svg=true)](https://ci.appveyor.com/project/bk138/libvncserver/branch/master)
[![Help making this possible](https://img.shields.io/badge/liberapay-donate-yellow.png)](https://liberapay.com/LibVNC/donate) [![Join the chat at https://gitter.im/LibVNC/libvncserver](https://badges.gitter.im/LibVNC/libvncserver.svg)](https://gitter.im/LibVNC/libvncserver?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

LibVNCClient: A library for easy implementation of a VNC client.
Copyright (C) 2001-2003 Johannes E. Schindelin

If you have a general question, it's best to [ask in the community chat](https://gitter.im/LibVNC/libvncserver). If your concern is about a bug or feature request instead, please use [the issue tracker](https://github.com/LibVNC/libvncserver/issues).

If you already used LibVNCClient, you probably want to read [NEWS](NEWS.md).

What is it?
===========

[VNC](https://en.wikipedia.org/wiki/Virtual_Network_Computing) is a set of programs
using the [RFB (Remote Frame Buffer)](https://github.com/rfbproto/rfbproto/blob/master/rfbproto.rst)
protocol. They are designed to "export" a frame buffer via net: you set up a server and can
connect to it via VNC viewers. 

It is already in wide use for administration, but it is not that easy to program a client yourself.

This has been changed by LibVNCClient.

Projects using it
=================

The [homepage has a tentative list](https://libvnc.github.io/#projects-using) of
all the projects using LibVNCClient.

RFB Protocol Support Status
===========================

## [Security Types](https://github.com/rfbproto/rfbproto/blob/master/rfbproto.rst#712security)

|Name               |Number      | LibVNCClient |
|-------------------|------------|--------------|
|None               |          1 |            ✔ |
|VNC Authentication |          2 |            ✔ |
|SASL               |         20 |            ✔ |
|MSLogon            | 0xfffffffa |            ✔ |
|Apple ARD          |         30 |            ✔ |
|TLS                |         18 |            ✔ |
|VeNCrypt           |         19 |            ✔ |
|UltraVNC MSLogonII |        113 |            ✔ |

## [Encodings](https://github.com/rfbproto/rfbproto/blob/master/rfbproto.rst#76encodings)

| Name     | Number | LibVNCClient |
|----------|--------|--------------|
| Raw      | 1      | ✔            |
| CopyRect | 2      | ✔            |
| RRE      | 3      | ✔            |
| CoRRE    | 4      | ✔            |
| Hextile  | 5      | ✔            |
| Zlib     | 6      | ✔            |
| Tight    | 7      | ✔            |
| Ultra    | 9      | ✔            |
| TRLE     | 15     | ✔            |
| ZRLE     | 16     | ✔            |
| ZYWRLE   | 17     | ✔            |

## Transports

| Name                       | LibVNCClient |
|----------------------------|--------------|

| RFB                        | ✔            |
| Encrypted RFB via VeNCrypt | ✔            |
| Encrypted RFB via AnonTLS  | ✔            |


How to build
============

LibVNCClient uses CMake, which you can download [here](https://cmake.org/download/)
or, better yet, install using your platform's package manager (apt, yum, brew, macports,
chocolatey, etc.).

You can then build via:

    mkdir build
    cd build
    cmake ..
    cmake --build .

Crypto support in LibVNCClient can use different backends:

 * OpenSSL   (`-DWITH_OPENSSL=ON -DWITH_GCRYPT=OFF`)
   * Supports all authentication methods in LibVNCClient.
 * Libgcrypt (`-DWITH_OPENSSL=OFF -DWITH_GCRYPT=ON`)
   * Supports all authentication methods in LibVNCClient.
 * Included  (`-DWITH_OPENSSL=OFF -DWITH_GCRYPT=OFF`)
   * Supports _only VNC authentication_ in LibVNCClient.

Transport Layer Security support in LibVNCClient can use:

 * OpenSSL (`-DWITH_OPENSSL=ON -DWITH_GNUTLS=OFF`)
 * GnuTLS  (`-DWITH_OPENSSL=OFF -DWITH_GNUTLS=ON`)

For some more comprehensive examples that include installation of dependencies, see
the [Unix CI](.travis.yml) and [Windows CI](.appveyor.yml) build setups.

Crosscompiling from Unix to Android
-----------------------------------

See https://developer.android.com/ndk/guides/cmake.html as a reference,
but basically it boils down to:

    mkdir build
    cd build
    cmake .. -DANDROID_NDK=<path> -DCMAKE_TOOLCHAIN_FILE=<path> -DANDROID_NATIVE_API_LEVEL=<API level you want>
    cmake --build .

Crosscompiling from Linux to Windows
------------------------------------

Tested with MinGW-w64 on Debian, which you should install via `sudo apt install mingw-w64`.
You can make use of the [provided toolchainfile](cmake/Toolchain-cross-mingw32-linux.cmake).
It sets CMake to expect (optional) win32 dependencies like libjpeg and friends
in the `deps` directory. Note that you need (probably self-built) development packages for
win32, the `-dev` packages coming with your distribution won't work. Also note that you'll
need to put `libwinpthread-1.dll` in the build dir to run the examples. You can find this DLL
on your Linux build machine via `locate libwinpthread-1.dll`.


	mkdir build
	cd build
	cmake -DCMAKE_TOOLCHAIN_FILE=../cmake/Toolchain-cross-mingw32-linux.cmake ..
	cmake --build .

Cutting a Release
-----------------

* [ ] Update AUTHORS.
* [ ] Update NEWS.
* [ ] Update git submodules.
* [ ] Increment version in CMakeLists.txt
* [ ] Update Doxygen API docs at https://libvnc.github.io/doc/html/
* [ ] Update ChangeLog (using [git2cl](utils/git2cl.pl))


How to use
==========

See the LibVNCClient API documentation for how to create a client instance and connect to VNC servers.

To learn LibVNCClient by example, have a look at the clients in the
[examples/client](examples/client) directory.

Incorporating LibVNCClient into your build system
--------------------------------------------------------------

The install process installs [pkg-config](https://www.freedesktop.org/wiki/Software/pkg-config/)
.pc files for LibVNCClient which you can use in your build
system via the usual `pkg-config --cflags libvncclient` et al.

If using CMake, LibVNCClient versions > 0.9.13 provide CMake configure files so
in your project's CMakeLists.txt, you can say:

```cmake
find_package(LibVNCClient)
if(LibVNCClient_FOUND)
	# libs and headers location are now accessible via properties, but you only
	# need to add the respective export target to your project's target_link_libraries,
	# cmake will automatically add libs and headers
	# eg: add client (YOUR_PROJECT_TARGET being a placeholder for your real target -
	# it must be defined by add_executable or add_library):
	target_link_libraries(YOUR_PROJECT_TARGET LibVNCClient::vncclient)
endif()
```



Achieving good performance on 'slow' links
------------------------------------------

If your client-server connection is sluggish because the link is 'slow', there
are a few things to consider.

First off, you have to investigate whether your link has low throughput or
high latency or both.

### Tackling High Latency

On a high-latency link, try asking for framebuffer updates continously, as
RFB is client-pull per default, not server-push. One example implementation
can be found [here](https://github.com/bk138/multivnc/blob/6251169ed11835ed709c0c191599937759856dda/src/VNCConn.cpp#L1112)
and it definitely improves responsiveness.

There also is the [ContinuousUpdates RFB extension](https://github.com/rfbproto/rfbproto/blob/513c651fff1b213188daa5069444145a63e71617/rfbproto.rst#L4052),
but that one is not supported by LibVNC (yet).

### Tackling Low Throughput

If your link is low-throughput, you basically have to reduce the number of
bytes that get sent per framebuffer update:

* First off, you should have your client request a lossy encoding such as Tight.
  This already yields some huge savings.
* Use a pixel format that represents a pixel with a smaller amount of bytes.
  For instance, you can switch from 24-bit true colour to 16-bit high colour or
  even a palleted colour mode. You can request a pixel format via the client or
  set a default (native) one in the server. With the latter approach, however,
  you very probably also have to change the way your framebuffer data gets written,
  so the first client-side one should be preferred.
* Send a scaled-down version of your framebuffer. Your client can request a scaled screen
  via a [SetScale or SetScaleFactor message](https://github.com/rfbproto/rfbproto/blob/513c651fff1b213188daa5069444145a63e71617/rfbproto.rst#L1344)
  (this is per-client scaling - UltraVNC viewers can request this).
  

Commercial Use
==============

At the beginning of this project Dscho, the original author, would have
liked to make it a BSD license. However, it is based on plenty of GPL'ed
code, so it has to be a GPL.

The people at AT&T worked really well to produce something as clean and lean
as VNC. The managers decided that for their fame, they would release the
program for free. But not only that! They realized that by releasing also
the code for free, VNC would become an evolving little child, conquering
new worlds, making its parents very proud. As well they can be! To protect
this innovation, they decided to make it GPL, not BSD. The principal
difference is: You can make closed source programs deriving from BSD, not
from GPL. You have to give proper credit with both.

Frequently Asked Questions
--------------------------
> Our commercial product wants to make use of LibVNCClient to create our own VNC client and distribute. Will this be considered derivative work in the GPLv2 context?

Yes. Please note that while you would have to stick to the GPL for your program if you link to LibVNCClient, you do not have to make your code public in case you use the derivative work internally in your organisation, see https://www.gnu.org/licenses/gpl-faq.html#GPLRequireSourcePostedPublic

> Does modifying LibVNCClient code or not make any difference in determining whether our VNC client will be considered derivative work?

No. By simply linking to LibVNCClient, your program becomes derivative work. 


License
-------

This program is free software; you can redistribute it and/or
modify it under the terms of the GNU General Public License
as published by the Free Software Foundation; either version 2
of the License, or (at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program; if not, write to the Free Software
Foundation, Inc., 59 Temple Place - Suite 330, Boston, MA  02111-1307, USA.

Contact
=======

* To file an issue, go to https://github.com/LibVNC/libvncserver/issues
* For non-public contact please see [SECURITY.md](SECURITY.md).


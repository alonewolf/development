development
===========

# Description

Quickly get camfire up and running for development.

# Instructions

## libjingle

The libjingle library does not currently provide a way to rebroadcast streams from peerconnections. This project creates
a fake video capturer that gets signaled from another broadcast.

I wanted my main target to be 64bit. The libjingle library uses carbon which does not play well with 64 bit. I had to
do some ugly hacking to get it to work. Basically the video capturing functionality is stubbed out to do nothing in OS X.
This doesn't cause any problems for this project since we are not interested in capturing video on the server themselves.
The server simply routes the stream inputs.

There are likely some memory leaks that still need to get cleand up!

Check my commits on this project to see what I actually changed:
https://github.com/camfire/libjingle/commits/develop/talk

### Compiling
`cd libjingle`

`gclient sync --force`

#### Using ninja (This is what I do)
`GYP_GENERATORS=ninja GYP_DEFINES="libjingle_java=1 build_with_libjingle=1 build_with_chromium=0 target_arch=x64" gclient runhooks --force`
`ninja -C out/Debug libjingle_peerconnection_jar`

#### Using xcode
`GYP_DEFINES="libjingle_java=1 build_with_libjingle=1 build_with_chromium=0 target_arch=x64" gclient runhooks --force`
`open talk/libjingle_all.xcodeproj/`

#### Updating project
`gclient runhooks --force`

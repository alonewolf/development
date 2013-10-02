# camfire

A server-based WebRTC video streaming solution. The main goal is to make a scalable, plugin-less, browser-based, real-time, streaming architecture. Users connect to a Ruby on Rails/AngularJS application that interacts with a signaling server through WebSockets in which messages are exchanged bidirectionally. Users can broadcast video streams through the server, making them available for others using the system. Benefits include:

* Users are not limited by their own bandwidth when making video streams available to a larger audience;
* Users have better security, since they do not connect directly to each other.

## Instructions

Quickly get camfire up and running for development.

1. Clone this repository, e.g., `git clone git@github.com:camfire/development.git camfire`
2. `cd camfire` or wherever you cloned this repository.
2. `git submodule init`
3. `git submodule update`

## libjingle

The libjingle library does not currently provide a way to rebroadcast streams from peerconnections. This project creates
a fake video capturer that gets signaled from another broadcast.

I wanted my main target to be 64-bit. The libjingle library uses Carbon which does not play well with 64 bit. I had to
do some ugly hacking to get it to work. Basically, the video-capturing functionality is stubbed out to do nothing in OS X.
This doesn't cause any problems for this project since we are not interested in capturing video on the server itself.
The server simply routes the stream inputs.

There are likely some memory leaks that still need to get cleaned up!

[Look at my commits on this project](https://github.com/camfire/libjingle/commits/develop/talk) to see what I actually changed.

### Compiling libjingle

    cd libjingle
    gclient sync --force

#### Using ninja (my technique)

    GYP_GENERATORS=ninja GYP_DEFINES="libjingle_java=1 build_with_libjingle=1 build_with_chromium=0 target_arch=x64" gclient runhooks --force
    ninja -C out/Debug libjingle_peerconnection_jar

#### Using xcode

    GYP_DEFINES="libjingle_java=1 build_with_libjingle=1 build_with_chromium=0 target_arch=x64" gclient runhooks --force
    open talk/libjingle_all.xcodeproj/

### Updating libjingle

`gclient runhooks --force`

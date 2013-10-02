# camfire for development

## What is camfire?

Camfire is a server-based WebRTC video streaming solution. The main goal is to
make a scalable, plugin-less, browser-based, real-time, streaming architecture.
Users connect to a Ruby on Rails/AngularJS application that interacts with a
signaling server through WebSockets in which messages are exchanged
bidirectionally. Users can broadcast video streams through the server, making
them available for others using the system. Benefits include:

* Users are not limited by their own bandwidth when making video streams available to a larger audience;
* Users have better security, since they do not connect directly to each other.

## What is this repository?

This repository contains all the code necessary to develop camfire. You
probably don't want all this stuff if you're just trying to run camfire in a
production environment. Ideally, you'd be running the
[signalling server](https://github.com/camfire/signaling) on an
independent server, [web](https://github.com/camfire/web) on a separate server,
and the [media server](https://github.com/camfire/media) which is not yet ready
for deployment.

## Instructions

Clone this repository recursively to get all its submodules:

`git clone git@github.com:camfire/development.git camfire --recursive`

If you have already cloned the repository normally, do the following to get all the submodule code:

1. `cd camfire` or wherever you cloned the repository.
2. `git submodule init && git submodule update`

## libjingle

The libjingle library does not currently provide a way to rebroadcast streams from peerconnections. This project creates
a fake video capturer that gets signaled from another broadcast.

I wanted my main target to be 64-bit. The libjingle library uses Carbon which does not play well with 64 bit. I had to
do some ugly hacking to get it to work. Basically, the video-capturing functionality is stubbed out to do nothing in OS X.
This doesn't cause any problems for this project since we are not interested in capturing video on the server itself.
The server simply routes the stream inputs.

There are likely some memory leaks that still need to get cleaned up!

[Look at my commits on this project](https://github.com/camfire/libjingle/commits/develop/talk) to see what I actually changed.

### Install gclient

#### Using zsh with Oh My Zsh

1. If you are using [Oh My Zsh](https://github.com/robbyrussell/oh-my-zsh), clone the `git@github.com:jgrowl/depot_tools.git`
    repository into your zsh plugins directory. Your plugins directory is
    possibly located at a path like ~/.dotfiles/oh-my-zsh/custom/plugins.
2. Edit your .zshrc file, possibly at ~/.dotfiles/zshrc. Add to your plugins list by including `depot_tools`: `plugins=(git brew gem svn depot_tools)`
3. In a new term, you should now have gclient:
        
        $ which gclient
        /Users/me/.oh-my-zsh/custom/plugins/depot_tools/depot_tools/gclient

#### Not using zsh

Install gclient by following [Google's instructions](http://www.chromium.org/developers/how-tos/install-depot-tools).

### Compile libjingle

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

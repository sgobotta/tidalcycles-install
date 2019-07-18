## Pre requisites

Install pre-requisites

```bash
	sudo apt-get update

	sudo apt-get install g++
```

## Install dev library for ALSA applications:

> Sometimes the version of Jack you installed was compiled without Alsa support.
> You need to either install a version of Jack with it compiled in(The VAST
> majority of packages out there fall under this) or build it yourself from
> source.                                                                        

```bash
	sudo apt-get install libasound2-dev
```

## QT dev tools and ide

Visit ***https://www.qt.io/download-thank-you?os=linux*** and download the linux x64 version, then execute installer:

```bash
	chmod +x qt-unified-linux-x64-3.1.1-online.run
	./qt-unified-linux-x64-3.1.1-online.run
```

## Jack Audio

Visit jackaudio homepage http://www.jackaudio.org/downloads/ or get a release from the [releases](https://github.com/jackaudio/jack2/releases/) page. [Version 1.9.12](https://github.com/jackaudio/jack2/releases/download/v1.9.12/jack2-1.9.12.tar.gz) or with wget:

```bash
	wget https://github.com/jackaudio/jack2/releases/download/v1.9.12/jack2-1.9.12.tar.gz
	./waf configure 
	./waf build 
	sudo ./waf install 
```

Later when you run SuperCollider or qjackctl, you might need to reconfigure the real-time scheduling for jackd:

```bash
	dpkg-reconfigure -p high jackd
```

If you don't have the dpgk-reconfigure command (it's not the same as dpkg --configure), you can download debconf (which contains the dpkg-reconfigure program) utils from [here](https://snapshot.debian.org/archive/debian/20170521T212524Z/pool/main/d/debconf/debconf_1.5.61.dsc), extract them and the dpkg reconfigure program as follows:

*download debconf utils from debian.org*
```bash
	wget https://snapshot.debian.org/archive/debian/20170521T212524Z/pool/main/d/debconf/debconf_1.5.61.dsc
```

*reconfigure*
```bash
	./dpkg-reconfigure -p high jackd
```

A new file 'audio.conf' in /etc/security/limits.d will be created, it should look something like this:

```
# Provided by the jackd package.
#
# Changes to this file will be preserved.
#
# If you want to enable/disable realtime permissions, run
#
#    dpkg-reconfigure -p high jackd

@audio   -  rtprio     95
@audio   -  memlock    unlimited
#@audio   -  nice      -19
```


## Qjackctl (UI client for jack audio)


### Qjackctl pre-requisites

Download qjackctl from https://downloads.sourceforge.net/qjackctl/qjackctl-0.5.8.tar.gz

Install qt5 as default:

> This package sets Qt 5 to be the default Qt version to be used when using
> development binaries like qmake. It provides a default configuration for
> qtchooser, but does not prevent alternative Qt installations from being used.

```bash
	aptitude show wt5-default
	sudo apt-get install qt5-default
```

> qjackctl will use lupdate command, which is part of qt5 dev tools, then install:

```bash
	sudo apt-get install qttools5-dev-tools
```

> If you're checking out from Git, you'll have to prepare the
configure script just before you proceed with the above instructions:

```bash
   ./autogen.sh
```

### Pre Configure qjackctl installation

```bash
	./configure --prefix=/usr/local
```

### Install qjackctl

```bash
	make
	sudo make install (because it tries to write in /usr/local/share/applications)
```

### Run qjackctl

```bash
	/usr/local/bin/qjackctl	
```

> QJACKCTL Configuration
> 
> QjackCtl holds its settings and configuration state per user, in a file
> located as $HOME/.config/rncbc.org/QjackCtl.conf . Normally, there's no
> need to edit this file, as it is recreated and rewritten everytime
> qjackctl is run.

## Tidal Cycles

### Installation:

#### Pre-Requisites

Haskell
Atom Editor/Vim
SuperCollider
Git

#### Install Haskell, Git

```bash
	sudo apt-get install build-essential cabal-install git
```

#### Install SuperCollider

##### Build/Install binaries/programs

(Recommended) Visit https://github.com/lvm/build-supercollider and follow instructions:

```bash
	git clone https://github.com/lvm/build-supercollider/
	cd build-supercollider
	sh build-supercollider.sh
	sh build-sc3-plugins.sh
```

***Note that if you rerun build-sc3-plugins, libraries will be duplicated and SuperCollider won't start up.

You can also install supercollider via:

```bash
	sudo apt-get install supercollider sc3-plugins
```

> However this generally doesn't work. Either the supercollider version is too old (superdirt
> needs at least version 3.7), or the version of supercollider is mismatched with sc3-plugins. 
> If you're using an ubuntu or debian distribution, my advice is to ignore the supercollider 
> packages and just compile them yourself, like explained above.

##### Download and Install SuperDirt library inside SuperCollider

TidalCycles is meant to run on top of SuperDirt, so you will have to run it first to make sound. Here are two options get SuperDirt installed inside SuperCollider:

1) User Interface: *starts the SuperCollider IDE from a terminal*
```
	scide
```

*In the editor window paste in the following line of code: *

```bash
	Quarks.checkForUpdates(); Quarks.install("SuperDirt", "v1.0")
```

2) Terminal: *downloads and installs dependencies from the sclang prompt*

run the sclang prompt
```bash
	sclang
```

download and install SuperDirt dependencies (called quarks)
```
	sc3> Quarks.checkForUpdates(); Quarks.install("SuperDirt", "v1.0")
```
SuperDirt maybe to be recompiled in the current SuperCollider instance:
```
	sc3> SuperDirt
```

***(?) it'll be nice to know how to exit the sc3 prompt without having to kill the process.***

### Install TidalCycles

In this step we use cabal package system (haskell libraries manager)


```
	cabal install tidal
	# Update cabal if there is no package named 'tidal'
	cabal update
	# Then try to install tidal again
	cabal install tidal
```

## Run the whole thing: test TidalCycles and SuperDirt for the first time

Useful link https://tidalcycles.org/index.php/Start_tidalcycles_and_superdirt_for_the_first_time

### Start jack server

+   By using the qt client qjackctl
+   Or by terminal (todo)

[Download](https://raw.githubusercontent.com/musikinformatik/SuperDirt/develop/superdirt_startup.scd) a sample startup script or use:

### Start SuperCollider

Via terminal

```bash
	sclang superdirt_startup.scd
```

via the supercollider IDE: scide

```
	SuperDirt.start
```


```
/*
This is an example startup file. You can load it from your startup file
(to be found in Platform.userAppSupportDir +/+ "startup.scd")
*/


(
// configure the sound server: here you could add hardware specific options
// see http://doc.sccode.org/Classes/ServerOptions.html
s.options.numBuffers = 1024 * 256; // increase this if you need to load more samples
s.options.memSize = 8192 * 16; // increase this if you get "alloc failed" messages
s.options.maxNodes = 1024 * 32; // increase this if you are getting drop outs and the message "too many nodes"
s.options.numOutputBusChannels = 2; // set this to your hardware output channel size, if necessary
s.options.numInputBusChannels = 2; // set this to your hardware output channel size, if necessary
// boot the server and start SuperDirt
s.waitForBoot {
	~dirt = SuperDirt(2, s); // two output channels, increase if you want to pan across more channels
	~dirt.loadSoundFiles;   // load samples (path containing a wildcard can be passed in)
	// for example: ~dirt.loadSoundFiles("/Users/myUserName/Dirt/samples/*");
	// s.sync; // optionally: wait for samples to be read
	~dirt.start(57120, [0, 0]);   // start listening on port 57120, create two busses each sending audio to channel 0

	// optional, needed for the sclang tests only:
	(

		~d1 = ~dirt.orbits[0]; // one orbit
		~d2 = ~dirt.orbits[1];

	);
};

s.latency = 0.3; // increase this if you get "late" messages
);
```

### Boot Tidal .hs file

https://github.com/tidalcycles/Tidal/blob/master/BootTidal.hs


### Dirt samples directory

~/.local/share/SuperCollider/downloaded-quarks/Dirt-Samples

### Run your favourite text editor with the tidalcycles plugin and execute:

```
	d1 $ sound "bd bd"
```


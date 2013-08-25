---
layout: post
title: "Using the Raspberry Pi as a MPD server"
date: 2012-12-24 16:15
published: true
tags:
- computing
- radio
- streaming
- mpd

---

As it is virtually Christmas, one of the requests I received at home was to make it easier to be able to listen to the music library I have set-up on the home server. My initial thought on this was to set-up a system that allowed the music to be heard in a central place in the house. The simplest way of doing this would be just to place a laptop in this location and have the music playing through the built-in speakers. However I then came to the decision that this method isn't very efficient with electricity. It would require two complete computer systems. So, I had a different idea - use the raspberry pi for playback via an FM transmitter.

<!-- more -->

### Overview of setup procedure ###
After thinking things through, I have come to the conclusion that there are four different stages in setting this up. They are:

1. Setting up the home server to have an NFS share that contains the music folder for the raspberry pi to mount/access.
2. Install and configure the MPD server on the raspberry pi.
3. Configure the client (for playback control) - mobile device.
4. Connect FM transmitter to raspberry pi and configure FM radio to the neccessary frequency.

### Home Server (File Server) Setup ###
As explained in the overview section, I needed to setup a NFS share that the raspberry pi can access. As I already had a NFS server setup on the server, I only needed to make a few tweaks to the `/etc/exports` file. Below is the contents of the file I used for this setup.

```
/storage 192.168.0.0/24(rw,fsid=0,no_subtree_check)
```

### MPD Server Install and Configuration ###
This was the most important step of the setup as it is the core component of the whole idea.

The raspberry pi was using the Arch Linux distribution so the first thing I did was use pacman to install mpd.

```bash
$ sudo pacman -S mpd
```

Once that had finished I then opened the mpd config file (`/etc/mpd.conf`). Below is the config file I have with just the main things I changed without any of the comments.

```bash
music_directory     "/var/lib/mpd/music"
playlist_directory  "/var/lib/mpd/playlists"
db_file             "/var/lib/mpd/mpd.db"
log_file            "/var/log/mpd/mpd.log"
pid_file            "/run/mpd/mpd.pid"
state_file          "/var/lib/mpd/mpdstate"

user                "mpd"

zeroconf_enabled    "yes"
zeroconf_name       "Raspberry Pi Music Player"

input {
  plugin "curl"
}

audio_output {
  type            "alsa"
  name            "My ALSA Device"
  mixer_type      "software"
}
```

The majority of these options in the config file are unchanged from the defaults. In particular, I've decided to not adjust the actual `music_directory` location. The reasons for this are to avoid any major permissions conflict by keeping it in the `/var/lib/mpd` directory and because I will just create a symbolic link to the actual mounted music directory to this directory for mpd.

#### NFS Mounting Setup ####
As part of configuring the MPD server the NFS share on the home server has to be mounted in order for the music to be accessible.

##### AutoFS Install and Config #####
So, the first step was to install a program called autofs. It is a auto-mounting program for network shares and removable devices. With it, I do not need to worry about having to manually mounting the NFS share from the home server.
The installation is accomplished through the normal way:

```bash
$ sudo pacman -S autofs
```

Once that is done, the next step is to load the autofs4 kernel module. This is achieved through the normal way with modprobe:

```bash
$ sudo modprobe autofs4
```

Now would be a good time to include this module in a `modules.load.d` file. Here is my `/etc/modules.load.d/autofs` file for loading this kernel module automatically in the future:

```bash
# Load autofs4 on boot
autofs4
```

Now that the kernel module is setup properly, the next step would be to check the configuration for autofs. Open up the `/etc/autofs/auto.master` file in your preferred text editor and then make sure that the following line is included:

```bash
/net -hosts --timeout=60
```

*Feel free to adjust the timeout value to something different than 60. (The unit is in seconds for this timeout variable)*

As all the configuration for autofs is now complete, the final step is to enable and start the autofs service. This is accomplished by running the following:

```bash
$ sudo systemctl enable autofs.service
$ sudo systemctl start autofs.service
```

Now if you go to the following directory by `cd`, you should see the contents of the NFS share. For example:

```bash
$ cd /net/[nfs-server-hostname]/[nfs-share-name]
$ cd /net/microserver/media
```

#### MPD Permission Setup and Initial Folder Creation ####
The next step or stage I went with was to create the necessary folders for MPD to function correctly and setup the permissions for these folders to what they should be. This was done as follows:

```bash
# If /var/log/mpd doesn't exist
$ sudo mkdir /var/log/mpd

$ sudo chown -R mpd /var/log/mpd
$ sudo mkdir -p /var/lib/mpd/playlists
$ sudo touch /var/lib/mpd/{mpd.db,mpdstate}
$ sudo chown -R mpd /var/lib/mpd
# Add mpd to the audio group in-order for it to use the ALSA sound system
$ sudo gpasswd -a mpd audio
```

##### Symbolic Link Creation (NFS<->MPD) #####

From here, the next step would be to create a symbolic link betwen the nfs server folder and the mpd music folder. In my case it was just running the following:

```bash
$ sudo ln -s /net/microserver/media/Lossy_ogg /var/lib/mpd/music
```

*Obviously, change this to how your folder structure is.*

#### MPD Service Enable and Start ####
From here, I think MPD shall be setup correctly for the daemon to be started. With systemctl, I enabled and started the mpd service as follows:

```bash
$ sudo systemctl enable mpd.service
$ sudo systemctl start mpd.service
```

#### MPD Library Scan ####
To be sure that MPD does perform a scan of the music folder, I decided to manually initiate it. Unfortunately, this cannot be achieved directly from MPD. Instead it must be done through MPC - the default MPD CLI client.

So if you haven't already installed it, do so through pacman. (`sudo pacman -S mpc`) Once that is done, you'll be able to run the following command to initiate the manual library scan:

```bash
$ mpc update
```

### Client Setup ###
As very briefly mentioned in the overview, I have decided to use a mobile device to act as the main client for the media server. My preferred type of device is one that is running on the Android platform. So for this I have been using the [MPDroid](https://github.com/abarisain/dmix) open-source application.

Setting-up MPDroid with the server was simply a matter of adding the hostname of the server to the settings and then connecting to it.

### FM Transmitter Setup ###
**This should the last and final stage of this whole setup.**

As the Raspberry Pi has an audio out jack, it was simply just a case of plugging in the FM transmitter, setting the transmission frequency and the FM radio (receiver) frequency, set some songs to play using a client and you should be good to go.

Except, there may be one extra thing. In the default installation of Arch Linux for the raspberry pi, the sound card kernel module isn't loaded automatically. Load the module through the standard way:

```bash
$ sudo modprobe snd_bcm2835
```

You may also want to create a `modules-load.d` file with the following contents:

```bash
# Load the sound card module for the broadcom chip on the rapi
snd_bcm2835
```

# squeezelite-pulseaudio-on-mint-22.1
Getting squeezelite-pulseaudio to work on Linux Mint Xfce 22.1

## Introduction
I have a piCorePlayer-based Lyrion Music Server running on a Raspberry Pi 4B. I have players everywhere in my house except my office.
I figured that I always have my Linux desktop computer running while I am working down here, so why not just
listen to music on my computer's USB speakers? There is a desktop application called `squeezelite` that runs
on Linux and has "just worked" with every other old computer I installed it on. I ran into
a few issues getting it to work this time, so I thought I would post a step-by-step so others might benefit.

## Steps 
The following sections are the steps I used to get this working. My computer is a late 2012 Intel-based Mac Mini
running Linux Mint Xfce 22.1. The computer has a pair of ancient Logic Z-5 USB speakers attached.

### Install `squeezelite-pulseaudio`
I used the Linux Mint Xfce GUI `Software Manger` application to install this. Your Linux desktop maybe has
a different tool, or you may prefer the command line. If you search for squeezelite in `Software Manager`,
you will see that there are several different `squeezelite` packages available including `squeezelite`, `squeezelite-pulseaudio`,
and `squeezelite-pa`. The `-pa` version is for "Port Audio." My Linux desktop, like most others today, runs PipeWire over ALSA and has
a PulseAudio compatibitility layer. Choose `squeezelite-pulseaudio`, which is the closest fit, and install it.

### Make sure the `squeezelite` service is NOT running
It is counter-intuitive, but for various reasons I did not care to fully investigate, running  the `squeezelite`
service as root during startup did not work. Make sure it is disabled from starting up at boot by opening
a command line terminal window and entering the following commands.
```
sudo systemctl stop squeezelite.service
sudo systemctl disable squeezelite.service
```

### Test running `squeezelite-pulseaudio` from the command line
From the command line terminal, enter the following command
```
squeezelite-pulseaudio -n [The player name you want to assign to your computer] -s [Your Lyrion Music Server IP address on your local network]
```
The `-s` argument and IP address should not be necessary because SqueezeLite is supposed to be able to discover servers on your network.
However, I found that the discovery didn't work on my computer/network. It could not find my server unless I told it the IP address. So I needed that argument. For example, on my system, the command line command was
```
squeezelite-pulseaudio -n macmini -s 10.0.0.128
```

Open up the Lyrion web interface at `http://[Your Lyrion Music Server IP address on your local network]:9000`. You should see
the string you typed after `-n` as one of the players. If you open your audio mixer/volume control application and click the
playback tab, you should see the 'SqueezeLite' application as one of the choices.

Stop runnig `squeezelite-pulseaudio` by typing using Ctrl C in the terminal window.

### Make `squeezelite-pulseaudio` start up at login
There are lots of ways to do this. I chose to use the Linux Mint Xfce GUI to set this up.
Run the `Session and Startup` application and click the `Applicaton Autostart` tab. Press the `Add` button.
- In the Application Name text box, type `SqueezeLite`.
- In the Description text box, type `Lyrion Music Player`.
- In the Command text box, type
```
squeezelite-pulseaudio -n [The player name you want to assign to your computer] -z -s [Your server IP address on your local network]
```
For example, on my system, the Command text box contains
```
squeezelite-pulseaudio -n macmini -z -s 10.0.0.128
```
Press `OK` to save the changes.

Note that the Command text box has a `-z` argument in it. That starts the player and keeps it running in the background.

Log out or reboot. Log back in.
Open up the Lyrion web interface at `http://[Your server IP address on your local network]:9000`. You should see
the string you typed after `-n` as one of the players.

### Audio weirdness
One issue I had was the audio level for my `SqueezeLite` player in the Volume Control application mixer was set to 0. I had to turn
it to 100% in the audio mixer/Volume Control application to hear the playback. I also had to adjust the volume to 100% in the Lyrion web playback interface so that I could use the computer volume control to adjust my speaker volume.

During use, I noticed that pausing playback would set the volume in the audio mixer/Volume Control for the `SqueezeLite` player to 0. Pressing play restored the volume to 100%.

### Enjoy
I hope that was helpful to you. It will probably help me the next time I have to install this.

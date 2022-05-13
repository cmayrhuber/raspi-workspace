# Home Office via Citrix Workspace App on Raspberry PI OS Bullseye arm64

The Raspberry Pi 4 single board computer supports dual screen configurations and
thus has become a silent, serious candidate for Home Office work.

I am using a Raspberry Pi 4 4GB, with Raspberry PI OS Bullseye, 2x screen with an USB speakerphone with hardware
echo cancellation.
The assumption is that Raspberry PI OS is installed with the LXDE-pi desktop and that the displays are working. 

## The previous guide was for Buster
You can find this guide here: [Home Office via Citrix Workspace App on Raspberry PI OS Buster](Buster.md)
It contains additional information in how to set up echo cancellation with for a cheap speakerphone that is
unable to do that in hardware via pulseaudio. I bought a second, more expensive speakerphone that supports 
hardware echo cancellation and it is a lot better than the previous setup with pulseaudio.

Also, the setup in buster is way more complicated than in Bullseye, even when using multiarch.

## What you will find in this HowTo
* Install Citrix Workspace on Raspberry Pi OS arm64 release
* Configuration of Raspberry PI OS to be able to use a dual screen setup with the Citrix Workspace App.

Ths HowTo uses nano as text editor. So wherever you see nano you can use a different editor.

### Positives and Negatives
* The Citrix Workspace App  
  \+ can be downloaded as armhf Debian package for Buster on the Citrix website  
  \+ multiple displays work out of the box in Raspberry Pi OS when it uses the Mutter window manager  
  \- multiple displays do not work with the Openbox window manager, thus at least a 2GB model is needed [Bullseye – the new version of Raspberry Pi OS](https://www.raspberrypi.com/news/raspberry-pi-os-debian-bullseye/)  
  \- no optimization for realtime audio (Skype calls, etc.)  
* Raspberry Pi OS Desktop arm64  
  \+ the LXDE-pi desktop is sleek and nicely customized  
  \+ Raspberry Pi OS is based upon Debian and thus supports the fantastic multiarch feature  
  \+ Bullseye comes with Pulseaudio support by default  
  \+ No more special configuration for gpu memory to prevent a memory leak  

## Raspberry Pi OS arm64
Why arm64 and not armhf? Well there are some synthetic benchmarks in the net that show a performance benefit.
I don't have a prove, but overall the 64bit version seems to behave a tinty bit "snappier" and there is of course
the chance to try out multiarch ...

So I did a re-install with the Raspberry Pi arm64 image [Raspberry Pi OS (64-bit)](https://www.raspberrypi.com/news/raspberry-pi-os-64-bit/)
and also followed the steps to install the chromium armhf version to have support for streaming media that requires
content protection.

Compared to Buster the Bullseye release has matured a lot on the Raspberry Pi hardware.

## Multiple screens
The Mutter window manager provides the right hints to the Citrix workspace app.
The only thing I had to do was to activate two screens in /boot/config.txt

```sh
sudo nano /boot/config.txt
```

Add the line:
```
max_framebuffers=2
```

## Citrix Workspace App Installation
The Citrix Workspace App can be downloaded via the Citrix homepage.

[Citrix Workspace app for Linux (ARM HF)](https://www.citrix.com/downloads/workspace-app/linux/workspace-app-for-linux-latest.html)

Navigate to _Available Downloads_ &rarr; _Debian Packages_ &rarr; _Full Packages_ and click **Download file**

Since version Dec 12, 2019 the package can be directly installed via gdebi package manager. All required packages will automatically be downloaded.
To install open the _Downloads_ folder in the file manager, right-click on the icaclient*.deb package and install the package.

After the install several libraries are missing to get audio and video working.
So you need to install some additional packages that are missing in the armhf architecture:

```sh
sudo apt install gstreamer1.0-libav:armhf gstreamer1.0-plugins-base:armhf gstreamer1.0-plugins-good:armhf gstreamer1.0-plugins-bad:armhf gstreamer1.0-plugins-ugly:armhf libc++1:armhf libcanberra-gtk-module:armhf libcanberra-gtk3-module:armhf libwebkit2gtk-4.0:armhf
```

I had a bit of a struggle to get the microphone working in Citrix Workspace and I think the solution was to install
pulseaudio for armhf to pull in all the dependencies and then to reinstall pulseaudo for arm64
(which got removed by the armhf version).

Install pulseaudio:armhf
```sh
sudo apt install pulseaudio:armhf 
```

Re-install pulseaudio:arm64 to actually have working audio after a reboot.
```sh
sudo apt install pulseaudio 
```


The Citrix workspace app provides a script if all necessary components are installed, run:
```sh
sudo /opt/Citrix/ICAClient/util/workspacecheck.sh
```

You now can launch Citrix Workspace via _Menu_ &rarr; _Others_ &rarr; _Citrix Workspace_

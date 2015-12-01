---
layout: post
title:  Creating a screencast gif in Ubuntu
date:   2015-12-02 10:01:00 +1000
---

I was working on a plugin for Atom - an Oracle PL/SQL autocompletion provider. And I was interested in creating a screencast gif as seems quite common on similar providers. Not quite sure where to begin, I recorded a video with `gtk-recordmydesktop`. The resulting file was 23.8MB.

So, next I did a little research to find out how I can convert this video to an animated gif. Which led me to the following question on AskUbuntu - http://askubuntu.com/questions/107726/how-to-create-animated-gif-images-of-a-screencast. The first (accepted) answer involved firstly converting each from of the video to a jpeg file.

```
mplayer -ao null <video file name> -vo jpeg:outdir=output
```

This results in a folder containing 3MB of jpeg images. The next step was converting those images into a gif using the `convert` program that comes with the `ImageMagick` package.

```
convert output/* output.gif
```

The only problem here, is that the resulting file was 9.8MB - not really a suitable size for the web. The next part of the answer goes onto suggest a couple of extra arguments to help optimise the image.

```
convert output.gif -fuzz 10% -layers Optimize optimised.gif
```

That works in that the size is now coming in at around 500KB. The only issue is the quality isn't the best with colours jumping around, where the shouldn't be. This has to do with, in part, the -fuzz argument. The value can be played around with - for example, if I use 5% the resulting image becomes 914KB - and the quality is better, but still not great.

There may be some other arguments to get a better result in the above described method, but for me, it was time to try one of the other suggested solutions.

Which leads us onto the package `byzanz`, which is in the repo's as at time of writing:

```
$ apt-cache policy byzanz
byzanz:
  Installed: 0.3.0+git20140123-1
  Candidate: 0.3.0+git20140123-1
  Version table:
 *** 0.3.0+git20140123-1 0
        500 http://au.archive.ubuntu.com/ubuntu/ trusty/universe amd64 Packages
        100 /var/lib/dpkg/status
```

This is a command line utility, so you will need to get your hands a little more dirty - you need to know the coordinates of the program you wish to record (x and y) as well as the dimensions. Fortunately, there is a program `xwininfo` that can report all this for you.

```
$ xwininfo

xwininfo: Please select the window about which you
          would like information by clicking the
          mouse in that window.

xwininfo: Window id: 0x5c0004e "untitled - Atom"

  Absolute upper-left X:  170
  Absolute upper-left Y:  238
  Relative upper-left X:  0
  Relative upper-left Y:  0
  Width: 706
  Height: 557
  Depth: 24
  Visual: 0x20
  Visual Class: TrueColor
  Border width: 0
  Class: InputOutput
  Colormap: 0x22 (installed)
  Bit Gravity State: ForgetGravity
  Window Gravity State: NorthWestGravity
  Backing Store State: NotUseful
  Save Under State: no
  Map State: IsViewable
  Override Redirect State: no
  Corners:  +170+238  -1044+238  -1044-285  +170-285
  -geometry 706x557+160+200
```

note: The coordinates are excluding the applications title bar.

The other thing you will need to know is the duration of the video. It can be tricky to know in advance, but you should be able to have a rough idea - and can deal with the excess later on.

So, then we have:

```
byzanz-record --duration=30 --x=170 --y=238 --width=706 --height=557 out.gif
```

The resulting video came in at 133KB.

There are a couple of scripts posted online which may make the above process slightly easier.

```bash
#!/bin/sh
#
# Usage: screencast-window [ARGUMENTS_FOR_BYZANZ_RECORD...]
#
# Take a video capture, in GIF format, of a single window.
#
# Written in 2010 by Suraj N. Kurapati <https://github.com/sunaku>

echo 'Select the window you want to video capture...'
byzanz-record $(xwininfo | awk '
  /Absolute upper-left X/ { x = $4 }
  /Absolute upper-left Y/ { y = $4 }
  /Width/                 { w = $2 }
  /Height/                { h = $2 }
  END                     { print "-x", x, "-y", y, "-w", w, "-h", h }
') -v "$@"
```
[source](https://github.com/sunaku/home/blob/master/bin/screencast-window)  
and [expanded python version](https://gist.github.com/noamraph/8348560)

This script makes it dead easy - you would call it like so

```
./screencast-window.sh -d 30 output.gif
```

Then, you just select the window to record and you're away.

Now, dealing with the excess frames. If you open the gif up in `Gimp` you can preview the gif which reports frame information. This is actioned through the menu: `Filters->Animation->Playback...`.

![](https://cloud.githubusercontent.com/assets/1747643/11516448/5ba3f1b2-98d7-11e5-90d8-ef75338482aa.png)

After watching the playback, I could conclude that I wanted to trim it from around frame 80 - and that there were a total of 121 frames.

![](https://cloud.githubusercontent.com/assets/1747643/11516489/9aafdcea-98d7-11e5-9217-9eac32c150e4.png)

Alternatively, watching the gif with `mplayer` also reports back on frame information (paused at frame 82 below).

```
$ mplayer deletedframes.gif
MPlayer 1.1-4.8 (C) 2000-2012 MPlayer Team

Playing deletedframes.gif.
GIF file format detected.
Audio: no sound
Starting playback...
V:  15.7  82/ 82  0%  2%  0.0% 0 0
  =====  PAUSE  =====
```

With that information, we can that lead onto the `gifsicle` tool.

```
$ apt-cache policy gifsicle
gifsicle:
  Installed: 1.78-1
  Candidate: 1.78-1
  Version table:
 *** 1.78-1 0
        500 http://au.archive.ubuntu.com/ubuntu/ trusty/universe amd64 Packages
        100 /var/lib/dpkg/status
```

This program allows you to easily delete frames from a gif with the argument `--delete #79-120`. This specifies the frame range to delete, where the first frame is the `0` frame.

```
gifsicle input.gif --delete "#79-120" > output.gif
```

Or similarly, there is an example on the web about dropping every second from to condense the size. See [here](http://graphicdesign.stackexchange.com/a/20913).

Created with                        | File Size |  Image
------------------------------------|-----------|--------
`byzanz-record`                     | 130KB     |  ![](https://cloud.githubusercontent.com/assets/1747643/11515330/8b3a1a42-98d1-11e5-8872-c9556bb498da.gif)
`gtk-recordmydesktop` and `convert` | 487.9KB   |  ![](https://cloud.githubusercontent.com/assets/1747643/11517086/81c3b536-98db-11e5-98ae-3dac02bb6ae4.gif)

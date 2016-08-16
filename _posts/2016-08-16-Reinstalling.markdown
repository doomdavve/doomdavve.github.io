---
layout: post
title:  Reinstalling
date:   2016-08-16
categories: ubuntu
---

Some notes from my most recent reinstallation, moving from Debian to
Ubuntu for work. Chromium only supports Ubuntu and getting my Debian
install able to compile Chromium was more work than it was worth.

Since I had everything in one partition, I copied my entire home
directory and /etc onto a separate disk. While the copy was running, I
downloaded a Ubuntu .iso and wrote it to a USB drive using dd:

{% highlight shell %}
dd bs=4M if=ubuntu...iso of=/dev/sdc
{% endhighlight %}

or so. After the install was complete, the first thing I did was to
install google-chrome (using firefox). Getting the keyboard to work is
step two. My [keyboard post]({% post_url 2015-11-12-keyboard %})
is useful here.

I used
[a random blog entry](https://cannibalcandy.wordpress.com/2012/04/26/installing-and-configuring-dwm-under-ubuntu/)
to get dwm up and running on ubuntu, using my own code repo/mirror for
dwm. I didn't use the .desktop from the package though, instead I used:

{% highlight shell %}
[Desktop Entry]
Encoding=UTF-8
Name=Xsession
Comment=User Xsession
Exec=/etc/X11/Xsession
{% endhighlight %}

as `/usr/share/xsessions/custom.desktop` to get my plain old .xsession
working which starts dwm among other things.

Getting `st` to work with core/bitmap fonts required me to install the
xfonts-100dpi, xfonts-75dpi and xfonts-base packages and enable bitmap
fonts with fontconfig:

{% highlight shell %}
$ rm /etc/fonts/conf.d/70-no-bitmaps.conf
$ ls -s /etc/fonts/conf.{avail,d}/70-yes-bitmaps.conf
$ dpkg-reconfigure fontconfig
{% endhighlight %}

or so (commands written from memory and not tested). Some guide
recommended removing 10-* from conf.d too, but I haven't done so this
far.

It seems to be mostly an affair of moving stuff from my old home
directory back from here on.

I can get pass to use my gnupg keys... The problem was that my keys
weren't imported into gpg2.

{% highlight shell %}
$ gpg2 --import ~/.gnupg/secring.gpg
{% endhighlight %}

fixed it. I suppose this may be a one-off thing.

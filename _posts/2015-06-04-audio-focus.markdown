---
layout: post
title:  "Audio focus"
date:   2015-06-04 21:29:26
categories: media audio android ios
---

Lately I've been looking into how Android and iOS handles the audio
focus concept. The reason I've been doing this is because there is
ongoing work on a web specification called [Media Session]. As part of
handling a Media Session, there is the concept of audio focus and
doing integration with the lower level platform. In the specification
this is also called media focus, but it's the same thing.

Having audio focus is as far as I know a concept only present on
mobile platforms. Basically it's assumed that the user only wishes to
listen to (or focus on) only one application at a time. If a music
player is in the foreground playing music, it has the audio focus.
When the user switches to another application, for example the video
player, the music from the music player is suspended and the sound
from the video takes its place. If the user switches back to the music
player and resumes playback, the video player, if allowed to output
the video sound track while in the background, is suspended.

Android
=======

On Android the audio focus system is quite simple.

{% highlight java %}
AudioManager am =
   (AudioManager) getApplicationContext().getSystemService(Context.AUDIO_SERVICE);

int result =
    am.requestAudioFocus(listener,
                         AudioManager.STREAM_MUSIC,
                         AudioManager.AUDIOFOCUS_GAIN);
{% endhighlight %}

That's basically all it takes to request audio focus on Android. If
another application (or the same application with another listener)
already has audio focus, their listener is called:

{% highlight java %}
onAudioFocusChange(int focusChange) {
   ...
}
{% endhighlight %}

where you're free to handle loss of focus as indicated by

{% highlight java %}
   focusChange == AudioManager.AUDIOFOCUS_LOSS
{% endhighlight %}

As a response, you may pause or stop your playback.

There is some more complexity with regards to transient interruptions
and with ducking (lowering your volume while other audio is playing),
but it follows the same basic pattern.

iOS
===

There is a similar system on iOS.

{% highlight swift %}
let session = AVAudioSession.sharedInstance()
session.setCategory(AVAudioSessionCategoryPlayback, error: nil)
session.setActive(true, withOptions: nil, error: nil)
{% endhighlight %}

This is how you may activate a media session on iOS (using swift). If
another application had audio focus, it's paused or muted at the point
this code is executed.

By default your app is entierly suspendend when the user switches to
another app. To really test the audio focus system you need to enable
playing audio in the background (a Info.plist thing).

!!! note: is the notification working now? What if a remote control
    toggle/play event is sent here.

!!! note: say something about background playing and ambient.

!!! note: a big difference between iOS and Android is that it's
    possible to have two apps playing at the same time on Android
    while it's not on iOS. The most recently playing app is given
    precesence and the other ones are muted.

[Media Session]: https://mediasession.spec.whatwg.org/

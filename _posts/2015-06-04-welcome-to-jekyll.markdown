---
layout: post
title:  "Audio focus"
date:   2015-06-04 21:29:26
categories: media audio android iod
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

More to come...

[Media Session]: https://mediasession.spec.whatwg.org/
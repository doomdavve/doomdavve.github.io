---
layout: post
title:  Fun with border-image
date:   2015-11-17
categories: css border-image
---

border-image-source selects between two border drawing modes. If it's
set to `none` (or it points to an image that can't be drawn), the
normal border-drawing mode is activated. The normal border-drawing
mode uses border-style, border-color, etc. to draw the border.

If border-image-source is set to an image, and that image is available
for paint (loaded, decoded and all), the border-image painting takes
over and no old-style border is shown at all.

Related bugs: [356802], [557114]

[356802]: https://code.google.com/p/chromium/issues/detail?id=356802
[557114]: https://code.google.com/p/chromium/issues/detail?id=557114

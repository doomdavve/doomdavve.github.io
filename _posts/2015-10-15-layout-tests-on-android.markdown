---
layout: post
title:  "LayoutTests on Android"
date:   2015-10-22 11:33:00
categories: android chromium blink
---

Running LayoutTests on Android in Debug configuration seems to be a
non-trivial task. So far I've tried Nexus 5 and Nexus 7 models, on
Nexus 5 with production builds and cyanogenmod. The last resort seems
to be building an userdebug build of android. Looking back though,
some of the problems I've had has not been device or platform
specific. Regardless, having a pristine userdebug build of Android for
testing seems worthwhile.

Basically, I just followed:

[Downloading and Building]

and so on. I used `openjdk-7-jdk`, which seems to be fine.

One thing though

{% highlight bash %}
$ fastboot -w flashall
{% endhighlight %}

didn't work.

{% highlight bash %}
davve@drogon:/archive/android$ sudo /home/davve/Android/Sdk/platform-tools/fastboot flashall -w
error: neither -p product specified nor ANDROID_PRODUCT_OUT set
davve@drogon:/archive/android$ find -name boot.img
./out/target/product/hammerhead/boot.img
davve@drogon:/archive/android$ sudo/home/davve/Android/Sdk/platform-tools/fastboot flashall -w -p ./out/target/product/hammerhead/
bash: sudo/home/davve/Android/Sdk/platform-tools/fastboot: No such file or directory
davve@drogon:/archive/android$ sudo /home/davve/Android/Sdk/platform-tools/fastboot flashall -w -p ./out/target/product/hammerhead/
error: could not load android-info.txt: No such file or directory
davve@drogon:/archive/android$ find -name android-info.txt
./out/target/product/hammerhead/android-info.txt
davve@drogon:/archive/android$ sudo /home/davve/Android/Sdk/platform-tools/fastboot flashall -w -p ./out/target/product/hammerhead
error: could not load android-info.txt: No such file or directory
{% endhighlight %}

I had to flash each image in

{% highlight bash %}
cd ./out/target/product/hammerhead
sudo /home/davve/Android/Sdk/platform-tools/fastboot flash boot boot.img
sudo /home/davve/Android/Sdk/platform-tools/fastboot flash system system.img
sudo /home/davve/Android/Sdk/platform-tools/fastboot flash userdata userdata.img
sudo /home/davve/Android/Sdk/platform-tools/fastboot flash recovery recovery.img
{% endhighlight %}

(Yep, I had to be root for fastboot to work on my machine.)

But I still see font related crashers when running layout tests:

{% highlight bash %}
STDOUT: 10-22 08:26:39.022  4312  4335 I chromium: [4312:4335:1022/082639:369928014:INFO:SkFontMgr_android.cpp(584)] SystemFontUse: OnlyCustom BasePath: /data/local/tmp/content_shell/fonts/ Fonts: /data/local/tmp/content_shell/android_main_fonts.xml FallbackFonts: /data/local/tmp/content_shell/android_fallback_fonts.xml
STDOUT: 10-22 08:26:39.022  4312  4335 I chromium: 
STDOUT: 10-22 08:26:39.022  4312  4335 I chromium: [4312:4335:1022/082639:369928240:INFO:SkFontMgr_android_parser.cpp(595)] [SkFontMgr Android Parser] '/data/local/tmp/content_shell/android_main_fonts.xml' could not be opened
STDOUT: 10-22 08:26:39.022  4312  4335 I chromium: 
STDOUT: 10-22 08:26:39.023  4312  4335 I chromium: [4312:4335:1022/082639:369928338:INFO:SkFontMgr_android_parser.cpp(595)] [SkFontMgr Android Parser] '/data/local/tmp/content_shell/android_fallback_fonts.xml' could not be opened
STDOUT: 10-22 08:26:39.023  4312  4335 I chromium: 
STDOUT: 10-22 08:26:39.023  4312  4335 I chromium: [4312:4335:1022/082639:369928405:INFO:SkFontMgr_android.cpp(544)] ../../third_party/skia/src/ports/SkFontMgr_android.cpp:544: failed assertion "!fFontStyleSets.empty()"
STDOUT: 10-22 08:26:39.023  4312  4335 I chromium: 
STDOUT: 10-22 08:26:39.033  4312  4335 F chromium: [4312:4335:1022/082639:369928463:FATAL:SkFontMgr_android.cpp(544)] SK_CRASH
{% endhighlight %}

To work-around the empty list, I had to remote support for custom
fonts in skia:

{% highlight diff %}
diff --git a/src/ports/SkFontMgr_android_factory.cpp b/src/ports/SkFontMgr_android_factory.cpp
index 96d8931..bac22a1 100644
--- a/src/ports/SkFontMgr_android_factory.cpp
+++ b/src/ports/SkFontMgr_android_factory.cpp
@@ -29,7 +29,7 @@ void SkUseTestFontConfigFile(const char* fontsXml, const char* fallbackFontsXml,
 SkFontMgr* SkFontMgr::Factory() {
     // These globals exist so that Chromium can override the environment.
     // TODO: these globals need to be removed, and Chromium use SkFontMgr_New_Android instead.
-    if ((gTestFontsXml || gTestFallbackFontsXml) && gTestBasePath) {
+    if (0 && (gTestFontsXml || gTestFallbackFontsXml) && gTestBasePath) {
         SkFontMgr_Android_CustomFonts custom = {
             SkFontMgr_Android_CustomFonts::kOnlyCustom,
             gTestBasePath,
{% endhighlight %}

I also have to kill the forwarded after each time, otherwise it just
won't run next time:h

{% highlight bash %}
adb shell kill $(adb shell ps | grep forward | awk '{print $2}')
{% endhighlight %}

Another patch I had to apply was

{% highlight diff %}
{% endhighlight %}


[Downloading and Building]: https://source.android.com/source/requirements.html

---
layout: post
title:  Telemetry on Debian
date:   2015-11-25
categories: chromium
---

Running telemetry on Debian.

First jump through some hoops:

{% highlight bash %}
/home/davve/src/blink/src/tools/telemetry/third_party/gsutilz/gsutil config
{% endhighlight %}

Be careful to read the instructions, product id can be left to 0.

Then apply the following patch on jessie:

{% highlight diff %}
diff --git a/tools/telemetry/telemetry/internal/platform/linux_platform_backend.py b/tools/telemetry/telemetry/internal/platform/linux_platform_backend.py
index 70e6b0d..73080a8 100644
--- a/tools/telemetry/telemetry/internal/platform/linux_platform_backend.py
+++ b/tools/telemetry/telemetry/internal/platform/linux_platform_backend.py
@@ -51,7 +51,7 @@ class LinuxPlatformBackend(
   @decorators.Cache
   def GetOSVersionName(self):
     if not os.path.exists('/etc/lsb-release'):
-      raise NotImplementedError('Unknown Linux OS version')
+      return os_version.OSVersion('jessie', '8.2')
 
     codename = None
     version = None
{% endhighlight %}

Not the most general patch in the world of course. But it'll do for me
for the time being.

Another thing was the the regression bot gave me the following command:

{% highlight bash %}
tools/perf/run_benchmark -v --browser=release --output-format=chartjson \
  --also-run-disabled-tests rasterize_and_record_micro.key_mobile_sites_smooth
{% endhighlight %}

but that fails with:

{% highlight bash %}
Traceback (most recent call last):
  RunBenchmark at tools/telemetry/telemetry/internal/story_runner.py:294
    Run(pt, stories, finder_options, results, benchmark.max_failures)
  Run at tools/telemetry/telemetry/internal/story_runner.py:214
    test, finder_options, story_set)
  __init__ at tools/telemetry/telemetry/page/shared_page_state.py:75
    self._test, finder_options)
  _GetPossibleBrowser at tools/telemetry/telemetry/page/shared_page_state.py:102
    possible_browser = self._FindBrowser(finder_options)
  _FindBrowser at tools/telemetry/telemetry/page/shared_page_state.py:97
    '\n'.join(browser_finder.GetAllAvailableBrowserTypes(finder_options)))
BrowserFinderException: No browser found.
{% endhighlight %}


{% highlight bash %}
tools/perf/run_benchmark -v --browser=content-shell-release --output-format=chartjson \
  --also-run-disabled-tests rasterize_and_record_micro.key_mobile_sites_smooth
{% endhighlight %}

seem to work. This it perhaps because I build content_shell rather
than chrome(ium).

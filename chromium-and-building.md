## Building

#### GN Args / Build Configuration

 - [Linux Build Instructions](https://chromium.googlesource.com/chromium/src/+/master/docs/linux/build_instructions.md)
 - [macOS Build Instructions](https://chromium.googlesource.com/chromium/src/+/master/docs/mac_build_instructions.md)
 - [GN build configuration docs](https://www.chromium.org/developers/gn-build-configuration)

My GN args:

```
# Set build arguments here. See `gn help buildargs`.
use_goma = true
is_debug = true
symbol_level = 2
blink_symbol_level = 2
dcheck_always_on = true
enable_nacl = false
```

#### Downloading and checking out Gerrit patches

```sh
mv ~/Downloads/e6090d1.diff.base64 ../gtanzer-3.diff.base64
base64 --decode ../gtanzer-3.diff.base64 > patch.diff
scp patch.diff farolino.c.googlers.com:/usr/local/google/home/domfarolino/Desktop/Git/chromium/
```

#### Common commands and targets
```
$ git rebase-update && gclient sync
$ autoninja -C out/Default chrome blink_tests browser_tests content_browsertests net_unittests
# All targets I commonly build:
$ autoninja -C out/Debug chrome browser_tests content_browsertests components_browsertests headless_browsertests interactive_ui_tests content_unittests blink_tests blink_platform_unittests unit_tests url_unittests services_unittests webkit_unit_tests chromedriver_py_tests
```

When using Goma, it can be convenient to still be able to perform incremental builds
without internet access. To avoid having to switch the GN arg `use_goma = false` and
recompiling entirely, you can just use the following in the terminal:

```
export GOMA_DISABLED=true
```

#### Running specific trybots

This can be done by selecting specific bots with the Gerrit UI, or by including
the following lines/syntax in the CL's commit description:

```
Cq-Include-Trybots: luci.chromium.try:linux-mbi-mode-per-render-process-host, <etc>
```

See
https://user-images.githubusercontent.com/9669289/191871764-f9d05c21-e9a6-4566-8fed-93f65e3d45a5.png
for example.

#### Bisect builds

https://www.chromium.org/developers/bisect-builds-py

#### Useful vim things:

https://www.freecodecamp.org/news/7-vim-tips-that-changed-my-life/

#### Unix find

Since I always seem to forget how to use it:
https://stackoverflow.com/questions/656741/find-file-in-directory-from-command-line.

## Debugging

The LLDB debugger for macOS is what I primarily use. Historically just invoking
`lldb ./out/Debug/binary_here` was enough to debug Chromium, however now we must
also follow the [lldbinit docs](https://chromium.googlesource.com/chromium/src/+/master/docs/lldbinit.md).

Also, for some reason to attach `lldb` to the Network Service process you must
[run `lldb` as root](https://groups.google.com/a/chromium.org/forum/#!topic/network-service-dev/QzPyRlxm_1A).
This means your lldb will be consulting the root user's `~/.lldbinit` file, so
you might also need to follow the above lldbinit instructions for the root user's
`~/.lldbinit` too.

#### ptrace yama

If you're having trouble connecting `lldb` to a process, getting the error
`Error [...] exiting` or something, you probably need to do the following:

#### Screen for long debugging sessions

Sometimes you have extremely long debugging sessions that last more than a
workday, and require you do them longer than you have access to one machine. I
do these by ssh'ing into a Cloudtop. But of course I have to ssh from multiple
machines throughout the day, so I use `screen`. See the following links for tips
on using `screen`: https://linuxize.com/post/how-to-use-linux-screen/.

```sh
sudo -i
echo 0 > /proc/sys/kernel/yama/ptrace_scope
```

See https://stackoverflow.com/questions/22749015/cannot-debug-with-gdb-in-ubuntu
for more details.

#### LLDB surrounding / preview lines of code

LLDB conveniently displays ten lines of code surrounding your debugger's
position. You can increase this for a more full picture:
https://stackoverflow.com/a/52284297/3947332.

#### When LLDB TOT is broken...

 - Things in https://buganizer.corp.google.com/issues/190227046
 - `/google/src/files/377349811/depot/google3/third_party/lldb/stable/installs/bin/lldb ./out/Debug/{chrome, content_browsertests, ...}`

### Debugging a renderer process

If you want to debug a renderer process, you have two choices:

 - Run Chrome as usual, then go to HamburgerMenu > More Tools > Task Manager,
   use the pid of the renderer process of interest for LLDB
 - If you need to debug something in a renderer's initialization code that you
   can't attach a breakpoint to quick enough, launch Chrome with the
   `--renderer-startup-dialog` flag. This will spit out a pid in the terminal
   for the renderer that was created, that you can use as the pid for LLDB.
   Alternatively, you can add `sleep(20)` or so in `//content/renderer/renderer_main.cc`
   => `RendererMain()`

These days, it seems that to debug a renderer process you'll need to run
Chromium with the `--no-sandbox` flag.

### Debugging the browser process

Like before, you have a few choices:

 - Run Chrome as usual, then go to HamburgerMenu > More Tools > Task Manager,
   use the pid of the browser process for LLDB.
 - If you need to debug something early in the browser process's initialization
   code that you can't attach a breakpoint to quick enough, you have two options:
    - Add `sleep(20);` in `//content/app/content_main_runner_impl.cc` =>
      `ContentMainRunnerImpl::RunServiceManager()`
    - jbroman@ says that you can also launch Chrome with the `--wait-for-debugger`
      command line flag, which sounds similar to the `--renderer-startup-dialog` flag


## Testing

Testing a non-unit-test from a remote device will likely result in the test
failing due to display issues. This requires running  `export DISPLAY=:20` to
fix.

#### Google Tests

Example: the `blink_platform_unittests` and `blink_unittests` (previously `webkit_unit_tests`) targets:

```
./out/Debug/blink_unittests --gtest_filter="HTMLPreloadScannerDocumentTest.XHRResponseDocument"
./out/Debug/blink_platform_unittests --gtest_filter="ResourceFetcherTest.UseExistingResource"
./out/Debug/blink_platform_unittests --gtest_filter="ResourceFetcherTest.*"
```

Useful Google Test Flags:

 - No timeout: `--test-timeout=100000000 --test-launcher-timeout=1000000`
 - No retry: `--test-launcher-retry-limit=0`
 - Multiple inclusions/exclusions
   [stackoverflow](https://stackoverflow.com/questions/14018434):
   `--gtest_filter="POSITIVE_PATTERNS:AND_MORE[-NEGATIVE_PATTERNS:AND_MORE]"`
    - Example:
      `--gtest_filter="ResourceFetcherTest.*-ResourceFetcherTest.StartLoadAfterFrameDetach:ResourceFetcherTest.Vary"`

Browser test tips:
https://chromium.googlesource.com/chromium/src/+/master/content/public/test/README.md.

#### Unit tests & runtime enabled features

Runtime enable features have automatically-generated `Scope*` classes to help
enable features for unit tests. See
`https://source.chromium.org/search?q=Scoped.*ForTest%20file:test.cc%20file:blink&sq=`
for example.

#### WPTs & Layout Tests:

 - [WPT](http://github.com/web-platform-tests/wpt) docs
 - [Chromium "web tests"](https://chromium.googlesource.com/chromium/src/+/master/docs/testing/web_tests.md#Running-Web-Tests) docs
 - [WPT local setup](https://web-platform-tests.org/running-tests/from-local-system.html#system-setup)
 - [Trusting root CA](https://web-platform-tests.org/tools/certs/README.html)
   - To easily get `https` tests working locally
 - [testharness.js API](https://web-platform-tests.org/writing-tests/testharness-api.html)
   - Super useful to learn testharness APIs i.e., that `promise_test`s run sequentially,
     how to use `t.step_func()`, or the difference between `add_result_callback()` and
     `add_completion_callback()`. Also it is not super easy to find for some reason I think

Running layout tests:
```
third_party/blink/tools/run_web_tests.py -t Debug http/tests/security/referrer-policy-conflicting-policies.html external/wpt/workers
```

Useful flags:
 - `--no-retry-failures`
 - `--fully-parallel`
   - When running WPTs, your machine spins up an "appropriate" number of content shells
     for parallelization. As the number of remaining WPTs decrease, your machine decreases
     the number of content shells running in parallel. Even when running up to ~80 tests,
     your machine may start many content shells and _quickly_ reduce the number, slowing down
     the run. This flag forces the machine to use as many content shells as it would for an
     enormous number of tests, all the way through the end.

The last set of test results are always available at:

```
file:///Users/domfarolino/Desktop/Git/chromium/src/out/Debug/layout-test-results/results.html
```

Collecting output:

Python server output of the form `wptserve_stderr.txt` and `wptserve_stdout.txt`
appears in `./out/<Dir>/layout-test-results/...

#### Useful run\_web\_tests.py flags

 - `--driver-logging`
 - `--additional-driver-flag=--no-sandbox`
 - `--timeout-ms=100000000`

#### Public and private networks

Sometimes you have to test Chrome's behavior on public and private networks. See
the following resources for doing so:

 - https://source.chromium.org/chromium/chromium/src/+/main:third_party/wpt_tools/wpt.config.json;l=9-13;drc=053d474806089cfbe11c4f2419b753bfe99d5cec
 - https://source.chromium.org/chromium/chromium/src/+/main:third_party/wpt_tools/wpt/tools/wptrunner/wptrunner/environment.py;l=177-181;drc=db5b385f525ee477c733a0170dea6ffe7163a338
 - https://source.chromium.org/chromium/chromium/src/+/main:services/network/public/cpp/network_switches.cc;l=113;drc=f8c933c2bd17344ce7ac61be2ac7725ed840b19f

Here's an example:

```
./out/Debug/chrome --no-sandbox --enable-experimental-web-platform-features --enable-features=FencedFrames,SharedStorageAPI --ip-address-space-overrides=127.0.0.1:8445=private,127.0.0.1:8446=public https://fenced-frames.glitch.me/
```

##### Testharness notes / useful things:

If you want to use a cross-origin in a test, you have two options:

1. [Tests without HTML boilerplate](https://web-platform-tests.org/writing-tests/testharness.html#without-html-boilerplate-window-js)
1. Substitution tests
   - Make the test name of the format `test-name.sub.html` or `test-name.sub.https.html`
   - This makes the test run through the server-side substitutor(?) before it is served
   - Therefore you can use things like the following to create origins:
     - `http://{{domains[www1]}}:{{ports[http][0]}}/html/`
     - `http://{{domains[wwww]}}:{{ports[https][0]}}/html/`
1. Import the `get-host-info` helper
   - `<script src="/common/get-host-info.sub.js"></script>`
   - `URL(get_host_info().HTTPS_REMOTE_ORIGIN + '/worklets/resources/referrer-checker.py');`

Image progressive rendering. There exists a python resource that responds with a
progressively-rendered image, at `/element-timing/resources/progressive-image.py`. You can
see it being used in the following CL:
`https://chromium-review.googlesource.com/c/chromium/src/+/2138994/3/third_party/blink/web_tests/external/wpt/preload/subresource-integrity-partial-image.html#25`.

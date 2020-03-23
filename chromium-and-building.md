## Building

This material was formerly hosted at https://gist.github.com/domfarolino/359a01b65241cbebf1224b666c9d89fd.

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

#### Common commands and targets
```
$ git rebase-update && gclient sync
$ autoninja -C out/Default chrome blink_tests browser_tests content_browsertests net_unittests
```

When using Goma, it can be convenient to still be able to perform incremental builds
without internet access. To avoid having to switch the GN arg `use_goma = false` and
recompiling entirely, you can just use the following in the terminal:

```
export GOMA_DISABLED=true
```

## Debugging

The LLDB debugger for macOS is what I primarily use. Historically just invoking
`lldb ./out/Debug/binary_here` was enough to debug Chromium, however now we must
also follow the [lldbinit docs](https://chromium.googlesource.com/chromium/src/+/master/docs/lldbinit.md).

Also, for some reason to attach `lldb` to the Network Service process you must
[run `lldb` as root](https://groups.google.com/a/chromium.org/forum/#!topic/network-service-dev/QzPyRlxm_1A).
This means your lldb will be consulting the root user's `~/.lldbinit` file, so
you might also need to follow the above lldbinit instructions for the root user's
`~/.lldbinit` too.


## Testing

#### Google Tests

Example: the `blink_platform_unittests` and `blink_unittests` (previously `webkit_unit_tests`) targets:

```
./out/Debug/blink_unittests --gtest_filter="HTMLPreloadScannerDocumentTest.XHRResponseDocument"
./out/Debug/blink_platform_unittests --gtest_filter="ResourceFetcherTest.UseExistingResource"
./out/Debug/blink_platform_unittests --gtest_filter="ResourceFetcherTest.*"
```

Useful Google Test Flags:

```
--test-launcher-retry-limit=0
```

Multiple [inclusions/exclusions](https://stackoverflow.com/questions/14018434):

```
--gtest_filter="POSITIVE_PATTERNS:AND_MORE[-NEGATIVE_PATTERNS:AND_MORE]"
```

Example:

```
--gtest_filter="ResourceFetcherTest.*-ResourceFetcherTest.StartLoadAfterFrameDetach:ResourceFetcherTest.Vary"
```

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
python third_party/blink/tools/run_web_tests.py -t Debug http/tests/security/referrer-policy-conflicting-policies.html external/wpt/workers
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

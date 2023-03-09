# Testing

## Google Tests (General)

Example: the `blink_platform_unittests` & `blink_unittests` (previously `webkit_unit_tests`) targets:

```
./out/Debug/blink_unittests --gtest_filter="HTMLPreloadScannerDocumentTest.XHRResponseDocument"
./out/Debug/blink_platform_unittests --gtest_filter="ResourceFetcherTest.UseExistingResource"
./out/Debug/blink_platform_unittests --gtest_filter="ResourceFetcherTest.*"
```

Useful gtest flags:

 - No timeout: `--test-timeout=100000000 --test-launcher-timeout=1000000`
 - No retry: `--test-launcher-retry-limit=0`
 - Multiple inclusions/exclusions [stackoverflow](https://stackoverflow.com/questions/14018434):
   `--gtest_filter="POSITIVE_PATTERNS:AND_MORE[-NEGATIVE_PATTERNS:AND_MORE]"`
    - Example:
      `--gtest_filter="ResourceFetcherTest.*-ResourceFetcherTest.StartLoadAfterFrameDetach:ResourceFetcherTest.Vary"`

## Browser tests

#### SSH & Display

Running a non-unit-test from a remote device over SSH will likely result in the test
failing due to display issues. This requires running `export DISPLAY=:20` to fix.

#### Misc tips

Browser test tips: https://chromium.googlesource.com/chromium/src/+/master/content/public/test/README.md.

## Unit tests

#### Integrating `RuntimeEnabledFeatures`

Runtime enable features have automatically-generated `Scope*` classes to help
enable features for unit tests. See
`https://source.chromium.org/search?q=Scoped.*ForTest%20file:test.cc%20file:blink&sq=`
for example.

## WPTs & Layout Tests

Misc useful links:

 - [WPT GitHub](http://github.com/web-platform-tests/wpt)
 - [testharness.js API docs](https://web-platform-tests.org/writing-tests/testharness-api.html)
   - Super useful to learn testharness APIs i.e., that `promise_test`s run sequentially,
     how to use `t.step_func()`, or the difference between `add_result_callback()` and
     `add_completion_callback()`. Also it is not super easy to find for some reason I think
 - [Chromium Running Web Tests](https://chromium.googlesource.com/chromium/src/+/master/docs/testing/web_tests.md#Running-Web-Tests) docs
 - [WPT local setup](https://web-platform-tests.org/running-tests/from-local-system.html#system-setup)
 - [Trusting root CA](https://web-platform-tests.org/tools/certs/README.html)
   - To easily get `https` tests working locally

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
     your machine may start many content shells and _quickly_ reduce the number, slowing down
     the run. This flag forces the machine to use as many content shells as it would for an
     enormous number of tests, all the way through the end.

#### WPT results

The last set of test results are always available at:

```
./out/Debug/layout-test-results/results.html
```

#### Server output

Python server output can be found at:

```
./out/<Dir>/layout-test-results/wptserve_stdout.txt
./out/<Dir>/layout-test-results/wptserve_stderr.txt
```

#### Useful `run_web_tests.py` flags

 - `--driver-logging`
    - Enable Chromium log streaming to the terminal
 - `--additional-driver-flag=--no-sandbox`
    - Needed(?) when attaching a debugger to a renderer process during a WPT
 - `--timeout-ms=100000000`

#### Cross-origin tests

If you want to use a cross-origin in a test, you have two options:

1. Substitution tests
   - Make the test name of the format `test-name.sub.html` or `test-name.sub.https.html`
   - This makes the test run through the server-side substitutor(?) before it is served
   - Therefore you can use things like the following to create origins:
     - `http://{{domains[www1]}}:{{ports[http][0]}}/html/`
     - `http://{{domains[wwww]}}:{{ports[https][0]}}/html/`
1. Import the `get-host-info` helper
   - `<script src="/common/get-host-info.sub.js"></script>`
   - `URL(get_host_info().HTTPS_REMOTE_ORIGIN + '/worklets/resources/referrer-checker.py');`

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

#### Misc WPT/testharness things

 - Without HTML boilerplate: https://web-platform-tests.org/writing-tests/testharness.html#without-html-boilerplate-window-js
 - Image progressive rendering. There exists a python resource that responds with a
progressively-rendered image, at `/element-timing/resources/progressive-image.py`. You can
see it being used in the following CL:
`https://chromium-review.googlesource.com/c/chromium/src/+/2138994/3/third_party/blink/web_tests/external/wpt/preload/subresource-integrity-partial-image.html#25`.

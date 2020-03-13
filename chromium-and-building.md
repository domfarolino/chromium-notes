## Building

This material was formerly hosted at https://gist.github.com/domfarolino/359a01b65241cbebf1224b666c9d89fd.

```
git rebase-update && gclient sync
autoninja -C out/Default chrome blink_tests browser_tests content_browsertests net_unittests
```
## Testing

### Google Tests

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

### Layout Tests:

Running layout tests:
```
python third_party/blink/tools/run_web_tests.py -t Debug --no-retry-failures http/tests/security/referrer-policy-conflicting-policies.html external/wpt/workers
```

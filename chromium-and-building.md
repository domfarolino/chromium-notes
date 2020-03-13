## Building

This material was formerly hosted at https://gist.github.com/domfarolino/359a01b65241cbebf1224b666c9d89fd.

```
git rebase-update && gclient sync
ninja -C out/Debug blink_tests | chrome | content_browsertests | blink_unittests | blink_platform_unittests
```
## Testing

### Google Tests

The `blink_platform_unittests` and `blink_unittests` (previously `webkit_unit_tests`) targets:

```
./out/Debug/blink_unittests --gtest_filter=HTMLPreloadScannerDocumentTest.XHRResponseDocument
./out/Debug/blink_platform_unittests --gtest_filter=ResourceFetcherTest.UseExistingResource
```

The `browser_tests` target:

```
./out/Debug/browser_tests --gtest_filter=DevToolsSanityTest.TestRawHeadersWithRedirectAndHSTS
```

Useful Google Test Flags:

```
--test-launcher-retry-limit=0
```

### Layout Tests:
Running layout tests:
```
python third_party/blink/tools/run_web_tests.py -t Debug --no-retry-failures http/tests/security/referrer-policy-conflicting-policies.html
```

This material was previously hosted at https://gist.github.com/domfarolino/7fbfb2640cbb49a6c8791be3d8ae49a1.

```sh
# https://chromium.googlesource.com/chromium/src/+/lkcr/docs/mac_build_instructions.md
# Update source
git rebase-update
gclient sync
# Build from source
ninja -C out/Default chrome
# Run chromium
out/Default/Chromium.app/Contents/MacOS/Chromium
```

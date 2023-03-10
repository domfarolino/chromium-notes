# Chromium notes

![Chrome logo](https://logodownload.org/wp-content/uploads/2017/05/google-chrome-logo-1.png)

## Building

 - [Linux Build Instructions](https://chromium.googlesource.com/chromium/src/+/master/docs/linux/build_instructions.md)
 - [macOS Build Instructions](https://chromium.googlesource.com/chromium/src/+/master/docs/mac_build_instructions.md)
 - [GN build configuration docs](https://www.chromium.org/developers/gn-build-configuration)

#### GN Args / Build Configuration

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
<Download patch from Gerrit site>

$ mv ~/Downloads/e6090d1.diff.base64 ../patch-name.diff.base64
$ base64 --decode ../patch-name.diff.base64 > patch.diff
$ scp patch.diff farolino.c.googlers.com:/usr/local/google/home/domfarolino/Desktop/Git/chromium/

# On remote machine:
$ git apply ../patch.diff
$ git cl issue XXXXXX
```

#### Common commands and targets

```sh
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

#### Unix find

Since I always seem to forget how to use it:
https://stackoverflow.com/questions/656741/find-file-in-directory-from-command-line.

#### Bisect builds

https://www.chromium.org/developers/bisect-builds-py

#### Useful vim things:

https://www.freecodecamp.org/news/7-vim-tips-that-changed-my-life/

## Testing

See [testing.md](./testing.md).

## Debugging

See [debugging.md](./debugging.md).

## Misc

 - [benchmarks.md](./benchmarks.md)
 - [cs-searching.md](./cs-searching.md)
 - [oh-my-zsh.md](./oh-my-zsh.md)
 - [vim.md](./vim.md)

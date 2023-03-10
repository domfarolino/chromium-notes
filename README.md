# Chromium notes

![Chrome logo](https://logodownload.org/wp-content/uploads/2017/05/google-chrome-logo-1.png)

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
<Download patch from Gerrit site>

$ mv ~/Downloads/e6090d1.diff.base64 ../patch-name.diff.base64
$ base64 --decode ../patch-name.diff.base64 > patch.diff
$ scp patch.diff farolino.c.googlers.com:/usr/local/google/home/domfarolino/Desktop/Git/chromium/
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

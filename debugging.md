# Debugging

The LLDB debugger for macOS is what I primarily use. Historically just invoking
`lldb ./out/Debug/binary_here` was enough to debug Chromium, however now we must
also follow the [lldbinit docs](https://chromium.googlesource.com/chromium/src/+/master/docs/lldbinit.md).

Also, for some reason to attach `lldb` to the Network Service process you must
[run `lldb` as root](https://groups.google.com/a/chromium.org/forum/#!topic/network-service-dev/QzPyRlxm_1A).
This means your lldb will be consulting the root user's `~/.lldbinit` file, so
you might also need to follow the above lldbinit instructions for the root user's
`~/.lldbinit` too.

## Debugging a renderer process

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

## Debugging the browser process

Like before, you have a few choices:

 - Run Chrome as usual, then go to HamburgerMenu > More Tools > Task Manager,
   use the pid of the browser process for LLDB.
 - If you need to debug something early in the browser process's initialization
   code that you can't attach a breakpoint to quick enough, you have two options:
    - Add `sleep(20);` in `//content/app/content_main_runner_impl.cc` =>
      `ContentMainRunnerImpl::RunServiceManager()`
    - jbroman@ says that you can also launch Chrome with the `--wait-for-debugger`
      command line flag, which sounds similar to the `--renderer-startup-dialog` flag

## Misc

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
$ sudo -i
$ echo 0 > /proc/sys/kernel/yama/ptrace_scope
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

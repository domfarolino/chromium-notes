# Applying a patch from Gerrit

```sh
$ mv ~/Downloads/f6db43a.diff.base64 ..
$ mv ../f6db43a.diff.base64 image-loader-task-base-url.diff.base64
$ base64 --decode ../image-loader-task-base-url.diff.base64 > patch.diff
$ git apply patch.diff
$ ...
$ git cl issue XXXXXX
```

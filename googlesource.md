# googlesource

## Searching through commits (useful for a bisect or something)

All changes between two hashes:
 - `https://chromium.googlesource.com/chromium/src/+log/aee9c1b8fa32ebd90e4aa666cc109ab9bcb20ab0..46992f0262726385fff32da88073b2836caa780c/?n=1000000`
 - Params: `?n=<num-per-page>`, `?pretty=fuller`

Seeing the `diff` between too commits (also works on commits in a CL, like the example below):
  - `https://chromium.googlesource.com/chromium/src/+/58be486b3568af6985c78529d144502aff43a8ab..b2c7fbc98ca83901a933282b0b9ef0126b4722bb`

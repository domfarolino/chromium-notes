This material was previously hosted at https://gist.github.com/domfarolino/1a513ba579554397a5e9facd0b92d3ed.

```sh
# See https://blog.jasonmeridth.com/posts/use-git-grep-to-replace-strings-in-files-in-your-git-repository/.
git grep -l 'kNoReferrerWhenDowngradeOriginWhenCrossOrigin' | xargs sed -i '' -e 's/kNoReferrerWhenDowngradeOriginWhenCrossOrigin/kStrictOriginWhenCrossOrigin/g'
```

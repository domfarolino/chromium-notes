# grep substitute

Since I can never remember how to do large-scale mechanical changes.
See
https://blog.jasonmeridth.com/posts/use-git-grep-to-replace-strings-in-files-in-your-git-repository/.

```sh
git grep -l 'kNoReferrerWhenDowngradeOriginWhenCrossOrigin' | xargs sed -i '' -e 's/kNoReferrerWhenDowngradeOriginWhenCrossOrigin/kStrictOriginWhenCrossOrigin/g'
```

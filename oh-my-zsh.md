# Oh My Zsh

Things related to my oh-my-zsh setup.

 - [DISABLE GIT PROMPT IN OH MY ZSH](https://www.stevenrombauts.be/2018/04/disable-git-prompt-in-oh-my-zsh/)
   - While this is nice for many repositories, it crushes your soul in
     the Chromium repo because `git status` takes so long, on macOS moreso
     than Linux. Running `gs` manually sometimes is a good tradeoff.
     Speaking of `gs` ...

### Aliases

File: `~/.oh-my-zsh/custom/aliases.zsh`

``` sh
alias cl="clear;ls"
alias zreload="source ~/.zshrc"
alias gs="git status"
```

### `~/.zshrc`

```
# Probably more from my personal laptop that I could put here...
goma_ctl ensure_start
```
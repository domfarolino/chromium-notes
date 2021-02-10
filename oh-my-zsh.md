# Oh My Zsh

Things related to my oh-my-zsh setup.

 - [DISABLE GIT PROMPT IN OH MY ZSH](https://www.stevenrombauts.be/2018/04/disable-git-prompt-in-oh-my-zsh/)
   - While this is nice for many repositories, it crushes your soul in
     the Chromium repo because `git status` takes so long, on macOS moreso
     than Linux. Running `gs` manually sometimes is a good tradeoff.
     Speaking of `gs` ...
   - `git config oh-my-zsh.hide-info 1` or `git config oh-my-zsh.hide-dirty 1` (https://github.com/ohmyzsh/ohmyzsh/issues/9576#issuecomment-755216771)

### Aliases

File: `~/.oh-my-zsh/custom/aliases.zsh`

``` sh
alias cl="clear;ls"
alias zreload="source ~/.zshrc"
alias gs="git status"
```

### Themes

I use the `maran` theme, which replaces "robbyrussell" in `~/.zshrc`. On macOS I use the terminal theme "Novel",
which has a default background color of `#DFDBC4`, which I override the default Linux terminal background color with for consistency.

### `~/.zshrc`

```
# Probably more from my personal laptop that I could put here...
goma_ctl ensure_start
```

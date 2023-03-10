# Oh My Zsh

Things related to my oh-my-zsh setup.

 - [DISABLE GIT PROMPT IN OH MY ZSH](https://www.stevenrombauts.be/2018/04/disable-git-prompt-in-oh-my-zsh/)
   - While this is nice for many repositories, it crushes your soul in
     the Chromium repo because `git status` takes so long, on macOS moreso
     than Linux. Running `gs` manually sometimes is a good tradeoff.
     Speaking of `gs` ...
   - `git config oh-my-zsh.hide-info 1` or `git config oh-my-zsh.hide-dirty 1` (https://github.com/ohmyzsh/ohmyzsh/issues/9576#issuecomment-755216771)

### Aliases

See https://github.com/domfarolino/dotfiles/tree/master/.oh-my-zsh.

### Themes

I use the `maran` theme, which replaces "robbyrussell" in `~/.zshrc`. On macOS I use the terminal theme "Novel",
which has a default background color of `#DFDBC4`, which I override the default Linux terminal background color
with for consistency.

Some information on the Novel theme can be found here:

[![novel](https://raw.githubusercontent.com/lysyi3m/macos-terminal-themes/master/screenshots/novel.png)](https://github.com/lysyi3m/macos-terminal-themes/blob/master/screenshots/novel.png)

# Vim

This material was previously hosted at https://gist.github.com/domfarolino/49f8634e772b676bf99053a74a26275c.

 - [Vim substitution syntax](https://codeyarns.com/2014/10/31/how-to-replace-from-current-line-in-vim/)
   - `:%s/old/new/gc`
     - (g = global, c = ask for confirmation on each one)
     - Performs substitution for the whole file. To do
   - `:,$s/old/new/gc`
     - Performs substitution from current line. See why in the doc above
     - This page is dedicated to vim configuration and notes.

### `~/.vimrc`

```sh
set tabstop=8 softtabstop=0 expandtab shiftwidth=2 smarttab
set ruler
syntax on
set smartindent
set incsearch

# set nosmartindent

autocmd BufEnter *.bs :setlocal filetype=html
```

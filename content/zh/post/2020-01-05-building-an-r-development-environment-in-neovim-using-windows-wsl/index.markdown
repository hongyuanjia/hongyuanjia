---
title: Building an R development environment in neovim using Windows WSL
author: Hongyuan Jia
date: '2020-01-05'
slug: building-an-r-development-environment-in-neovim-using-windows-wsl
categories:
  - tech
tags:
  - R
---

{{% toc %}}

## Motivation

1. Right now [Windows WSL](Windows Subsystem for Linux Documentation) can be well integreated
2. Try to build a neovim(https://neovim.io/)

Inspired by Kun Ren's brilliant work and
[post](https://renkun.me/2019/12/26/writing-r-in-vscode-interacting-with-an-r-session/).

[vscode](https://code.visualstudio.com/) together with Checkout vscode-R,
empowering #rstats with #vscode, new experimental support of session symbol
value on hover, show plot on the fly, view htmlwidgets and shiny apps.

This post will show how to use Windows WSL to build a linux-based toolchain for
R.

## Why just totaly switch to Linux?

There are 2 things that pulled me up from switching to Linux:

### Dropbox smart sync support.

Currently Dropbox on Linux did not have the functionality of so called **smart sync**, which is basically

### Tools that are no good alternative

1. Everything. FSearch is trying to be an alternative but cannot be compared to
   Everything in terms of speed.

2. Office. Office 365.

3. Wechat. There is [electron-wechat](https://github.com/eNkru/freechat)

4. [Total Commander](https://www.ghisler.com) + [VimDesktop](https://github.com/goreliu/vimdesktop)


## Why I use Linux

1. Neovim & GVim are slow on Windows

Right now I have 3 laptops, one ancient Dell XPS 15 L502X, one old Surface Pro
3, and one newly brought Thinkpad X1 Extreme Gen 2.

The Dell XPS 15 L502X is running on [Manjaro
i3](https://manjaro.org/download/community/i3/), still usable


```shell
# add neovim ppa
sudo add-apt-repository ppa:neovim-ppa/unstable
sudo apt update && sudo apt upgrade
sudo apt install zsh tmux neovim ranger nodejs
```

```shell
# install oh-my-zsh
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting

# clone dotfiles
git clone git@github.com:hongyuanjia/dotfiles.git

# install ripgrep
curl -LO https://github.com/BurntSushi/ripgrep/releases/download/11.0.2/ripgrep_11.0.2_amd64.deb
sudo dpkg -i ripgrep_11.0.2_amd64.deb
rm ripgrep_11.0.2_amd64.deb
```

```shell
# install R
## add the relevant GPG key.
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys E298A3A825C0D65DFD57CBB651716619E084DAB9
## add the repository
sudo add-apt-repository 'deb https://cloud.r-project.org/bin/linux/ubuntu bionic-cran35/'
sudo apt update
sudo apt-get install r-base r-base-dev
```

```shell
export DISPLAY=:0
# needed for devtools
sudo apt install libxml2-dev libcurl4-openssl-dev libssl-dev

# needed for units
sudo apt install libudunits2-dev

# use radian
pip3 install -U radian

# install rstudio-server
sudo apt-get install gdebi-core
wget https://download2.rstudio.org/server/bionic/amd64/rstudio-server-1.2.5033-amd64.deb
sudo gdebi rstudio-server-1.2.5033-amd64.deb
```

```vim
" R support
Plug 'jalvesaq/Nvim-R', {'for': ['r', 'rmd']}
Plug 'jalvesaq/R-Vim-runtime', {'for': ['r', 'rmd']}
Plug 'vim-pandoc/vim-pandoc-syntax',  {'for': ['rmd']}

" Autocompletion
Plug 'neoclide/coc.nvim', { 'do': { -> coc#util#install()} }


```

```vim
let g:coc_global_extensions = [
    \ 'coc-r-lsp',
    \ ]
```

```vim
" Radian integration with NVim-R
let R_app = "radian"
let R_cmd = "R"
let R_hl_term = 0
let R_args = []
let R_bracketed_paste = 1
```

```vim
augroup au_R
    au!
    " force showing color column
    au FileType r,rmd setlocal colorcolumn=80

    " use {{{,}}} as fold marker
    au FileType r setlocal foldmethod=marker
    au FileType r setlocal foldmarker={{{,}}}

    " set comment string
    au FileType r setlocal commentstring=#%s
    au FileType r setlocal comments+=b:#'

    au FileType rmd setlocal comments=b:*,b:-,b:+,n:>

    " NVim-R
    autocmd FileType rbrowser nnoremap <buffer><silent> <CR> :call RBrowserDoubleClick()<CR>
    autocmd FileType rbrowser nnoremap <buffer><silent> <2-LeftMouse> :call RBrowserDoubleClick()<CR>
    autocmd FileType rbrowser nnoremap <buffer><silent> <RightMouse> :call RBrowserRightClick()<CR>

    " assign, pipe and data.table assign
    if (has("win32"))
        autocmd FileType r,rmd inoremap <buffer> ½ <c-v><Space>%>%<c-v><Space>
        autocmd FileType r,rmd inoremap <buffer> » <c-v><Space>:=<c-v><Space>
    endif
    autocmd FileType r,rmd inoremap <buffer> <M--> <c-v><Space><-<c-v><Space>
    autocmd FileType r,rmd inoremap <buffer> <M-=> <c-v><Space>%>%<c-v><Space>
    autocmd FileType r,rmd inoremap <buffer> <M-;> <c-v><Space>:=<c-v><Space>

    " devtools integration
    autocmd FileType r,rmd nnoremap <buffer> <LocalLeader>da :RSend devtools::load_all()<cr>
    autocmd FileType r,rmd nnoremap <buffer> <LocalLeader>dd :RSend devtools::document()<cr>
    autocmd FileType r,rmd nnoremap <buffer> <LocalLeader>dt :RSend devtools::test()<cr>
    autocmd FileType r,rmd nnoremap <buffer> <LocalLeader>dc :RSend devtools::check()<cr>
    autocmd FileType r,rmd nnoremap <buffer> <LocalLeader>dr :RSend devtools::build_readme()<cr>

    " doge to generate roxygen2 template
    autocmd FileType r,rmd nnoremap <buffer> <LocalLeader>rO :DogeGenerate<cr>

    " debug
    autocmd FileType r,rmd nnoremap <buffer> <LocalLeader>tb :RSend traceback()<cr>
    autocmd FileType r,rmd nnoremap <buffer> <LocalLeader>sq :RSend Q<cr>
    autocmd FileType r,rmd nnoremap <buffer> <LocalLeader>sc :RSend c<cr>
    autocmd FileType r,rmd nnoremap <buffer> <LocalLeader>sn :RSend n<cr>

    " RMarkdown
    autocmd FileType r nnoremap <buffer> <LocalLeader>ki :call SpinRmd()<CR>
    autocmd FileType r,rmd nnoremap <buffer> <LocalLeader>kk :call RenderRmd()<CR>
    autocmd FileType r,rmd nnoremap <buffer> <LocalLeader>kb :call RenderBook()<CR>
    autocmd FileType r,rmd nnoremap <buffer> <LocalLeader>kp :call RenderChapter()<CR>

    " Blogdown
    autocmd FileType rmd nnoremap <buffer> <LocalLeader>Ss :RSend blogdown::serve_site()<CR>
augroup END
```

```vim
function SpinRmd()
    let rmdPath = expand("%:p")
    if has("win32")
        let rmdPath = substitute(rmdPath, '\\', '/', 'g')
    endif
    let cmd = "RSend knitr::spin('" . rmdPath. "', knit = FALSE, report = FALSE)"
    exec cmd
endfunction
```

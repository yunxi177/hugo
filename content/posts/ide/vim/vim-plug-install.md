+++
title = "Vim Plug Install"
date = 2019-04-20T17:02:57+08:00
tags = ["工欲善其事，必先利其器"]
categories = ["工欲善其事，必先利其器"]
draft = true
commentId="ide-vim-plug-install"

+++

vim-plug 是一个极简的 vim 插件管理器

![](http://pic.artacode.com/installer.gif)

# 安装
vim-plug 的安装十分简单只需要一行命令就可以搞定
# vim
### Linux

```
curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```
### Windows (PowerShell)
```
md ~\vimfiles\autoload
$uri = 'https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim'
(New-Object Net.WebClient).DownloadFile(
  $uri,
  $ExecutionContext.SessionState.Path.GetUnresolvedProviderPathFromPSPath(
    "~\vimfiles\autoload\plug.vim"
  )
)
```

## Neovim

### Linux
```
curl -fLo ~/.local/share/nvim/site/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

### Windows (PowerShell)

```
md ~\AppData\Local\nvim\autoload
$uri = 'https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim'
(New-Object Net.WebClient).DownloadFile(
  $uri,
  $ExecutionContext.SessionState.Path.GetUnresolvedProviderPathFromPSPath(
    "~\AppData\Local\nvim\autoload\plug.vim"
  )
)
```

# 使用
在 vim 配置文件 `~/vimrc` 或 Neovim 配置文件 `~/.config/nvim/init.vim` 中增加 vim-plug 配置项

1. 开始部分 `call plug#begin()`
2. `Plug` 命令用于定义使用的插件列表
3. 结束部分 `call plug#end()` 更新 `&runtimepath` 并且初始化插件系统
    1. 自动执行 `filetype plugin indent on` 和 `syntax enable`. 你也可以在调用后恢复设置，例如: `filetype indent off`, `syntax off`...


用例： 
```
" 指定插件目录
" - Neovim 为: ~/.local/share/nvim/plugged
" - 避免使用 Vim 标准目录名，例如：`plugin`
call plug#begin('~/.vim/plugged')

" 所有配置均使用单引号

" 简化符号; fetches https://github.com/junegunn/vim-easy-align
Plug 'junegunn/vim-easy-align'

" 允许使用任何有效的 git URL
Plug 'https://github.com/junegunn/vim-github-dashboard.git'

" Multiple Plug commands can be written in a single line using | separators
" 多个插件可以使用一个单引号书写，中间用 | 分隔
Plug 'SirVer/ultisnips' | Plug 'honza/vim-snippets'

" 按需加载（紧在使用时加载）
Plug 'scrooloose/nerdtree', { 'on':  'NERDTreeToggle' }
Plug 'tpope/vim-fireplace', { 'for': 'clojure' }

" 使用其他分支
Plug 'rdnetto/YCM-Generator', { 'branch': 'stable' }

" 使用指定tag
Plug 'fatih/vim-go', { 'tag': '*' }

" 插件选项
Plug 'nsf/gocode', { 'tag': 'v.20150303', 'rtp': 'vim' }

" 挂更新后钩子
Plug 'junegunn/fzf', { 'dir': '~/.fzf', 'do': './install --all' }

" 非托管插件 (手动安装和更新)
Plug '~/my-prototype-plugin'

" 初始化插件系统
call plug#end()
```
安装插件

1. 重载 `.vimrc` 
2. 打开 vim  `$ vim` 
3. `:PlugInstall` 安装配置的插件

# 命令行
|命令|描述|
|---|---|
|`PlugInstall [name ...] [#threads]`|安装插件|
|`PlugUpdate [name ...] [#threads]	`|安装或更新插件|
|`PlugClean[!]`|清除不常用目录|
|`PlugUpgrade`|更新 vim-plug 自身|
|`PlugStatus`|检查插件状态|
|`PlugDiff`|检查上次更新和挂起更新的修改|
|`PlugSnapshot[!] [output path]`|生成插件快照|

# 插件选项（Plug options）
|选项|描述|
|---|---|
|`branch/tag/commit`||
|`rtp`||
|`dir`||
|`as`||
|`do`||
|`on`||
|`for`||
|`frozen`||


# 参考链接
[vim-plug](https://github.com/junegunn/vim-plug)
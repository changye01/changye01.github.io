---
title: "zsh: no matches found"
tag:
- linux
- zsh
category:
- linux
date: 2022-04-21 11:53:45
---
如果你使用的是`zsh` ，那么在你使用 `find` 进行通配符 `*` 匹配的时候，会出现 `zsh: no matches found` 的问题
<!--more-->
这是由于 `zsh` 导致的，在缺省的情况下，`zsh` 始终自动解释 `*` ，传不到给 `find` 解释
解决方案:
在 `~/.zshrc` 中追加
```shell
setopt no_nomatch
```
使修改生效，执行`source ~/.zshrc`
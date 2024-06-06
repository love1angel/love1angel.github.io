---
title: self configuration in linux 
date: 2024-06-06 13:23:00
categories:
tags:
---

<!-- more -->

## install

``` bash
apt install build-essential

echo $PATH
```

## bachrc

``` bash
# git
alias mgs="git status"
alias mgc="git commit"
alias mgca="git commit --amend"

# cmake
alias mcmakec="rm -rf build && cmake -S . -B build"
alias mcmakeb="cmake --build build --parallel 32 "
```

## ssh related

``` bash
# generate ssh key pair
ssh-keygen -t ed25519 -C helianthus547@gmail.com

# login without password
ssh-copy-id -i ~/.ssh/id_ed25519.pub heli@192.168.1.113

# login without server information alias
vim ~/.ssh/config

Host WS
    HostName 192.168.1.113
    Port 22
    User heli
    IdentityFile ~/.ssh/id_ed25519

# verify git can ssh use
ssh -T git@github.com
```

## change to nerd font

``` bash
apt install -yqq fontconfig

wget -q -nv https://github.com/ryanoasis/nerd-fonts/releases/download/v3.2.1/JetBrainsMono.zip

unzip JetBrainsMono.zip -d ~/.local/share/fonts

fc-cache -fv

# macbook 
brew search font
brew install font-jetbrains-mono-nerd-font

# vscode
'JetbrainsMonoNL Nerd Font Mono'
```

## tar

``` bash
# zip
## -c create
tar -zcvf xxx.tar.gz source_file

# unzip
## -x extract
tar -zxvf xxx.tar.gz -C path
```

## big or little endian

`lscpu`

``` cpp
#include <bit>
#include <iostream>
 
int main()
{
    if constexpr (std::endian::native == std::endian::big)
        std::cout << "big-endian\n";
    else if constexpr (std::endian::native == std::endian::little)
        std::cout << "little-endian\n";
    else
        std::cout << "mixed-endian\n";
}
```

``` c
union T {
    int i;
    char byte[];
} t;

int main()
{
    t.i = 1;
    t.byte[0] == 1;

    return 
}
```
---
layout: post
title: "FOSSX: OSX Setup For The FOSS Mind"
date: 2013-06-21 14:18
comments: true
categories: [apple,osx,mountain lion,linux,foss,open-source,homebrew,git,vim,zsh,rvm,octopress] 
---

I've recently had to convert from Linux to OSX as my primary workstation, work requirements and all.
It could have been much worse, I could have been forced to work on M$ Windows. ;-) Though, OSX still 
takes some conversion to be amenable to the <abbr title="Free and Open Source Software">FOSS</abbr> mind.

At the very least, I need one-command access to every open-source application in the world! Luckily, 
[Homebrew](http://mxcl.github.io/homebrew/) does just that for OSX, 
[well almost](https://github.com/mxcl/homebrew/tree/master/Library/Formula). Additionally, I need 
[vim](http://www.vim.org) and [zsh](http://www.zsh.org) with all their awesome plugins. Of course, 
[git](http://git-scm.com) is a given. Finally, I need to be able to update my 
[octopress](http://octopress.org) blog!

<!-- more -->

Below are the steps it took for me feel at home.
> NOTE: This assumes you do *NOT* have root access, this is my employer's machine after all. However,
it *DOES* assume that [Command Line Tools for Xcode](https://medium.com/kr-projects/6e54e8c50dc8) is installed.

``` sh
cd $HOME

# These three lines are one, FYI. Seperated for readability
# though they should execute just fine copying and pasting as 3 lines.
mkdir .homebrew && 
curl -L https://github.com/mxcl/homebrew/tarball/master | 
tar xz --strip 1 -C .homebrew

~/.homebrew/bin/brew install git vim

# Setup git
git config --global user.name mitzip
git config --global user.email <my email>
git config --global push.default simple

# Setup Vim with my good friend Matt's config
git clone https://github.com/MattRWallace/vimfiles.git ~/.vim
git clone https://github.com/gmarik/vundle.git ~/.vim/bundle/vundle
ln -s ~/.vim/.vimrc ~/.vimrc
vim +BundleInstall +qall

# zsh setup adapted from
# http://ricochen.wordpress.com/2012/09/30/mac-os-x-lion-zsh-installation-with-brew-guide/
~/.homebrew/bin/brew install --disable-etcdir zsh
~/.homebrew/bin/brew install zsh-completions zsh-lovers
git clone https://github.com/robbyrussell/oh-my-zsh.git .oh-my-zsh
cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
echo "unalias run-help
autoload run-help
HELPDIR=$HOME/.homebrew/share/zsh/helpfiles
fpath=($HOME/.homebrew/share/zsh-completions $fpath)" >> ~/.zshrc 
echo "if [ -x ~/.homebrew/bin/zsh ]; then exec ~/.homebrew/bin/zsh -l; fi" > ~/.profile
echo "export PATH='$HOME/.rvm/bin:$HOME/.homebrew/bin:$HOME/.bin:$PATH'
export EDITOR='$HOME/.homebrew/bin/vim'" > ~/.zprofile

# Lets enter our new awesome Z shell
~/.homebrew/bin/zsh -l

# Setup RVM for Octopress adapted from:
# http://thomasrieder.github.io/blog/2012/09/23/octopress-and-mountain-lion/
curl -L https://get.rvm.io | zsh -s stable
rvm pkg install iconv
rvm install 1.9.3 --with-gcc=clang --with-iconv-dir=$rvm_path/usr
rvm use 1.9.3

# Looks dangerous, but if it's a new OSX install, you're not missing anything
rm -f .bash*

mkdir Projects

# Get previous octopress install and setup it up on new machine
# or use git clone git://github.com/imathis/octopress.git for new install
git clone git://github.com/<yourUser>/<yourOctopress>.git Projects/blog
cd Projects/blog
gem install bundler
bundle install
# or use `rake install` for new install to create branches anew
git checkout -b source origin/source
rake setup_github_pages
# if you have custom domain, fix your _config.yml as 
# `rake setup_github_pages` changes your domain to <user>.github.io
vim _config.yml
```


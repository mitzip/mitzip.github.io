---
layout: post
title: "FOSSX: OSX Setup For The FOSS Mind"
date: 2013-06-21 14:18
comments: true
categories: [apple,osx,mountain lion,linux,foss,open-source,homebrew,git,vim,zsh,rvm,php,python,node.js,octopress] 
---

I've recently had to convert from Linux to OSX as my primary workstation, work requirements and all.
It could have been much worse, I could have been forced to work on M$ Windows. ;-) Though, OSX still 
takes some conversion to be amenable to the <abbr title="Free and Open Source Software">FOSS</abbr> mind.

At the very least, I need one-command access to every open-source application in the world! Luckily, 
[Homebrew](http://mxcl.github.io/homebrew/) does just that for OSX, 
[well almost](https://github.com/mxcl/homebrew/tree/master/Library/Formula). Additionally, I need 
[vim](http://www.vim.org) and [zsh](http://www.zsh.org) with all their awesome plugins. Of course, 
[git](http://git-scm.com) is a given, and a few interpreters are also in order: PHP, Python, Node.js and Ruby.
I need Ruby to update my [octopress](http://octopress.org) blog!

As an added bonus, <!-- more --> during my research, I [ran across](http://code2k.net/blog/2011-07-20/) a fancy 
replacement for the stock OSX Terminal.app, enter [iTerm2.app](http://www.iterm2.com/#/section/documentation/highlights). 
I highly recommend trying it out! I think a descriptive analogy would be, *zsh* is to *bash* what *iTerm2* is to *Terminal.app*.

Below are the steps it took for me feel at home.
> NOTE: This entire guide assumes you do *NOT* have root access, this is my employer's machine after all. However,
it *DOES* assume that [Command Line Tools for Xcode](https://medium.com/kr-projects/6e54e8c50dc8) is installed.

``` sh
cd $HOME

# These three lines are one, FYI. Seperated for readability
# though they should execute just fine copying and pasting as 3 lines.
mkdir .homebrew && 
curl -L https://github.com/mxcl/homebrew/tarball/master | 
tar xz --strip 1 -C .homebrew

# The Essentials
# Python Notes:
# Mountain Lion comes with Python 2.7, however still requires workarounds for installing
# python packages without root. Homebrewed Python works around all that mess outta the box.
# Plus, I need python3. :-) See references below for more info:
# http://docs.python-guide.org/en/latest/starting/install/osx.html
# https://github.com/mxcl/homebrew/wiki/Homebrew-and-Python
~/.homebrew/bin/brew install git vim python python3 node npm

# Link python apps (IDLE and IDLE3) in Applications directory
mkdir ~/Applications && ~/.homebrew/bin/brew linkapps

# Tap homebrew PHP repo and install
~/.homebrew/bin/brew tap josegonzalez/php
~/.homebrew/bin/brew josegonzalez/php/php54-mongo josegonzalez/php/php54-xdebug 
~/.homebrew/bin/brew josegonzalez/php/php54-apc josegonzalez/php/php54-mcrypt 
~/.homebrew/bin/brew josegonzalez/php/phpdocumentor composer

# Setup git
git config --global user.name <my name>
git config --global user.email <my email>
# So `git push` just pushs my branch I'm on, fails if not on remote
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
echo "export PATH='$HOME/.rvm/bin:$HOME/.homebrew/sbin:$HOME/.homebrew/bin:$HOME/.bin:$PATH'
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
## Icing on the Cake
Though this final peice of software is not required for the FOSS mind, it is required for my FOSS mind. 
[Eloquent](https://launchpad.net/eloquent) is the best Bible software I've found for OSX. It runs natively 
written in Objective-C, just as it's Linux counterpart [Xiphos](http://xiphos.org) run natively written in C.

---
title: Developing on OS X Yosemite
date: 2014-10-20
---

<img src="https://dl.dropboxusercontent.com/u/10170977/yosemite.png" width="200" style="float: right;" />
Last week Apple officially released OS X Yosemite and I decided it was finally time to switch having had the beta sat on my hard drive for a number of weeks, too afraid to flip the switch. I decided to record the process of setting up a basic ruby development environment for my the benefit of my future self (..and hopefully you, too).

What follows is *one way* of setting up and configuring OS X 10.10 primarily for web development, take/leave it as you see fit.

## Installing OS X 10.10

Obviously if you plan to upgrade your existing Mavericks install, you can do this through the regular App Store flow. I chose to "fresh install" (backing up first!) and go from there..

### Create an Installation USB drive

Once you've downloaded the Yosemite installer from the Mac App Store, you can use the following to create an installation USB drive (you will need at least 8 Gb to play with).

```sh
# assumes you have a clean USB drive mounted at /Volumes/Untitled
$ sudo /Applications/Install\ OS\ X\ Yosemite.app/Contents/Resources/createinstallmedia --volume /Volumes/Untitled --applicationpath /Applications/Install\ OS\ X\ Yosemite.app --nointeraction
```

_Full instructions on this process can be found over at [Mashable](http://mashable.com/2014/10/17/clean-install-os-x-yosemite/)._

## Sensible Defaults
After you've gone through the usual installation steps, I recommend setting some defaults that will likely save you a fair bit of frustration from the off.

Below are a few gems I pinched from [this handy gist](https://gist.github.com/g3d/2709563) (intended for Mavericks, but most still relevant in Yosemite).

```sh
#Enable repeat on keydown
defaults write -g ApplePressAndHoldEnabled -bool false

#Use current directory as default search scope in Finder
defaults write com.apple.finder FXDefaultSearchScope -string "SCcf"

#Show Path bar in Finder
defaults write com.apple.finder ShowPathbar -bool true

#Show Status bar in Finder
defaults write com.apple.finder ShowStatusBar -bool true

#Show indicator lights for open applications in the Dock
defaults write com.apple.dock show-process-indicators -bool true

#Enable AirDrop over Ethernet and on unsupported Macs running Lion
defaults write com.apple.NetworkBrowser BrowseAllInterfaces -bool true

#Set a blazingly fast keyboard repeat rate
defaults write NSGlobalDomain KeyRepeat -int 0.02

#Set a shorter Delay until key repeat
defaults write NSGlobalDomain InitialKeyRepeat -int 12

#Enable Safari’s debug menu
defaults write com.apple.Safari IncludeInternalDebugMenu -bool true

#Add a context menu item for showing the Web Inspector in web views
defaults write NSGlobalDomain WebKitDeveloperExtras -bool true

#Show the ~/Library folder
chflags nohidden ~/Library
```

## Command Line Tools
As anyone who has done this before will know, OS X doesn't ship with `gcc` installed which becomes rather limiting if you start compiling libraries etc. (which you almost certainly will). In the past the remedy for this has been to download the _Command Line Tools_ `.dmg` from the Apple developer portal or direct from Xcode. When I came to this in Yosemite I found that installers for 10.10 weren't yet available to download, but `xcode-select --install` did the job nicely.

## Homebrew
At this point I *highly* recommend you install [Homebrew](http://brew.sh/) (and probably [Cask](http://caskroom.io/) too):

```sh
# install brew
$ ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
# check install
$ brew doctor
# install cask
$ brew install caskroom/cask/brew-cask
```

## rvm or rbenv
I'm not going to debate the relative merit of [rvm](http://rvm.io/) over [rbenv](https://github.com/sstephenson/rbenv) or visa versa, but you're probably going to want to install one or the other (not both! - that'd be stupid).

```sh
# rvm
$ curl -sSL https://get.rvm.io | bash -s stable 
# rbenv
$ git clone https://github.com/sstephenson/rbenv.git ~/.rbenv
$ echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.zshrc
$ echo 'eval "$(rbenv init -)"' >> ~/.zshrc
```

At this point you'll probably want to install the latest (stable) ruby and set it to default.

## zsh
You probably picked up on the references to `.zshrc` in the previous step, so let's go ahead and install (or not) [oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh):

```sh
# (optional) install latest zsh with homebrew (currently 5.0.6)
$ brew install zsh
# get oh-my-zsh
$ curl -L http://install.ohmyz.sh | sh
```

I recommend spending some time reading the `oh-my-zsh` docs, and setting your `.zshrc` up according to your needs. There is a ton of [great plugins](https://github.com/robbyrussell/oh-my-zsh/wiki/Plugins-Overview) you can enable too :)

## Vim
Obviously optional, but you'd be a fool not to :)

```sh
# get the latest (bundled is a bit old)
$ brew install vim
# install vundle
$ git clone https://github.com/gmarik/Vundle.vim.git ~/.vim/bundle/Vundle.vim
```

Here's a basic `.vimrc` with Vundle and a few sensible defaults (again, edit as required):

```vim
set nocompatible              " be iMproved, required
filetype off                  " required

" highlighting
set relativenumber
syntax on
set background=dark
set cursorline

" tabbing
set expandtab
set shiftwidth=2
set softtabstop=2

" folding settings
set foldmethod=indent
set foldnestmax=10
set nofoldenable
set foldlevel=1

" set the runtime path to include Vundle and initialize
set rtp+=~/.vim/bundle/Vundle.vim
call vundle#begin()

" let Vundle manage Vundle, required
Plugin 'gmarik/Vundle.vim'
Plugin 'altercation/vim-colors-solarized'
Plugin 'airblade/vim-gitgutter'
Plugin 'kien/ctrlp.vim'

" All of your Plugins must be added before the following line
call vundle#end()            " required
filetype plugin indent on    " required

colorscheme solarized
let g:solarized_termcolors=256
```

## Tmux
Not going to go into too much depth here, but there are a few tricks worth mentioning for getting `tmux` to play nicely with OS X.

```sh
# first, install it!
$ brew install tmux
# super useful, more here: https://github.com/ChrisJohnsen/tmux-MacOSX-pasteboard
$ brew install reattach-to-user-namespace
```

Again, here is a base `.tmux.conf` to get started with:

```conf
# Start new session
new-session

# Set prefix to Ctrl-Space
unbind C-b
set -g prefix C-Space
bind Space send-prefix

# https://github.com/ChrisJohnsen/tmux-MacOSX-pasteboard
set-option -g default-command "reattach-to-user-namespace -l zsh"
# uncomment for copy/paste functionality
#bind C-c run "tmux show-buffer | reattach-to-user-namespace pbcopy"
#bind C-v run "reattach-to-user-namespace pbpaste | tmux load-buffer - && tmux paste-buffer"

# Use vi keys
set -gw mode-keys vi

# 256 colour support
set -g default-terminal "screen-256color"

# Show activity in background windows
setw -g monitor-activity on

# Start window/pane numbers from 1
set -g base-index 1
set -g pane-base-index 1

# Set window titles
set -g set-titles on

# Switching panes
bind h select-pane -L
bind j select-pane -D
bind k select-pane -U
bind l select-pane -R

# resize panes
bind-key -r J resize-pane -D 5
bind-key -r K resize-pane -U 5
bind-key -r H resize-pane -L 5
bind-key -r L resize-pane -R 5

# Fixes for ssh-agent
set -g update-environment "SSH_ASKPASS SSH_AUTH_SOCK SSH_AGENT_PID SSH_CONNECTION"

# 1.9 open new panes in $PWD
bind '"' split-window -c "#{pane_current_path}"
bind % split-window -h -c "#{pane_current_path}"
bind c new-window -c "#{pane_current_path}"

# Status bar
set -g status-bg black
set -g status-fg white
set -g status-interval 10
set -g status-left-length 50
set -g status-left "#[fg=green][#I:#P #W] #(whoami)@#(hostname -s)"
set -g status-right "#[fg=colour45]%a %d/%m %H:%M#[fg=default]"
```

You may opt for a fancier status bar like [powerline](https://github.com/Lokaltog/powerline), but I'm not going to go into that (for now).

## Finishing touches
There's going to be plenty more to add/configure, the above should provide a solid base to build on. Below are a couple of useful resources to check out:

* [Install web development tools to Mavericks \(OS X 10.9)](https://gist.github.com/kimmobrunfeldt/350f4898d1b82cf10bce)
* [Mislav's dotfiles](https://github.com/mislav/dotfiles) -- great set of zsh, git, tmux configs
* [VimAwesome](http://vimawesome.com/) -- a library of Vim plugins

Finally, a gem I pinched from Mislav's dotfiles repo, a simple `.irbrc` that includes an _invaluable_ wrapper for `pbcopy`:

```ruby
require 'pp'
require 'irb/completion'

IRB.conf[:PROMPT_MODE] = :SIMPLE

def pbcopy(data)
  File.popen('pbcopy', 'w') { |p| p << data.to_s }
  $?.success?
end
```

## Closing Remarks
Like most things I write, this is neither polished or finished. If you see something that's wrong or could be improved upon, I'd love to hear from you :)

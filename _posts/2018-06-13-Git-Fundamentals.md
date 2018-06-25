---
layout : post
title : Git Fundamentals
---

Git stores snapshots of your repositories. To minimize space usage unmodified
files are stored as pointers to their previous versions. Most other VCSs store
deltas, just the differences between versions. Storing snapshots allows git to
do almost every operation locally, thus making it really fast.

#### Configuration:
_/etc/.gitconfig_ : configuration settings for all the users (**--system**)

_~/.gitconfig_ : only for you (**--global**)

_<some_repository>/.gitconfig_ : project specific configurations, overrides
above settings.

git config --global user.name "bob12"

git config --global user.name bob12@gmail.com

\# lists all the settings\
git config --global --list

\# specific setting\
git config user.name

\# which file is responsible for this key value (**Not Working**)\
git config --show-origin user.name 

git help config (or) git config --help \# manual page for config

git config -h \# concise, shows all available options

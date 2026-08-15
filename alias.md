# Temporary Alias

```bash

# Ask before overwriting or deleting
alias rm='rm -i'
alias cp='cp -i'
alias mv='mv -i'

# Prevent accidental recursive root deletion
alias rm='rm --preserve-root -i'

# Quickly navigate up directory trees 
alias ..='cd ..' 
alias ...='cd ../..' 
alias ....='cd ../../..'

```


# Permanent Alias

```bash

nano ~/.bashrc

alias ll='ls -la'
alias update='sudo apt update && sudo apt upgrade'

# Reload config file
$ source ~/.bashrc

```
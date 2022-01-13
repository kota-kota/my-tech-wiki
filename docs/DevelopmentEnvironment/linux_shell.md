# Linuxシェルの環境設定

## bash

bashの環境設定は、`.bashrc` に記載されます。

### エイリアス

```bash
# alias
alias ls='ls --color=auto'
alias ll='ls -la'
alias lt='ls -lat'
alias hc='who | cut -c -9 | sort | uniq | wc -l'
#alias grep='grep --color=auto -n -R'
alias grep='grep --color=auto -n'
alias cdd='cd ../'
alias cddd='cd ../../'
alias cdddd='cd ../../../'
alias cddddd='cd ../../../../'
alias cdddddd='cd ../../../../../'
alias cddddddd='cd ../../../../../../'
```

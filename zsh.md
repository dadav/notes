# Why would you use zsh?

## global aliases

```shell
# run this command quietly
alias -g Q='>/dev/null 2>&1'
# ex: sleep 1000 Q

# move latest file to foo
alias -g NF='*(.om[1])'
# ex: mv NF foo/
```

## fileext aliases

```shell
# open files with these extensions with nvim
alias -s {xml,json,yml,yaml}=nvim
```

## globbing

Read more about this in `man zshexpn` (took me quite some time to find the correct manpage)

```shell
# empty files
ls *(.L0)

# empty files + dotfiles (D)
ls *(.DL0)

# empty files; recursively
ls **(.L0)

# files smaller than 20 bytes
ls -l *(.L-20)

# files larger than 20 kilobytes
ls -l *(.Lk+20)

# files larger than 2MB
ls -l *(Lm+2)

# smallest file
ls -l *(.oL[1])

# biggest file
ls -l *(.OL[1])

# oldest file
ls -l *(.Om[1])

# latest file
ls -l *(.om[1])

# biggest file recursively; exclude foo
ls -lhS **/*~(foo/*)(.OL[1])

# modified in the last minute (m=minutes,d=days(default),w=week,M=Month)
ls *(.mm0)

# modified today
ls *(.m0)

# not modified today
ls *(.^md0)

# filepermissions 644
ls *(.f644)

# group: root
ls *(.g:root:)

# user: apache
ls *(.u:apache:)

# all files without group write perms
ls **(.:g-w:)

# move logs older than 3 weeks to old/
mv *.log(.mw+3) old/

# only print file that succeed the given command
ls *(e:'[[ -e $REPLY ]]':)
```

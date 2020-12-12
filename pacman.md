# pacman

## cache

```bash
# delete old cache
pacman -Sc

# delete all cache
pacman -Scc
```

## query

```bash
# show all installed packages
pacman -Q

# without versions
pacman -Qq

# orphans
pacman -Qdt

# explicit installed
pacman -Qet

# which package owns this file
pacman -Qo file

# files from an installed package
pacman -Ql package

# files from any package
pacman -Fl package

# package which contains fileX
pacman -F fileX

# package which contains file matched by regex
pacman -Fx regex
```

## remove

```bash
# remove package and unneeded deps
pacman -Rs package

# remove package, deps and deps of deps
pacman -Rss package
```

## extra

```bash
# packages by size
expac -H M '%m\t%n' | sort -h

# last installed packages
expac --timefmt='%Y-%m-%d %T' '%l\t%n' | sort | tail -n 20

# modified files
pacman -Qii | awk '/^MODIFIED/ {print $2}'

# download only
pacman -Syuw --no-confirm
```

# aur packages

```bash
# update only aur packages
yay -Sua

# clean unneeded deps
yay -Ys
```

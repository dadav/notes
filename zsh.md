source: http://zzapper.co.uk/101ZshGlobs.php

# globbing

## by size

### bytes

```shell
# empty files
ls *(.L0)
# empty files + dotfiles (D)
ls *(.DL0)
# empty files; recursively
ls **(.L0)
# files smaller than 20 bytes
ls -l *(.L-20)
# files exactly 20 bytes
ls -l *(.L20)
# files larger than 20 bytes
ls -l *(.L+20)    # list file size more than 20 bytes 
```

### kilobytes

```shell
# files larger than 100KB
ls -l *(Lk+100)
# files smaller than 100KB
ls -l *(Lk-100)
```


### megabytes

```shell
# files larger than 2MB
ls -l *(Lm+2)
# files smaler than 2MB
ls -l *(Lm-2)
```

### ordering

```shell
# smallest file
ls -lhS *(.oL[1])
# biggest file
ls -lhS *(.OL[1])
# biggest file recursively
ls -lhS **(.OL[1])
# biggest file recursively; exclude foo
ls -lhS **~(foo/*)(.OL[1])
# ten biggets files; exclude foo
ls -hlS **~(foo/*)(.OL[1,10])
# five largest files in .php or .inc dir; exclude libs and locallibs
ls -lS **.(php|inc)~(libs|locallibs)/*(.OL[1,5])
```

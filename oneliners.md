# some useful oneliners

```bash
# remove invalid image files
fd -e jpg -e jpeg -e png -0 | xargs -0 -r -P8 -I{} sh -c "identify -regard-warnings -- {} &>/dev/null || { echo {} is invalid. I will remove it now; rm -- {}"
```

```shell
# add subtitle
ffmpeg -i <infile> -i <subtitle> -c copy -c:s mov_text <outfile>
```

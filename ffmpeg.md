```shell
# add subtitle
ffmpeg -i <infile> -i <subtitle> -c copy -c:s mov_text <outfile>

# record screen
ffmpeg -video_size 1024x768 -framerate 25 -f x11grab -i :0.0+100,200 output.mp4
```

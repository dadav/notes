```bash
gource --path path/to/repo --seconds-per-day 0.1 --title "title" --date-format "%F" -1280x720 --file-idle-time 0 --auto-skip-seconds 0.75 --multi-sampling --stop-at-end --key --highlight-users --hide filenames,mouse,progress,bloom, --max-files 0 --background-colour 000000 --font-size 24 --output-ppm-stream - --output-framerate 30 -o - | ffmpeg -y -r 60 -f image2pipe -vcodec ppm -i - -i audio.mp3 -shortest -vcodec libx264 -preset ultrafast -pix_fmt yuv420p -crf 1 -threads 0 -bf 0 output.mp4
```

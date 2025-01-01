# Create timelaps videos with ffmpeg on Linux

First I list the video files in a text file.

```bash
ls > mylist.txt
```
workout with Excel/Calc

```bash
ffmpeg -f concat -safe 0 -i mylist.txt -c copy concat-video.mp4

find *.mp4 | sed 's:\ :\\\ :g'| sed 's/^/file /' > fl.txt; ffmpeg -f concat -i fl.txt -c copy concat-video.mp4; rm fl.txt

ffmpeg -i concat-video.mp4 -filter:v "setpts=0.01*PTS" -an output_tl.mp4
```
## To rename, not needed if you sort in mrgv

```bash
ls -v | cat -n | while read n f; do mv -n "$f" "$n.mp4"; done 

if doesn't rename try to change "$n.mp4" to "$n.mp41" then back to "$n.mp4"
```

## To make alias

```bash
nano ~/.bashrc

alias mrgv="find *.mp4 | sort -n | sed 's:\ :\\\ :g'| sed 's/^/file /' > fl.txt; ffmpeg -f concat -i fl.txt -c copy concat-video.mp4; rm fl.txt"
```

you need to do mrgv to the source file. If you do mrgv folder by folder and then try mrgv to the already merged files it won't work.

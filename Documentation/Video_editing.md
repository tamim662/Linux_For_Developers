# FFmpeg

### 
ffmpeg is a command-line tool that converts audio or video formats including others basic handlings.


### Video Trimming:
```bash
    ffmpeg -i second.avi -vcodec copy -acodec copy -ss 00:00:00 -t 00:00:07 secondd.avi

```
### Combine Audio and Video:
```bash
    ffmpeg -i video.mp4 -i audio.wav -c:v copy -c:a aac output.mp4
```

### Sppedup the Video:
 Here, you can change the value in place of 30 to change the speed
```bash
    ffmpeg -i input.mp4 -vf "setpts=(PTS-STARTPTS)/30" -crf 18 output.mp4

```
### Merge Two Videos:
 Here, you can change the value in place of 30 to change the speed
```bash
    ffmpeg -i vid-1.mp4 -i vid-2.mp4 -filter_complex '[0:v] [1:v] concat=n=2:v=1 [v]' -map '[v]' output.mp4
```

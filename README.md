# ffmpeg scripts

A collection of ffmpeg shell scripts

* combine-clips
* fade-audio
* fade-clip
* fade-video
* fade-video-audio
* loudnorm
* normalize
* subadd
* waveform

## combine-clips

combine video and audio files into new clip

* script usage

```
combine-clip -v video.(mp4|mov|mkv|m4v) -a audio.(m4a|aac|wav|mp3)
```

## fade-audio

cross fade audio between two clips  
and fade the the first clip in and the second clip out

* script usage

```
fade-audio -i clip-1.(mp4|mkv|mov|m4v) -i clip-2.(mp4|mkv|mov|m4v)
```

## fade-clip

fade video and audio in and out

* script usage

```
fade-clip -i video.(mp4|mkv|mov|m4v)
```

## fade-video

cross fade video between two clips

* script usage

```
fade-video -i clip-1.(mp4|mkv|mov|m4v) -i clip-2.(mp4|mkv|mov|m4v)
```

## fade-video-audio

cross fade clips audio and video between two clips

* script usage

```
fade-video-audio -i clip-1.(mp4|mkv|mov|m4v) -i clip-2.(mp4|mkv|mov|m4v)
```

## loudnorm

ffmpeg loudnorm 

* script usage

```
loudnorm -i infile.(mkv|mp4|mov|m4v|m4a|aac|wav|mp3)
```

## normalize

normalize audio levels

* script usage

```
normalize -i infile.(mp4|mkv|mov|m4v|aac|m4a|wav|mp3)
```

## subadd

add subtitles to a video file

* script usage

```
subadd -v video.(mp4|mov|mkv|m4v) -s subtitle.srt
```

## waveform

create a waveform from an audio or video file and save as a png

* script usage

```
waveform -i infile.(mp4|mkv|mov|m4v|wav|aac|m4a|mp3)
```

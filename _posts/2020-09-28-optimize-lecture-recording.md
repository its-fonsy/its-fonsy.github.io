---
layout : post
title: Optimize lecture recording with ffmpeg
---

I recently started the new year in my university and due to COVID-19 I can follow all the classes online. I decided to
record every lecture to be able to review them when I need to study a specific subject. One problem that instantly came up
was the large size of the video that I recorded. After some research on the web I find out a simple command to compress all
my recordings.

Now a folder of **4.96GB** just weight **1.44GB**!

## The recording 
Since all the lectures are based on a presentation there is no need to record at a high framerate. That will save a lot
of space.

This is the command that I use to record a lecture

```
ffmpeg -video_size 1920x1080 -r 10 -f x11grab -i :0.0 -f pulse -ac 1 -i 0 lecture.mkv
```

I simply record the desktop at 10fps and the output is `lecture.mkv`. I decided to output in `.mkv` since is a flexible
format.

## The compression
Recording with the command above give us a file with the size of about **200MB** with a 2 hours lecture. That's not a lot,
but can be less. After reading [this post on reddit][reddit_post] and tried his solution to my videos I was able to reduce
file size by 70%!

[reddit_post]:https://www.reddit.com/r/ffmpeg/comments/iqqxv6/optimizing_lecture_recording/

The magic command is

```
ffmpeg -i lecture.mkv -filter:v fps=5 -b:a 16k lecture.webm
```

Let's see what every flags do:

- `-i lecture.mkv` set the video input
- `-filter:v fps=5 ` set the fps of the output to 5
- `-b:a 16k` compress the audio with a rate of `16kb/s`
- `lecture.webm` change the format of the output to `.webm`

## Automate the compression
I writed a simple shell script to compress all the `.mkv` files in my lecture folder, you can find it [here][script].
It also does some extra stuff.

### Data generation
When done compressing the script generates a file with the size of all the videos in `.mkv` and `.webm` for comparison.

An example of output is:

```
file1	340     133
file2	591     167
file3	258     75
file4	361     81
file5	133     31
file6	135     36
```
The first column is the file name, the second the size of the `.mkv` and the third the size of the `.webm`.
I pipe that file into an `awk` script to see some statistic.

### Fast removing
The last thing that my script does is generate `rm_list`. This file contains the relative path of every video converted.

With that you can delete them with

```
xargs rm < rm_list
```

## Conclusion
I'm really happy with the result that I have achieved, I will save up a lot of space!


[script]:https://github.com/its-fonsy/dotfiles/blob/master/local/.local/bin/compress_lecture

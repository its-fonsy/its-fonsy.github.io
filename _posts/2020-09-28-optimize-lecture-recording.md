---
layout : post
title: Optimize lecture recording with ffmpeg
---

I recently started the new year in my university and due to COVID-19 I can
follow all the classes online. I decided to record every lecture to be able to
review them when I need to study a specific subject. One problem that instantly
came up was the large size of the video that I recorded. After some research on
the web I find out a simple command to compress all my recordings.

## Recording 
Since all the lectures are based on a presentation there is no need to record
at a high framerate. That will save a lot of space.

The command that I use to record a lecture is the following

```
ffmpeg -video_size 1920x1080 -r 10 -f x11grab -i :0.0 -f pulse -ac 1 -i 0 lecture.mkv
```

I simply record the desktop at 10fps and the output is `lecture.mkv`. I decided
to output in `.mkv` since is a flexible format.

## Compression
Recording with the command above give us a file with the size of about
`200MB` with a 2 hours lecture. That's not a lot, but can be shrinked. After
reading [this post on reddit][reddit_post] and tried his solution I was able to
reduce file size by 80%!

[reddit_post]:https://www.reddit.com/r/ffmpeg/comments/iqqxv6/optimizing_lecture_recording/

The command is

```
ffmpeg -i lecture.mkv -filter:v fps=5 -b:a 16k lecture.webm
```

Let's see what every flags do:

- `-i lecture.mkv` set the video input
- `-filter:v fps=5 ` set the fps of the output to 5
- `-b:a 16k` compress the audio with a rate of `16kb/s`
- `lecture.webm` change the format of the output to `.webm`

## Automate the compression

I writed a simple shell script to compress all the `.mkv` files in my lecture
folder, you can find it [here][script]. It also does some extra stuff.

The usage is written on the script itself. It just look recursevly in the
folder specified for `.mkv` files and compress them with the command above.

### Data generation
When done compressing the script generates a file with the size of all the
videos in `.mkv` and `.webm`.
An example of output is:

```
file1	340     133
file2	591     167
file3	258     75
file4	361     81
file5	133     31
file6	135     36
```

The first column is the file name, the second the size of the `.mkv` and the
third the size of the `.webm`. I pipe that file into an `awk` script to see
some statistic.

When previosly I said that the command compress the file by 80% that wasn't
based on one file. The `awk` script that I written is the following

```awk
#!/bin/awk -f
BEGIN {
  lines=0;
  mkv_total=0;
  webm_total=0;
}

{
  # this code is executed once for each line
  # increase the number of files
  lines++;
  # increase the total size, which is field #1
  mkv_total+=$2;
  webm_total+=$3;
}

END {
  print lines " files";
  print "total mkv size is:", mkv_total, "MB";
  print "total webm size is:", webm_total, "MB";
  if (lines > 0 ) {
    print "average of mkv:", mkv_total/lines, "MB";
    print "average of webm:", webm_total/lines, "MB";
    print "webm is", 100-webm_total*100/mkv_total,"per cent smaller then mkv";
  } else {
    print "average is 0";
  }
}
```

that gives as output for my usage

```
235 files
total mkv size is: 69379.2 MB
total webm size is: 12725 MB
average of mkv: 295.231 MB
average of webm: 54.1489 MB
webm is 81.6588 per cent smaller then mkv
```

### Fast removing
The last thing that the script does is generate `rm_list`. This file contains
the relative path of every video compressed. With that you can delete all video
uncompressed with one command

```
xargs rm < rm_list
```

[script]:https://github.com/its-fonsy/dotfiles/blob/dd445eba0decff623847c3307542c8096f50e5d9/scripts/compress_lecture

# Random wallpaper

## Use `feh` to set a random wallpaper every hour

Install `feh`, then set a random image as wallpaper from the pictures folder

    feh --bg-fill --randomize ~/pictures/* 

## Crontab

Since crontab doesn't run under X it needs to know to which display set the
wallpaper. To get the display number

    echo $DISPLAY

then a new crontab rule can be made

    crontab -e

to change wallpaper every hour on display 0 use the following

    0 * * * * DISPLAY=:0 /usr/bin/feh --randomize --bg-fill /FULL/PATH/TO/PICTURES/*

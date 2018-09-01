---
date: 2018-09-01T12:00:00+03:00
lastmod: 2018-09-01T12:00:00+03:00
title: Creating Video Screencaps using FFMPEG and ImageMagick
authors: ["fdalvi"]
categories:
  - techtips
tags:
  - screencaps
  - video processing
  - ffmpeg
  - imagemagick
slug: 2018-09-01-creating-video-screencaps
---

I recently wanted to create screencaps for a large set of videos of various lengths and resolutions - basically screenshot each video 5 times, and then combine these 5 screencaps all into one nice image per video, with all of the images being of consistent size.

{{% center %}}
	{{% figure src="https://github.com/fdalvi/video-screencaps/blob/master/docs/sample%20-%20500px.jpg?raw=true" caption="Sample render from a seaplane ride over Vancouver" %}}
{{% /center %}}

There are a bunch of tools available online that I can either install locally, or upload my video files to get similar results - but given that this was easily achievable using simple tools like `ffmpeg` and `imagemagick`, I decided to whip up a quick script to accomplish this. The script is accessible [here](https://github.com/fdalvi/video-screencaps/blob/master/create_screenshots.sh), and here is a quick walk through so that you can modify it to suit your needs:

First, lets get the input file, what the output file(s) should be called and how many screenshots you'd like to capture:
{{< highlight Bash >}}
video_file=$1
output_file_prefix=$2
number_of_screenshots=5
{{< / highlight >}}

Next, lets use `ffprobe` and some simple calculations to get the total number of seconds in the video. We have to use `head -1` at the end since there may be multiple streams in the video, with all of them usually having the same duration.
{{< highlight Bash >}}
duration=`ffprobe -show_streams ${video_file} 2>&1 | grep "^duration=" | cut -d'=' -f2 | head -1`
{{< / highlight >}}

Now, use some simple arithmetic to get the number of seconds between each screenshot. We subtract `0.1s` from the overall duration of the video to get our last screenshot just before the video ends.
{{< highlight Bash >}}
seek_factor=`echo "scale=5; ($duration-0.1)/($number_of_screenshots-1)" | bc`
{{< / highlight >}}

Run a loop to capture the actual screenshots. We want to seek (`-ss`) to the correct place in the video, capture exactly one frame, save it and exit. This is super efficient, as `ffmpeg` skips over all of the frames that are not of our interest. The utility is clear when the video file is stored remotely (a media server or just some URL off the Internet) - this strategy of letting `ffmpeg` skip over the uninteresting frames first avoids it from downloading the entire video, saving a lot of bandwidth.

{{< highlight Bash >}}
for ss_idx in `seq 0 $((number_of_screenshots-1))`; do
	ss=`echo "${ss_idx} * ${seek_factor}" | bc`
	echo "Capturing screenshot at ${ss}s..."
	ffmpeg -ss ${ss} -i ${video_file} -vframes 1 -f image2 tmp_${ss_idx}.jpg
done
{{< / highlight >}}

Finally, use `montage` from `imagemagick` to put the screenshots all together into one nice image. Here, we choose to use the middle image as our large preview, the the remaining four for the grid below the main image. The geometry is set to output an image that is exactly `500px` wide. A similar set of commands is also used to output a bigger, `1000px` image.
{{< highlight Bash >}}
montage tmp_2.jpg -tile 1x1  -border 2 -geometry 496x+0+0 final_1.jpg
montage tmp_{0,1,3,4}.jpg -tile 2x2  -border 2 -geometry 246x+0+0 final_2.jpg
montage final_1.jpg final_2.jpg -tile 1x2 -geometry +0+0 "${output_file_prefix} - 500px.jpg"
rm -f final_1.jpg final_2.jpg
{{< / highlight >}}

And thats all! You can run this script inside a loop over a large number of video files and get nice screencap previews for all of them! Check out the full script over at GitHub: [https://github.com/fdalvi/video-screencaps/blob/master/create_screenshots.sh](https://github.com/fdalvi/video-screencaps/blob/master/create_screenshots.sh).

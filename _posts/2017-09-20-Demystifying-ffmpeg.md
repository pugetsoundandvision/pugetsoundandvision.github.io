---
layout: post
---

### Demystifying FFmpeg

This is a quick and dirty adaption of my 'Demystifying FFmpeg' talk! Includes an embedded version as well as useful links and cut/paste versions of some of the commands used in the slides.

Links:

* [Presentation Slides in Google](https://docs.google.com/presentation/d/1_DET6E4Al9vSul6nBwraX64K1mw2SYJ7ZN0TiqbZaqU/edit?usp=sharing)
* [ffmprovisr](ffmprovisr: https://amiaopensource.github.io/ffmprovisr/)
* [FFmpeg Filtering Examples](https://trac.ffmpeg.org/wiki/FancyFilteringExamples)
* [FFmpeg Documentation](https://ffmpeg.org/ffmpeg.html)
* [Installation Help by Reto Kromer](https://avpres.net/FFmpeg/#ch1)

### Slides
<iframe src="https://docs.google.com/presentation/d/e/2PACX-1vSyy0wYXXAaj6_a_6iOnWDfrPrI6zNLFfWR4Lh5aT74Mn74P_kW0FmyGVyD_w0W2hNxLWfNayUuJNtL/embed?start=false&loop=false&delayms=5000" frameborder="0" width="480" height="299" allowfullscreen="true" mozallowfullscreen="true" webkitallowfullscreen="true"></iframe>


### Commands used in Slides

**These render a little oddly (extra blank space) in this webpage, but work fine when cut pasted (at least on my machine!) Most commands are written using macOS file paths unless otherwise specified. For windows users, substitute `~power.gif` for your output file.**

#### Slide 5

FFmpeg macOS Homebrew Install:

Install Homebrew by opening Terminal and following homebrew install instructions from [https://brew.sh](https://brew.sh))

Then run:
`brew install ffmpeg --with-sdl2`

Alternately Download from here (OSX and Windows) [https://ffmpeg.org/download.html](https://ffmpeg.org/download.html)

#### Slide 10
Caveat for following slides:

FFmpeg must be installed with freetype and openssl for the following slides. If already installed, run:

`brew reinstall ffmpeg --with-freetype --with-openssl`

#### Slides11/17
OSX Command:

`ffmpeg -f lavfi -i testsrc=500x500 -i http://github.com/privatezero/NDSR/raw/master/heman.tiff -filter_complex "[0:v][1:v]overlay=-30:55,drawtext=fontfile=/Library/Fonts/Andale Mono.ttf:text='I have the power':fontcolor=white:fontsize=40:box=1:boxcolor=black" -r 10 -t 5 ~/desktop/power.gif`

Windows Command:

`ffmpeg -f lavfi -i testsrc=500x500 -i http://github.com/privatezero/NDSR/raw/master/heman.tiff -filter_complex "[0:v][1:v]overlay=-30:55,drawtext=fontfile=/Windows/Fonts/lucon.ttf:text='I have the power':fontcolor=white:fontsize=40:box=1:boxcolor=black" -r 10 -t 5 ~power.gif`

#### Slide 12
`ffmpeg -i http://github.com/privatezero/NDSR/raw/master/heman.tiff ~/desktop/power.gif`

#### Slide 13
`ffmpeg -f lavfi -i testsrc ~/desktop/power.gif`

#### Slide 14
`ffmpeg -f lavfi -i testsrc -r 10 -t 5 ~/desktop/power.gif`

#### Slide 15
`ffmpeg -f lavfi -i testsrc -vf vflip -r 10 -t 5 ~/desktop/power.gif`

#### Slide 16
`ffmpeg -f lavfi -i testsrc=500x500 -i http://github.com/privatezero/NDSR/raw/master/heman.tiff -filter_complex "[0:v][1:v]overlay=-30:55" -r 10 -t 5 ~/desktop/power.gif`
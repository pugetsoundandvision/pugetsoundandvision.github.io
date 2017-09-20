---
layout: post
---

### Background
One of the primary goals of my project at CUNY TV is to prototype a system of perceptual hashing that can be integrated into our archival workflows. Perceptual hashing is a method of identifying similar content using automated analysis; the goal being to eliminate the (often impossible) necessity of having a person look at every item one-by-one to make comparisons. Perceptual hashes function in a similar sense to standard checksums, except instead of comparing hashes to establish exact matches between files at the bit level, they establish similarity of content as would be perceived by a viewer or listener.

There are many examples of perceptual hashing in use that you might already have encountered. If you have ever used Shazam to identify a song, then you have used perceptual hashing! Likewise, if you have done a reverse Google image search, that was also facilitated through perceptual hashing. When you encounter a video on Youtube that has had its audio track muted due to copyright violation, it was probably detected via perceptual hashing.

One of the key differences between normal checksum comparisons and perceptual hashing is that perceptual hashes attempt to enable linking of original items and derivative or modified versions. For example, if you have a high quality FLAC file of a song and make a copy transcoded to MP3, you will not be able to generate matching checksums from the two files. Likewise, if you added a watermark to a digital video it would be impossible to compare the files using checksums. In both of these examples, even though the actual content is almost identical in a perceptive sense, as data it is completely different.

Perceptual hashing methods seek to accomodate these types of changes by applying various transformations to the content before generating the actual hash, also known as a 'fingerprint'. For example, the audio fingerprinting library _Chromaprint_ converts all inputs to a sample rate of 11025 Hz before generating representations of the content in musical notes via frequency analysis which are used to create the final fingerprint. [^1] The fingerprint standard in MPEG-7 that I have been experimenting with for my project does something analogous, generating global fingerprints per frame with both original and square aspect ratios as well as several sub-fingerprints. [^2] This allows comparisons to be made that are resistant to differences caused by factors such as lossy codecs and cropping.

### Hands-on Example
As of the version 3.3 release, the powerful Open Source tool FFmpeg has the ability to generate and compare MPEG-7 video fingerprints.  What this means, is that if you have the most recent version of FFmpeg, you are already capable of conducting perceptual hash comparisons! If you don't have FFmpeg and are interested in installing it, there are excellent instructions for Apple, Linux and Windows users available at [Reto Kromer's Webpage](https://avpres.net/FFmpeg/#ch1).

For this example I used the following video from the University of Washington Libraries' [Internet Archive  page](https://archive.org/details/uwlibraries) as a source.

<iframe src="https://archive.org/embed/NarrowsclipH.264ForVideoPodcasting" width="320" height="240" frameborder="0" webkitallowfullscreen="true" mozallowfullscreen="true" allowfullscreen></iframe>


From this source, I created a GIF out of a small excerpt and uploaded it to Github.

![GIF](https://raw.githubusercontent.com/privatezero/Blog-Materials/master/Tacoma.gif)

Since FFmpeg supports URLs as inputs, it is possible to test out the filter without downloading the sample files! Just copy the following command into your terminal and try running it! (Sometimes this might time out; in that case just wait a bit and try again).

__Sample Fingerprint Command__
{% highlight bash %}
ffmpeg -i https://ia601302.us.archive.org/25/items/NarrowsclipH.264ForVideoPodcasting/Narrowsclip-h.264.ogv  -i https://raw.githubusercontent.com/privatezero/Blog-Materials/master/Tacoma.gif -filter_complex signature=detectmode=full:nb_inputs=2 -f null -
{% endhighlight %}

__Generated Output__
{% highlight bash %}
[Parsed_signature_0 @ 0x7feef8c34bc0] matching of video 0 at 80.747414 and 1 at 0.000000, 48 frames matching
[Parsed_signature_0 @ 0x7feef8c34bc0] whole video matching
{% endhighlight %}

What these results show is that even though the GIF was of lower quality and different dimensions, the FFmpeg signature filter was able to detect that it matched content in the original video starting around 80 seconds in!

For some further breakdown of how this command works (and lots of other FFmpeg commands), see the example at [__ffmprovisr__](https://amiaopensource.github.io/ffmprovisr/#compare_video_fingerprints).

### Implementing Perceptual Hashing at CUNY TV
At CUNY we are interested in perceptual hashing to help identify redundant material, as well as establish connections between shows utilizing identical source content. For example, by implementing systemwide perceptual hashing, it would be possible to take footage from a particular shoot and programmatically search for every production that it had been used in. This would obviously be MUCH faster than viewing videos one by one looking for similar scenes.

As our goal is collection wide searches, three elements had to be added to existing preservation structures: A method for generating fingerprints on file ingest, a database for storing them and a way to compare files against that database. Fortunately, one of these was already essentially in place. As other parts of my residency called for building a database to store other forms of preservation metadata, I had an existing database that I could modify with an additional table for perceptual hashes. The MPEG-7 system of hash comparisons uses a three-tiered approach to establish accurate links, starting with a 'rough fingerprint' and then moving on to more granular levels.  For simplicity and speed (and due to us not needing accuracy down to fractions of seconds) I decided to only store the components of the 'rough fingerprint' in the database. Each 'rough fingerprint' represents the information contained in ninety frame segments. Full fingerprints are stored as sidecar files in our archival packages.

As digital preservation at CUNY TV revolves around a set of microservices, I was able to write a script for fingerprint generation and database insertion that can be run on individual files as well as inserted as necessary into preservation actions (such as AIP generation). This script is available [here](https://github.com/mediamicroservices/mm/blob/master/makefingerprint) at the mediamicroservices repository on github. Likewise, my script for generating a hash from an input and comparing it against values stored in the database can be found [here](https://github.com/mediamicroservices/mm/blob/master/searchfingerprint).

I am now engaged with creating and ingesting fingerprints into the database for as many files as possible. As of writing, there are around 1700 videos represented in the database by over 1.7 million individual fingerprints. The image below is an example of current search output.

![Breweryguy](https://raw.githubusercontent.com/privatezero/Blog-Materials/master/breweryguy.jpg)

During search, fingerprints are generated and then compared against the database, with matches being reported in 500 frame chunks. Output is displayed both in the terminal as well as superimposed on a preview window.  In this example, a 100 second segment of the input returned matches from three files, with search time (including fingerprint generation) taking about 25 seconds.

The most accurate results so far have been derived from footage of a more distinct nature (such as the example above). False positives occur more frequently with less visually unique sources, such as interviews being conducted in front of a uniform black background. While there are still a few quirks in the system, testing so far has been successful at identifying footage reused across multiple broadcasts with a manageably low amount of false results. Overall, I am very optimistic for the potential enhancements that perceptual hashing can add to CUNY TV archival workflows!



[^1]: Lalinský, L. (2011, January 18). How Does Chromaprint Work? [https://oxygene.sk/2011/01/how-does-chromaprint-work/](https://oxygene.sk/2011/01/how-does-chromaprint-work/)
[^2]: [Chiariglione, L. (2014). Mpeg representation of digital media. Springer, 84-85.](http://www.worldcat.org/oclc/902763394)

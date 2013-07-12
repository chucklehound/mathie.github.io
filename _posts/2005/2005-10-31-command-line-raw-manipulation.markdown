---
layout: post
title: Command line RAW manipulation
date: 2005-10-31 13:07:59 +00:00
categories:
- Geekery
- Photography
---
Just a quickie this morning, since I seem to be insanely busy (and insanely tired from a mad weekend, which I'm sure I'll post more about later!).  Here's a piece of shell script (only tested on [bash](http://www.gnu.org/software/bash/bash.html)) which will take a bunch of raw images and create lightweight JPEG images for browsing through.  It requires [NetPBM](http://netpbm.sourceforge.net/) and [dcraw](http://www.cybercom.net/~dcoffin/dcraw/)

{% highlight bash %}
export TIMEFORMAT="%R seconds"
for i in *.dng; do
  echo -n "$i ... "
  time dcraw -c $i \
    | pamscale 0.25 \
    | ppmtojpeg -quality=60 > ${i/dng/jpg}
done
{% endhighlight %}

That takes your original raw image, converts it to NetPBM's PPM format, scales it down to a quarter of the original size, and outputs a JPEG file.  Just for bonus points, it also tells you how long the conversion has taken.  Which, on my laptop, is about 20 - 30 seconds each...  Still, it means the resulting images can be quickly viewed, and even passed around elsewhere (by email or uploading to a web site) without abusing vast quantities of bandwidth!

**Update** Or, if you use the <code>-h</code> switch to dcraw, it'll produce half-size images, which gives the rest less data to work with!  So it's now completing each image in 4 - 5 seconds.  I've also added <code>-a</code> to do automatic white balance correction.  Which makes the bash function I've just committed:

{% highlight bash %}
raw_to_web_jpegs()
{
    (
        [ -d jpegs/ ] || mkdir jpegs
        export TIMEFORMAT="%R seconds"
        for i in "$@"; do
            echo -n "$i ... "
            time dcraw -a -c -h "$i" \
                | pamscale 0.5 \
                | ppmtojpeg -quality=60 > "jpegs/${i/dng/jpg}"
        done
    )
}
{% endhighlight %}

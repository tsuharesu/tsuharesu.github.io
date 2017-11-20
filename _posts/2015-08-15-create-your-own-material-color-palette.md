---
title: "Create your own material color palette"
category: dev
---
Today I was improving some colors on the app I'm working on. There is a primary color from the product but the darker color didn't feel right. I started to search of ways to generate my own palette of color, like Google did on their design site.

The first thing I learned is that Google uses HSL to generate those colors, not RGB like we do on our colors.xml ([source](http://graphicdesign.stackexchange.com/a/44646)). This explains why always my colors felt strange. I was using some sites to darken RGB colors and because of this they were not good enough.

On my way to find how to transform from RGB to HSL, I found a way to do it on Java. This is good because sometimes you need to change some color but don't want to define it on xml, or you really don't know which color you will have (like when getting the most prominent color in an image). So, if you need to convert RGB <-> HSL, Android has you covered ([source](http://stackoverflow.com/a/4928826)):

{% highlight java %}
float[] hsv = new float[3];
int color = getColor();
Color.colorToHSV(color, hsv);
hsv[2] *= 0.8f;
color = Color.HSVToColor(hsv);
{% endhighlight %}

~~And then at last I found a way to create a palette that you can copy to your colors.xml. It is not perfect but it worked really great for me. Changing the 700 color from XML to Java generated using 0.8 is practically equals. [http://mcg.mbitson.com/#/](http://mcg.mbitson.com/#/)~~

One thing that I still don't get is how much should I darken or brighten a color or change saturation until I get to the next color in the palette. Google don't tell either. Still some research to do...

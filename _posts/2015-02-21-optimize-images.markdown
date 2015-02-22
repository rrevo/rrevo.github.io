---
layout:  post
title:   Optimizations
date:    2015-02-21
summary: Reduce image sizes
---

### Images

In the previous blog on [Entrepreneurship]({{ site.url }}{{ site.baseurl }}/2015/02/20/entrepreneurship/) I added an image from the Disciplined Entrepreneurship book [poster](http://www.disciplinedentrepreneurship.com/downloads/Disciplined_Entrepreneurship_Poster_Aulet.pdf).

I created a large image of size 1884 × 1844 pixels which ended up becoming a large download of around 2.6 mb. Definitely kills the user experience.

The first optimization I could think of was to reduce the file size. So I resized to 800 × 783 pixels which caused a reduction to around 700 kb. Impressive but not good enough.

The next trick was to reduce the color space of the image. By default the image was using the [RGB color space](https://en.wikipedia.org/wiki/RGB_color_space). Using GIMP I switched to an [Indexed image mode](http://docs.gimp.org/en/gimp-using-web-reducing-file-size.html) which uses at most 256 color values. At zoom the image might look grainy and the colors are an approximation of the original sample space but for a blog that's just fine. At the original size just switching to indexed mode reduced the image size to 585 kb. So I finally reduced the size to 900 × 881 pixels and used reduced colors to reach 182 kb which is below the 200 kb threshold.

| Image | Size (KB) | Savings  |
| ------- |-------------| -----:|
| Original | 2600 | - |
| Reduced size | 700 | 73% |
| Reduced colors | 585 | 77% |
| Reduced size and colors | 182 | 93% |


### Performance

This reminded me of the book [High Performance Web Sites](http://stevesouders.com/hpws/) which I had read a long time back. I've always tried to build performant systems. Most of my work has been in backends for sites. This book actually proved that most optimizations need to be done in the client code rather than the backend. Simple tips like fewer HTTP requests and enabling caching of resources make a huge and noticeable improvement is usability. Ironically this is true even today where users are accessing sites over choppy mobile networks. So always [Profile before optimizing](http://c2.com/cgi/wiki?ProfileBeforeOptimizing).

At work, I've been working on integrating profiling into the application. We are using the [Metrics](https://github.com/dropwizard/metrics) library to provide stats across different areas of the product. We've wrapped the library in an interesting way to be applicable for both an production in an on-prem solution and development for engineers. The next stage of mining that data will happen someday in the future.

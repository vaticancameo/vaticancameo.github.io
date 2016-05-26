---
layout: post
title: How to overlay two audio files with fluent-ffmpeg
categories:
excerpt: "Merge two audio files into one using fluent-ffmpeg"
tags: [javascript, audio, node]
---


  While working on a project called [jabbr](https://github.com/cliffmoney/jabber), I had to merge two audio streams into one file, by overlaying one on top of the other, not just concatenating them. Here's one solution using [fluent-ffmpeg](https://github.com/fluent-ffmpeg/node-fluent-ffmpeg):

{% highlight javascript %}
var command = ffmpeg(fileName1)
//You can add more files by chaining the `.input(fileName)` command
  .input(fileName2)
  .inputOptions('-filter_complex amix=inputs=2:duration=shortest:dropout_transition=1')
  .on('start', function(commandline) {
    console.log('Spawned ffmpeg with command: ' + commandline);
  })
  .on('error', function (err) {
    console.log(err.message);
  })
  .on('end', function () {
    console.log('Merging finished !');
  })
  .save(outputFilename);
{% endhighlight %}

This is the command line that would be spawned:

{% highlight javascript %}
ffmpeg -i fileName1 -i fileName2 -filter_complex amix=inputs=2:duration=shortest:dropout_transition=1 outputFilename
{% endhighlight %}

[Here's](https://ffmpeg.org/ffmpeg-filters.html#amix) the official documentation for the amix filter, which explains in detail the parameter options such as duration and dropout transition.

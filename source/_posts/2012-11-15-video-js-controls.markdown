---
layout: post
title: Video.js lacks refinement
---


The controls are always up in fullscreen. They come up on autoplay and don't go away. I found [this solution](http://help.videojs.com/discussions/problems/1113-control-bar-doesnt-fade-out-in-fullscreen) but it didn't work exactly. Here are the changes we ended up making to controls.js:

{% highlight javascript %}

    diff --git a/src/controls.js b/src/controls.js
    index d5662a5..02ff2d6 100644
    --- a/src/controls.js
    +++ b/src/controls.js
    @@ -1,3 +1,6 @@
    +var videoJSTimer;
    +var firstTime = true;
    +
     /* Control - Base class for all control elements
     ================================================================================ */
     _V_.Control = _V_.Component.extend({
    @@ -31,9 +34,21 @@ _V_.ControlBar = _V_.Component.extend({
         this._super(player, options);

         player.one("play", this.proxy(function(){
    -      this.fadeIn();
    -      this.player.on("mouseover", this.proxy(this.fadeIn));
    -      this.player.on("mouseout", this.proxy(this.fadeOut));
    +      if (firstTime) {
    +        this.fadeIn();
    +        firstTime = false;
    +        videoJSTimer = setTimeout(this.proxy(this.fadeOut), 2000);
    +      }
    +      this.player.addEvent("mouseout", this.proxy(this.fadeOut));
    +      this.player.addEvent("mousemove",function() {
    +        if (videoJSTimer) {
    +            clearTimeout(videoJSTimer);
    +            videoJSTimer = 0;
    +        }
    +        this.controlBar.fadeIn();
    +        videoJSTimer = setTimeout(this.controlBar.proxy(this.controlBar.fadeOut), 3000);
    +    }));
    +  }
         }));

       },

{% endhighlight %}


Also the loading spinner was always showing at the end of videos.


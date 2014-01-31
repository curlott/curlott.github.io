---
layout: post
title: Generative Algorithms
excerpt: |
  An Audiovisual Experiment with Processing, Pure Data and Ableton Live 9.
date:   2014-01-29 14:34:00
categories: Blog Processing
tags: 
  - Processing
  - Pure Data
  - OSC
tools:  LiveOSC
image: lol
feature: <iframe width="100%" height="350" src="http://www.youtube.com/embed/t3XHLunRFz4?showinfo=0&controls=1&theme=light&color=white" frameborder="0" allowfullscreen></iframe>
---

* TOC
{:toc}

##Intro

"I had a course in university" ==where== _I had_ to work with generated algorithms to create something audiovisual. I got fascinated by an example with generated trees of the book [The_Nature_of_Code] by Daniel Shiffman. So I decided to work with that and learn the basics in [Processing] and [Pure_Data]. This article covers the basic setup of my project. It is not intended to teach you programming, fractals or the theory of harmony. 

###test

> **NOTE:** 
> 
> [lol](lol)
> LOL
{:.info}



##The Setup

Since I do not have a Mac at home, I've used my Windows Machine. But it should run on MacOS aswell. The Main parts were: 

* **[Processing]**
    - Did all the visual Stuff and some Note calculations.
* **[Pure_Data] Extended**
    - Was the middleman to let Processing and Ableton Live communicate via [OSC] and [MIDI].
* **[Ableton_Live_9]**
    - Brought the instruments, the beat and some equalizers.

> LOFL
> ROLF
{:.advice}

##Communication between Processing and Pure Data

I started writing some code in [Processing] and played a bit with the examples in [The_Nature_of_Code]. The first thing to communicate with [Pure_Data] was, to set up a connection via OSC 
(I copied and pasted this from an example which I cannot find anymore):

~~~ text

//libraries needed for osc
import oscP5.*;
import netP5.*;

//variables needed for osc
OscP5 oscP5;
NetAddress myRemoteLocation;

//set/change port numbers here
int incomingPort = 12000;
int outgoingPort = 12001;

//set/change the IP address that the OSC data is being sent to
//127.0.0.1 is the local address (for sending osc to an application on the same computer)
String ipAddress = "127.0.0.1";

void setup() {
  //for incoming OSC messages
  oscP5 = new OscP5(this,incomingPort); //port number set above
  
  //for outgoing OSC messages
  myRemoteLocation = new NetAddress(ipAddress, outgoingPort);
  
  //send start note1
  OscMessage Mode = new OscMessage("/note1");
  Mode.add(60);
  oscP5.send(Mode, myRemoteLocation);

  ...

~~~

> warning!
{:.warning}

The second step was setting up [Pure_Data] to receive my `"/note1"`:

![Setup of Pure Data Extended](/images/osc_setup_pde.png)

`mrpeach` is the OSC libary I used. `routeOSC` was used to route between more incoming signals. In this case of only one note, it doesn't make sense. But think of the future here...

Now [Pure_Data] was sending a solid **C** to my selected MIDI port (`media` > `MIDI preferences...`) when I initialized my programm in [Processing]. 

##Communication between Pure Data and Ableton Live 9

To let [Ableton_Live_9] receive my MIDI signals, I had to install [LoopBe1]. This tool is very simple to use. Just select it as your outgoing MIDI port in [Pure_Data] and everything is good to go.

In [Ableton_Live_9] I had to select the [LoopBe1] port in `Preferences` > `MIDI Sync`:

![MIDI Preferences of Ableton_Live_9](/images/ableton_live_midi_prefs.png)


##Communication between Ableton Live 9 and Processing

Here, another tool came in handy: [LiveOSC]

I forgot to install [Python_2.5.1] and wondered why nothing has worked. Damn reading.

So install it correctly and select it in your preferences:

![Midi Preferences of Ableton Live 9 for OSC](/images/ableton_live_osc_prefs.png) 

LiveOSC sents through `port 9001` and receives at `port 9000`. So the next step in [Pure_Data] had to look like this:

![Receiving OSC messages in Pure Data](/images/osc_setup_pde_02.png) 

Don't forget to click the button to connect. I know I could also just go and receive the OSC messages directly in [Processing], but I liked the simplicity of PD and it could come in handy to recieve the beat in PD.

Now the last step was, to receive the messages in Processing:

{% highlight text %}
void oscEvent(OscMessage theOscMessage) {
  // println("theOscMessage: "+theOscMessage);
  ...
}
{% endhighlight %}  

`oscEvent()` gets called whenever we receive a OSC message. 

From here on, everything is up to you. Thank you. 


[Processing]: http://processing.org/
[Pure_Data]: http://puredata.info/
[Ableton_Live_9]: https://www.ableton.com/de/live/new-in-9/
[The_Nature_of_Code]:http://natureofcode.com/book/
[OSC]: http://en.wikipedia.org/wiki/Open_Sound_Control
[MIDI]: http://en.wikipedia.org/wiki/Musical_Instrument_Digital_Interface
[LoopBe1]: http://www.nerds.de/en/download.html
[LiveOSC]: http://livecontrol.q3f.org/ableton-liveapi/liveosc/
[Python_2.5.1]: http://www.python.org/download/releases/2.5.1/



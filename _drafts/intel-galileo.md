---
layout: post

title: Intel Galileo
subtitle: "Microsoft on Devices"
#cover_image: "bridge.jpg"

excerpt: "Intel Galileo and Microsoft join forces to push Windows to any device on the World. Welcome to the Win32 of Things right now."

author:
  name: GerardSoleCa
  link: http://gsole.cat
  twitter: GerardSoleCa
  bio: Software Engineer
  image: gs.jpg
---

Microsoft started a program called Windows on Devices. It's aim was create a Windows port to be included in embedded devices. The first version of this operating system was developed for Intel Galileo Board. Probably, one of the reasons for Microsoft to choose this board was it's i585 CPU. The first windows supported version is the Gen. 1, support for Gen. 2 is on its way!

I was lucky to get a sample from Microsoft (Thanks! Microsoft IoT team!), and after a couple of days testing it really promises. Microsoft has made a really good job, although this Windows version is headless, as this board, it uses the last Windows NT Kernel or at least is what _ver_ command says.

They also have implemented the [Wiring API](http://wiring.org.co){:target="_blank"} so porting Arduino applications is really easy. Remember that this board shares the same socket with Arduino.

![Intel Galileo Board](/images/intelgalileoboard/intelgalileoboard.jpg)

By now, this Windows version has two big handicaps for me (and my ideas):

- __.NET Implementation:__ Although they have ported a small subset of .NET classes it's impossible to develop a fully working program using this language. If you intend to write really simple programs (like the ones that you write when you start programing) it is _fine_.
- __Win32 API:__ Some functions have the same entry points, other are just removed and others are renamed. Is really hard to know exactly what has been implemented. It would be useful to have a better documentation of the API Status.

Finally, I've two basic goals on mind, the first one is port [IOSharp](https://github.com/GerardSoleCa/IOSharp-netmf-Linux) two this platform. But until we have a useful .NET runtime, this will be just out of my control. The second one is use [AlterNative](https://github.com/AlexAlbala/Alter-Native) to translate .NET assemblies to C++ in order to be able to run on this machine. 

Read more information about this fantastic board at [Intel Galileo's page](http://www.intel.com/content/www/us/en/do-it-yourself/galileo-maker-quark-board.html){:target="_blank"} and if you want to know more about the operating system, check [Windows on Devices](https://www.windowsondevices.com){:target="_blank"}.
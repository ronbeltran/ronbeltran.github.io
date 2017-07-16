---
layout: post
title: Learn Objective-C on Ubuntu 
---

Want to learn Objective C but don't have a Mac or can't afford yet to buy one? Worry no more my friend because you can learn and code in Objective C in Ubuntu Linux. I assume that you are planning to make apps on a Mac or in iPhone. If you're like me who wants to master first the basics of Objective C before investing a huge amount of money on Mac, read on.

Installing
===========

Objective C are supported by GNU Compiler Collection in Linux by simply installing the **gcc-objc** package. 

    sudo apt-get install gnustep gnustep-devel gobjc

After successfully installing those packages you have to open your ~/.bashrc file to set up the GNUstep environment to compile programs. Add below line to your ~/.bashrc.

    source /usr/share/GNUstep/Makefiles/GNUstep.sh

Compiling
===========

To test that we had successfully installed the packages we will compile a sample program - as usual the Hello World program again. Create a folder named Helloworld, inside that folder create two files Source.m and GNUmakefile.

    mkdir HelloWorld
    touch HelloWorld\{Source.m,GNUmakefile}

Open HelloWorld\Source.m and add the following codes:

{% highlight c %}
#import <Foundation/Foundation.h>

int main()
{
  NSLog(@"Hello, Objective-C!");
  return 0;
}
{% endhighlight %}

Then you can compile it using GNU Make 'make' command. Open HelloWorld/GNUmakefile and add the following codes.

{% highlight c %}
include $(GNUSTEP_MAKEFILES)/common.make
 
APP_NAME = HelloWorld 
HelloWorld_HEADERS =
HelloWorld_OBJC_FILES = Source.m
HelloWorld_RESOURCE_FILES =
 
include $(GNUSTEP_MAKEFILES)/application.make
{% endhighlight %}

Now you can invoke 'make' command to compile and if you see that no error outputs. There will be directories that will be created in our case since we named the APP_NAME to HelloWorld a directory called HelloWorld.app will be created that contains the compiled executable  file. To run the program invoke the following command:

    openapp ./HelloWorld.app

Looking for a good book on Objective-C? Try [Learn Objective-C on Mac](http://amzn.to/KXRdcw). Good Luck!

---
layout: post
title:  "Upgraded my OS to Ubuntu 14.04 LTS Trusty Tahr"
categories: Ubuntu Thinkpad
---

More than a month after the release of Ubuntu 14.04 LTS codenamed **Trusty Tahr** I decided to upgrade my OS from Ubuntu 12.04 **Precise Pangolin** to the latest. Although I can upgrade my current Ubuntu version to trusty tahr via commandline, I choose to make a fresh install.

The OS install went fine in my **Lenovo Thinkpad E130** laptop. Then I installed my usual development tools. The default system font is fine but I prefer the **Inconsolata** font - a beautiful programming font. In ubuntu 12.04 the package name to install the font is **ttf-inconsolata** but in version 14.04 the package name has changed and is now called **fonts-inconsolata**. You first need to enable the universe repository in your system and do an **apt-get update** after.

    sudo apt-get install fonts-inconsolata

Another thing is that I wanted to make the default terminal look pretty. I'm using a mac mini in the office before I finally use ubuntu as my primary OS for web development. The reason I stop using mac mini is that it has wifi connectivity issues. The internet connection via wifi in mac mini is way tooo sloooowww and I can't fix it. Anyway, In mac mini I installed a bash script that make the OSX terminal beautiful.

Have a look at [bashtrap](https://github.com/barryclark/bashstrap).

<img src="https://raw.github.com/barryclark/bashstrap/master/screenshot.png" class="img-responsive" alt="Bashtrap">

Since it's just a bash script I run it in my ubuntu terminal and it **mostly** works. I really mean mostly because when you run the **ls** command it doesn't show the directories as colored anymore. I found the fix of course. See this [line 8](https://github.com/barryclark/bashstrap/blob/master/.bash_profile#L8)? Just replace it with:

    colorflag="-G --color=auto"

Now that is just a quick hack to make it work. If you want a more elegant solution just fork the repo, add some bash script there to change the colorflag param if it sees that you're using a mac or linux.

I'm expecting also that after upgrading to 14.04 my **SmartBro USB dongle** (an internet broadband) will be automatically recognized but it's not. Can I access the printer when I'm connected to our wifi network? I believe I can't. Oh well.


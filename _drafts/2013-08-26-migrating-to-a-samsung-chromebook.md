---
layout: post
published: false
title: Migrating to a Samsung Chromebook
date: "2013-08-26 20:30"
---

Over the past month or so I've been trying to migrate to the Samsung Chromebook for most of my activities. And although it has it's limitations it is surprisingly effective for all my productive tasks. Here's just an overview on how I've managed to make it my main computer.

### Software Development

I spend a fair deal of my time doing programming so it was important for me to see if I could have a productive workflow on the Chromebook. My first port of call was to see if the cloud based development IDEs fitted my needs.

#### Online Web IDEs

- **Cloud9** - Probably one of the most well known IDEs available. The first thing becomes apparent is how beautiful the interface is. That aside though, it's the usefulness that is the most important thing. The editor has many features and is quite comparable to most code editors such as Sublime Text 2. Another feature that it boasts is an integrated terminal running in a sandboxed Linux environment. This sort of thing is very important to me because my environment requires the use of a terminal.

   Upon reviewing all of these features it would seem it has all the features I need. However in practice they weren't that sweet. There's certainly a lot of things that need to be ironed out.

   The first problem is how creating and saving files can sometimes time out or get renamed wrongly. This was quite annoying and sometimes the only solution was to reload the page.
   
   The second problem is that the terminal is too slow for my liking. There is far too much latency. I don't know if this is a common problem but it might be just because of my location in the world - the UK. Their servers are in America. Whatever the reason behind it is, it greatly hinders my productivity and making it bit useless really. Sometimes the terminal doesn't even respond and requires a page reload like the first problem I've mentioned.
   
   All in all Cloud9 looks good from the face of things but in practice doesn't quite match up. This might not be the same for everyone but that's how it's felt for me anyway. This isn't just with the Chromebook either. I experienced the same problems when on a standard modern desktop machine.
   
- **Codenvy** - This one is a bit different to Cloud9 as it's slightly less flexible but seems to be more stable. The first thing I noticed was how integrated it was to deployment to cloud PaaS providers. It also has good support for Java development which Cloud9 didn't have.

   Unfortunately Codenvy doesn't have full terminal access. Only one that gives access to virtual commands which basically act as an alias to the functions in the IDE. This was quite a big problem for me because I often need to run UNIX commands as part of my development workflow.
   
- **ShiftEdit** - This one was probably the most different compared to the previous two. Mainly because it supports working offline in Chrome and it doesn't have workspaces on their servers. Instead you connect to existing _'sites'_ via FTP, Google Drive, Dropbox, WebDAV etc. This also means that you will be modifying the files directly without any version control. So that was a no go to begin with.

   What really got be interested in this one was it's offline support. Unfortunately that wasn't a show stopper either. Initially I wasn't instantly able to work out how to make use of the offline mode support. At the time of writing, the documentation on this feature was a bit flakey. I came across a video about it but that was outdated. The interface had changed since. Even though I could eventually work out how it worked, it was still disappointing how unclear it was. Setting it up is rather tedious because you have to manually select each individual file you want to make available for offline editing. To me this seemed ridiculous. Why can't you just select a folder? Syncing the offline edits back aren't automatic either.
   
   All in all I was quite disappointed with ShiftEdit and can't say I'd recommend it. Maybe improvements will be made in the future. But I'm not sure whether I will ever see it comparable to the others.
   
- **Nitrous.IO** - This one is probably the newest out of them all. Surprisingly it seemed like it would be the one that is the nearest to my requirements. The primary feature that it boasts is how you are given a full non-root Linux terminal access pre-installed with development tools (depending on which template you choose). It also has a code editor with a file explorer. 

   The best thing about Nitrous was that the built-in terminal was so much more responsive than Cloud9. For me this would make it superior as this is such an important feature to me. If I wanted to I could ignore the code editor and just use VIM in a terminal for all my editing needs.
   
   The code editor isn't perfect but I don't expect it to be considering it's an early project still in public beta. The minor issues I have are: fixed colour palette, supported file types could be bigger, and no word wrap functionality. These don't usually impede on productivity. Except the word wrap when dealing with lots of plain text.
   
In the end I decided that Nitrous.IO will be the one that I'll be using for the majority of my coding when using the Chromebook.

#### What about offline functionality?

As you might have guessed Nitrous.IO is useless when I don't have an internet connection. But this isn't a dead end. There is a possible solution to this - [enable developer mode](http://www.chromium.org/chromium-os/developer-information-for-chrome-os-devices/samsung-arm-chromebook#TOC-Entering-Developer-Mode) and use [crouton](http://github.com/dnschneid/crouton/).
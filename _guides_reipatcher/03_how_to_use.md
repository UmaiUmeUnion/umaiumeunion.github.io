---
title: "Installation and basic commands"
---

# How to use ReiPatcher

In this chapter we take a look at how to install, configure and run ReiPatcher.

## Downloading and installing the tool

ReiPatcher is made by [usagirei](http://github.com/usagirei) and can be downloaded from one of the two official sites:

1. [ReiPatcher's GitHub webpage](http://usagirei.github.io/#!/downloads/reipatcher/main)
2. [Original thread on HongFire](http://www.hongfire.com/forum/showthread.php/444566)

Of course, remember to thank [usagirei](http://www.hongfire.com/forum/reputation.php?do=addreputation&p=3703144) for his work on this tool.

After you have downloaded the archive, extract it somewhere you can find it. The location does not matter -- as long as all files in the archive are kept together and the tool is easy to access, you're all set.

## Basic commands

As for **any** command-line tool, the best way to learn how to use it is to run it.

However, in order to see the output we need to run it from the command prompt. To do that, you need to:

1. Go to the folder you extracted ReiPatcher to. You should clearly see that `ReiPatcher.exe` is in the folder.
2. Hold Shift and right-click on the empty space in the folder. The context menu should now pop up.
3. Select `Open command prompt` from the context menu. The command prompt should open up.
4. Type in `ReiPatcher`

As a result, you will most likely get the following help screen:

{% highlight console %}
ReiPatcher 0.9.0.8
.NET Assembly Patcher - Powered by Mono.Cecil
Copyright © Usagirei 2015
GIT master [aac43ce754149cbde635f4ef6d5b164aa796fba1]
==============================================================                                 
Switch Usage:
-fc <Config>            Force creation of new configuration file
-c <Config>             Use or create of configuration file 
-w              	Waits for user input after patching
-r              	Reloads latest Assembly backup 

Exit Codes:
 0                      Success
 1                      NoPatchesApplied
-3                      InternalException
-2                      FileNotFound
-1                      DirectoryNotFound
{% endhighlight %}

If this is what you see, congratulations! You have successfully installed and run ReiPatcher for the first time!

## Next

Next, you can either download game configurations from [ReiPatcher's original thread](http://www.hongfire.com/forum/showthread.php/444566-ReiPatcher-General-Purpose-Net-Assembly-Patcher),
put it into ReiPatcher's directory and continue onto Chapter 5, or create the game configuration yourself in the next chapter.
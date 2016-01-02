---
title: "Installing patchers"
---

# Installing patchers

After all game configurations and assemblies are in place, it is time to finally use ReiPatcher. If you don't have the patcher configuration, consult the [previous chapter](04_game_configurations.html) on how to set up ReiPatcher.

Below are the basic steps on how to apply a patch to a game using ReiPatcher.

#### 1. Download patchers

Most of the patchers come with separate instructions on where to place them. In that case simply follow the guide provided with the patcher.
In the case there are no instructions, you can follow the following generic guide:

Download a patcher of your choice and place it in the folder specified in the `PatchesDir` key found in the patcher configuration INI file. The patcher can usually be identified by the `Patcher.dll` (or `Patch.dll`) at the end of its name.

#### 2. Run ReiPatcher

After the patcher has been placed in its appropriate folder, do the following:

1. Go the the folder where `ReiPatcher.exe` is located.
2. Hold Shift and right-click on the empty space in the folder. The context menu should come up.
3. Select `Open command prompt` from the context menu. The command prompt should open.
4. Type in `ReiPatcher -c <NAME>.ini` where `<NAME>` is the name of the patcher configuration to use.

Observe the output. If everything went well, you will get the following output (*Lines prefixed with `#` are comments and are different depending on the installed patchers*):

{% highlight console %}
ReiPatcher 0.9.0.8
.NET Assembly Patcher - Powered by Mono.Cecil
Copyright © Usagirei 2015
GIT master [aac43ce754149cbde635f4ef6d5b164aa796fba1]
==============================================================================
Loading configuration file: '<NAME>.ini'
============================== Loading Patchers ==============================
Loading Patchers
## Here you will see a list of patchers that are being loaded
================================= Pre-Patch ==================================
## Here you will see the same list again
============================= Loading Assemblies =============================
## Here will you see a list of assemblies that will be patched
================================== Patching ==================================
## This is an important section. If there are any problems, ReiPatcher will stop here and display an error.
================================= Post-Patch =================================
## If everything went fine, you will see the list of patchers again
================================== Finished ==================================
{% endhighlight %}

And you're done! Note again, that `<NAME>` is the name of the patcher configuration. If you don't have one, you can always check the [previous chapter](04_game_configurations.html) that explains how to get one or how to create such a file.

ReiPatcher creates a backup of each assembly it patches and saves it in the same folder as the patched assembly. If you want to patch the last backed-up version of assembly, use the `-fc` switch instead of `-c` when using ReiPatcher.

### Creating a batch file

Instead of using the command prompt every time, you can create a batch file that runs ReiPatcher automatically.

To do that, open a text file editor of your preference (Notepad++ is a nice choice), and write

{% highlight bat %}
@echo off
ReiPatcher.exe -c <NAME>.ini
pause
{% endhighlight %}

where `<NAME>` is the name of the patcher configuration. Name the file `ReiPatcher_Patch_<NAME>.bat` and save it to the folder where `ReiPatcher.exe` resides. Now you can simply double-click the batch file, which will run ReiPatcher with `<NAME>.ini` as the patcher configuration.


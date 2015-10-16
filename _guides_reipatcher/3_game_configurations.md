---
title: "Creating game configurations"
previous: 2_how_to_use
---

# Creating the game configuration

One of the most important aspects of ReiPatcher is that it's general-purpose: ReiPatcher can be configured to patch any Unity Engine game.
In order to do that, however, we need to give some information to ReiPatcher about our game. That is done with game configurations. Without them ReiPatcher will not work.

As of right now, some of pre-made configurations can be found on [ReiPatcher's original thread](http://www.hongfire.com/forum/showthread.php/444566-ReiPatcher-General-Purpose-Net-Assembly-Patcher). 
Still, if the game you wish to patch does not have the configurations available, you must do them yourself. 
In this section we shall take a look at ReiPatcher's game configuration files and how to work with them.

If you, however, have the configuration ready, you can simply put it in ReiPatcher's directory and omit this section.

<div class="alert alert-info" role="alert">
<span class="glyphicon glyphicon-exclamation-sign" aria-hidden="true"></span>
<span class="sr-only">Note:</span>
<span class="alert-text">Technically, the configuration file may reside anywhere on your computer. Still, putting the configuration wherever is ill-advised, as later one must have to tell ReiPatcher where the file is.</span>
</div>

To create a template, do the following:

1. Go to the folder you extracted ReiPatcher to. You should clearly see that `ReiPatcher.exe` is in the folder.
2. Hold Shift and right-click on the empty space in the folder. The context menu should now pop up.
3. Select `Open command prompt` from the context menu. The command prompt should open up.
4. Type in `ReiPatcher -fc <name>` where `<name>` is the name of your choice. It is advised to name the configuration files by the game's acronym or some other ID.

If everything went correctly, you will see a file named `<name>.ini` in ReiPatcher's directory. Go ahead and open it with the text file editor of your choice.
You will see something similar to this:

<div>
{% highlight ini linenos %}
;@varname=$(REGISTRY_PATH)
[ReiPatcher]
PatchesDir=Patches
AssembliesDir=

[Assemblies]

[Launch]
Executable=
Arguments=
Directory=
{% endhighlight %}
</div>

Just like almost any plug-in made for UnityInjector, ReiPatcher uses [ExIni](https://github.com/usagirei/ExIni) to load, edit and save configurations.
The syntax is quite simple and consists mainly of four parts:

* Lines starting with `;` are called comments. By default, they are ignored by any INI parsers.
* Lines of the form `<Variable>=<Value>` are properties that are used by ReiPatcher or other patchers. In ReiPatcher they are primarily used to store paths to different folders.
* Lines of the form `[Name]` are called groups. They -- obviously -- group multiple variables together. 
All variables between two group names (or between a group name and the end of the file) belong to one group. The name of the grouped variables is defined in the upper group name declaration.
For example. variables `PatchesDir` and `AssembliesDir` belong to a group called `ReiPatcher`.
* **Syntatic sugar provided by ReiPatcher:** if a commented line is of the form `@variable=value`, ExIni creates an environment variable that is accessible by properties. In the main file, the variable is accessed by surrounding the name with `%`, as in `%variable%`.
Moreover, if the value of the variable is of the form `$(PATH)` where `PATH` is the path to the registy key, ExIni will get the value of the said key and assign it to the environment variable.

<div class="alert alert-info" role="alert">
<span class="glyphicon glyphicon-exclamation-sign" aria-hidden="true"></span>
<span class="sr-only">Note:</span>
<span class="alert-text">Not only does ExIni allow to create own custom environment variables, but also makes it possible to access the variables already set by Windows.</span>
</div>

Almost all games create a registry key containing the path to the game's directory, so the ability to load the keys into variables is really useful, since while the game's directory may change,
they path to the registry key will not. That way it is possible to create game configurations that work on all computers.


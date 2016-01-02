---
title: "Creating game configurations"
---

# Creating the game configuration

One of the most important aspects of ReiPatcher is that it's general-purpose: ReiPatcher can be configured to patch any Unity Engine game (or even any .NET application).
In order to do that, however, we need to give some information to ReiPatcher about the game. That is done with game configurations. Without them ReiPatcher will not work.

As of right now, some of pre-made configurations can be found on [ReiPatcher's original thread](http://www.hongfire.com/forum/showthread.php/444566-ReiPatcher-General-Purpose-Net-Assembly-Patcher). 
Still, if the game you wish to patch does not have the configurations available, you must create them yourself. 
In this section we shall take a look at ReiPatcher's game configuration files and how to work with them.

If you, however, have the configuration ready, you can simply put it in ReiPatcher's directory and omit this section.

<div class="alert alert-info" role="alert">
<span class="glyphicon glyphicon-exclamation-sign" aria-hidden="true"></span>
<span class="sr-only">Note:</span>
<span class="alert-text">Technically, the configuration file may reside anywhere on your computer. Still, putting the configuration wherever is ill-advised, as later one must have to specify the configuration file's location to ReiPatcher</span>
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
* Lines of the form `[Name]` are called sections. They group multiple variables together. 
All variables between two section names (or between a section name and the end of the file) belong to the upper section. For example, variables `PatchesDir` and `AssembliesDir` belong to a section called `ReiPatcher`.
* **Syntactic sugar provided by ReiPatcher:** if a commented line is of the form `@variable=value`, ExIni creates an environment variable that is accessible by properties. In the main file, the variable is accessed by surrounding the name with `%`, as in `%variable%`.
Moreover, if the value of the variable is of the form `$(PATH)` where `PATH` is the path to the Windows registry key, ExIni will get the value of the said key and assign it to the environment variable.

<div class="alert alert-info" role="alert">
<span class="glyphicon glyphicon-exclamation-sign" aria-hidden="true"></span>
<span class="sr-only">Note:</span>
<span class="alert-text">Not only does ExIni allow to create own custom environment variables, but also makes it possible to access the variables already set by Windows.</span>
</div>

Below is the table which shows properties ReiPatcher recognizes and what they mean.

| Property | Description |
| -------- | ----------- |
| `PatchesDir`| Path to the folder which contains the patchers. If just a name specified, the folder is assumed to be in the same folder as ReiPatcher.exe |
| `AssembliesDir` | Path to the folder which contains game's assemblies that should be patched (*target assemblies*). |
| `Executable` | Path to the executable that is run after ReiPatcher finishes patching. |
| `Arguments` | Arguments that are passed to the executable specified in `Executable` |
| `Directory` | Working directory that is passed to the executable specified in `Executable` | 

Almost all games create a registry key containing the path to the game's directory, so the ability to load the keys into variables is really useful, since while the game's directory may change, their path to the registry key will not. That way it is possible to create game configurations that work on all computers.


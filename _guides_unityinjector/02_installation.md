---
title: "Installation and configuration"
---

# Installation and configuration

In this chapter we will look at how to install and configure UnityInjector.

<div class="alert alert-info" role="alert">
<span class="glyphicon glyphicon-exclamation-sign" aria-hidden="true"></span>
<span class="sr-only">Note:</span>
<span class="alert-text">UnityInjector is installed using ReiPatcher (or Sybaris). Make sure you have it [downloaded]({{ site.baseurl }}{{ site.guides_reipatcher[2].url }}) and [configured]({{ site.baseurl }}{{ site.guides_reipatcher[3].url }}) before proceeding any further.</span>
</div>

## Copying files

After you have [downloaded](01_preface.html#download-the-tool) UnityInjector, unpack the archive. In it there should be **at least** the following files: `ExIni.dll`, `UnityInjector.dll` and `UnityInjector.Patcher.dll`. Depending on the game and your particular configuration of ReiPatcher for the game, do the following:

1. Place `ExIni.dll` and `UnityInjector.dll` into `<Game Root>\<Game Code>_Data\Managed` folder, where `<Game Root>` is the folder where the game's executable resides and `<Game Code>` is the name of the game's executable.
2. Place `UnityInjector.Patcher.dll` into `<ReiPatcher Root>\<Patchers>` folder, where `<ReiPatcher Root>` is the folder where `ReiPatcher.exe` resides, and `<Patchers>` is the folder that contains the patchers for the target game. Usually the `<Patchers>` folder is just called `Patches`, but you can check the exact folder name in ReiPatcher's game configuration INI file by looking at the `PatchesDir` key. If you don't have a game configuration, you can get or create one as described [in the ReiPatcher guide]({{ site.baseurl }}{{ site.guides_reipatcher[3].url }}).
3. In the `<Game Root>` folder, create a folder called `UnityInjector`

## Configuring UnityInjector
UnityInjector relies on ReiPatcher and uses the game configuration file to patch an appropriate method.

<div class="alert alert-info" role="alert">
<span class="glyphicon glyphicon-exclamation-sign" aria-hidden="true"></span>
<span class="sr-only">Note:</span>
<span class="alert-text">If you have [downloaded pre-made game configurations](http://www.hongfire.com/forum/showthread.php/444566-ReiPatcher-General-Purpose-Net-Assembly-Patcher), the configuration file will most likely have the needed properties specified. In that case you can read [how to install plugins](#installing-a-plugin).</span>
</div>

In case there are no pre-made configurations, or UnityInjector's patcher fails to inject methods, you will have to edit the game configuration yourself. To do that, do the following:

1. Head to the folder where ReiPatcher is installed.
2. Locate the INI configuration file for the game. If there is no such, [download or create one yourself]({{ site.baseurl }}{{ site.guides_reipatcher[3].url }}).
3. Open the configuration file in your favourite text editor.
4. At the end of the file, add the following:
{% highlight ini linenos %}
[UnityInjector]
Assembly=
Class=
Method=
{% endhighlight %}

UnityInjector's patcher will use these properties to inject some initialisation code into some assembly of the game. Here is a table that describes each property in more detail.

| Property | Description |
| -------- | ----------- |
| `Assembly` | The name of the assembly to inject initialisation code into. The assembly is written without the file extension, i.e. `MyAssembly` instead of `MyAssembly.dll`. |
| `Class` | The class in the specified `Assembly` to inject initialisation code into. The name must contain the namespace of the class (unless the class does not belong to any namespaces), i.e. `MyNamespace.SomeClass`. |
| `Method` | The method in the specified `Class` to inject initialisation code into.

To fill in the values, you will have to use a .NET decompiler (such as [ILSpy](http://www.ilspy.com/), [Cecil Studio](http://code.google.com/p/cecilstudio), [dotPeek](https://www.jetbrains.com/decompiler/) and [.NET Reflector](http://www.red-gate.com/products/dotnet-development/reflector/)) to search through game's assemblies and locate a suitable class and method to inject UnityInjector initialisation code into.

Here are some tips on finding a proper method to inject:

* You can use any game assembly located in `<Game Root>\<Game Code>_Data\Managed` folder, where `<Game Root>` is the folder where the game's executable resides and `<Game Code>` is the name of the game's executable. Moreover, you can use any class and any method found in the assembly, *as long as the method is run once when the game loads*.
* Most commonly used assemblies are `Assembly-CSharp` and `Assembly-UnityScript`.
* Most commonly used classes inherit `MonoBehaviour` and contain such words as "start" and "scene".
* If the chosen class inherits `MonoBehaviour`, you can use `Awake` or `Start` as the value of `Method` (as long a method with such name exists).
* You can inject the code into constructors by using `.ctor` as the value of `Method`, but it is not preferable.
* Make sure the chosen method is called only one time. In addition, attempt to chose a method that is called right after the game loads.
* If you are unsure of which method to choose, do it in a trial-and-error fashion: specify some values for the above-mentioned properties, run ReiPatcher and load the game to make sure the patch was successful. If everything went to plan, you will see a console window appear when the game loads. If nothing happens, simply restore the patched assembly and choose a different method.

## Patching the assembly
Once you have the game configuration edited and saved, just run ReiPatcher (as described in [ReiPatcher's guide]({{ site.baseurl }}{{ site.guides_reipatcher[4].url }})). 

If no errors occurred, run the game executable. If you see a console window appear alongside the game, that means you have successfully installed UnityInjector! You can now install and run plugins.

## Installing a plugin

A plugin is a separate DLL file that is put to the `UnityInjector` folder located in the game's root folder. To install a plugin, do the following:

1. Download the plugin.
2. Place the plugin DLL (usually the one ending with `Plugin.dll`) into `<Game Root>\UnityInjector` folder, where `<Game Root>` is the folder where the game's executable resides.
3. Place any additional files in the folder specified in the plugin's installation instructions.
4. Run the game. If the plugin was installed successfully, you will see a "Plugin loaded" message in the debugger console.



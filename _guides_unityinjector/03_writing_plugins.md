---
title: "Writing plugins"
---

<div class="alert alert-warning" role="alert">
<span class="glyphicon glyphicon-alert" aria-hidden="true"></span>
<span class="sr-only">Warning:</span>
<span class="alert-text">
**HERE BE DRAGONS**<br/><br/>
The end-user guide ends here.<br/>
From now on, only the technical aspects of UnityInjector are discussed.
</span>
</div>

# Writing plugins

Writing plugins for UnityInjector is as easy as creating a custom `MonoBehaviour`. That being said, the actual plugin writing and Unity Scripting API are out of the scope of this tutorial. For more information on how to use `MonoBehaviour`s, refer to [its official documentation](http://docs.unity3d.com/ScriptReference/MonoBehaviour.html).

## Requirements

To create a plugin for UnityInjector, the following tools are needed.

### Required

* [UnityInjector](http://www.hongfire.com/forum/showthread.php/444567-UnityInjector-Plugin-Powered-Unity-Code-Injector).
* .NET Framework 3.5 (or the same version as the game uses).
* Basic knowledge of C# (or other language supporting .NET) and the Unity Engine Scripting API.

### Optional

* Game's assemblies.
* An IDE to program with. Some of the more popular ones are [Visual Studio](https://www.visualstudio.com/), [MonoDevelop](http://www.monodevelop.com/) and [SharpDevelop](http://www.icsharpcode.net/OpenSource/SD/).
* [ReSharper](https://www.jetbrains.com/resharper/) -- a productivity tool for Visual Studio. Makes working with C# much faster.
* Additional libraries to make plugin creation easier.

<div class="alert alert-info" role="alert">
<span class="glyphicon glyphicon-exclamation-sign" aria-hidden="true"></span>
<span class="sr-only">Note:</span>
<span class="alert-text">This tutorial assumes you know how to use all the tools except UnityInjector itself. If you are unfamiliar with some tools you have chosen to use with the plugin, refer to the tools' documentations before proceeding.</span>
</div>

## Creating the plugin module

<div class="alert alert-info" role="alert">
<span class="glyphicon glyphicon-exclamation-sign" aria-hidden="true"></span>
<span class="sr-only">Note:</span>
<span class="alert-text">This tutorial uses naming conventions defined in the [PHP/PCP mod architecture]({{ site.baseurl }} {{ site.guides_reipatcher[7].url }}#naming-conventions).</span>
</div>

To create a basic plugin project, do the following:

#### 1. Create a project in the IDE of your choice.
If you don't use any IDEs, just create a folder for your plugin project. 

Name the plugin `<GAME>.<Plugin Name>.Plugin`, where `<GAME>` is the game code or abbreviation (e.g. CM3D2 or SBPR), and `<Plugin Name>` is the name of the plugin.

Next, configure your project as follows:

1. Set .NET Framework target to the same version as the one the game uses (usually 3.5 for Unity Engine games).
2. Add the following references to the project: 
    * `UnityInjector.dll` (found in UnityInjector itself).
    * `ExIni.dll` (comes bundled with ReiPatcher and UnityInjector).
    * `UnityEngine.dll` (found in `<Game Root>\<Game Code>_Data\Managed` folder)
    * (Optional) `Assembly-CSharp.dll`/`Assembly-UnityScript.dll` and other game assemblies found in `<Game Root>\<Game Code>_Data\Managed`
      Here `<Game Root>` is the folder where the game's executable resides and `<Game Code>` is the name of the game's executable.

<div class="alert alert-info" role="alert">
<span class="glyphicon glyphicon-exclamation-sign" aria-hidden="true"></span>
<span class="sr-only">Note:</span>
<span class="alert-text">Depending on what your plugin does you may want to include different game assemblies. However most of the in-game logic can be found in assemblies named `Assembly-Foo`, where `Foo` is some word (e.g. `CSharp` and `UnityScript`).</span>
</div>

Finally, don't forget to set the output of your project to DLL. Name the DLL the same way as you named the plugin.

#### 2. Create a custom `MonoBehaviour`
In the project, create a plugin class that inherits `PluginBase` (defined in `UnityInjector.dll`). Since `PluginBase` inherits `MonoBehaviour`, the plugin class automatically becomes a `MonoBehaviour` itself.

As an example, here is a template for a very basic plugin class:

{% highlight csharp linenos %}
using UnityEngine;
using UnityInjector;
using UnityInjector.Attributes;

namespace GAME.MyPlugin.Plugin
{
    [PluginName("My Plugin"), PluginVersion("1.0")]
    public class FooPlugin : PluginBase
    {
        // Used by the Unity Engine Scripting API
        public void Awake()
        {
            // Called once the MonoBehaviour is added into the game
        }

        // Used by the Unity Engine Scripting API
        public void Update()
        {
        	// Called every tick
        }

        // Add more MonoBehaviour methods or additional plugin logic
    }
}
{% endhighlight %}

Here are some important notes regarding the code (so as to re-emphasize the previous points):

* A plugin module may contain multiple classes, but only the non-abstract ones inheriting `PluginBase` are automatically loaded by UnityInjector.
* The plugin class is essentially a `MonoBehaviour` with some additional properties, like `Preferences` property (discussed in the next chapter) and plugin attributes.
* The plugin's name and version is specified by adding an appropriate attribute to the plugin class. UnityInjector provides the following attributes:

| Attribute | Description |
| --------- | ----------- |
| `PluginName(string name)` | Specifies the name of the plugin. If not specified, will be the same as that of the plugin class. |
| `PluginVersion(string version)` | Specifies the version of the plugin. If not specified, will be the same as that of the plugin assembly. |
| `PluginFilter(string exeName)` | Specifies that the plugin class should be loaded only if the name of the game's executable is the same as `exeName`. If not specified, UnityInjector will always load the plugin. |

* You can create and load INI configurations using ExIni. More will be explained in the next chapter.
* You can use `System.Console` class to output messages to UnityInjector's debug console. However, **be careful when attempting to output coloured messages**! Some games come bundled with the version Mono which does not have colour-related properties and methods defined. If your plugin is made to be working on multiple games, refer to the subsequent section on how to use the "colour-safe" console provided by UnityInjector.

#### 3. Compile and test the plugin module
After you have written the console logic out, you can compile it into a DLL.

Test the compiled plugin by placing it into `<Game Root>\UnityInjector` folder (here `<Game Root>` is the folder where the game's executable resides) and running the game.

## About coloured messages and `SafeConsole`
Since Unity Engine games use Mono instead of the default .NET Framework, some common methods might be inaccessible when the plugin is in the game. Such methods are `System.Console.ForegroundColor` and `System.Console.BackgroundColor`, for instance.

It has been noted that some distributions of mscorlib.dll don't have such methods defined, making the game to throw an exception every time those properties are used. Most of the time this does not concern the developer in any way, but sometimes when creating a game-independent plugin it might be desirable to make sure the game supports console colours (for logging purposes, for instance).

That is why UnitInjector provides `SafeConsole.ForegroundColor` and `SafeConsole.BackgroundColor` properties. They have the same effect as the original properties defined in `System.Console`, but using these properties will not cause an exception if the game does not support coloured console output.

**A rule of thumb is therefore**: if your plugin is supposed to be game-independent, use `SafeConsole.ForegroundColor` and `SafeConsole.BackgroundColor` instead of `System.Console.ForegroundColor` and `System.Console.BackgroundColor`.

## Next
In the next chapters we will discuss how to use ExIni to create and read plugin configurations easily.
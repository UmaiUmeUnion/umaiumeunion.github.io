---
title: "Guidelines: PHP/PCP architecture"
---

# Guidelines: PHP/PCP mod architecture

In the previous chapters we discussed the fundamental API that ReiPatcher exposes, yet we blatantly left actual planning and designing phase out of the scope.
Since ReiPatcher acts as an executable version of Mono.Cecil, patchers and mods can be build in a large variety of ways. However as of this writing, the most common way to design and build mods utilising ReiPatcher is what we shall refer to as the *PHP/PCP (Patcher-Hook-Plugin/Patcher-Core-Plugin) architecture*.

In this chapter we provide a basic idea of how mods **can be** designed and introduce the idea behind the PHP/PCP architecture.

## Architecture overview

In the PHP/PCP architecture one uses either one or a combination of such tools as ReiPatcher, UnityInjector and external libraries to create a working modification for a game powered by Unity Engine (or a .NET application). In PHP/PCP, all code is divided into the three separate modules: **Patcher**, **Hook** (or **Core**) and **Plugin**. In practice, these modules may be separate classes, namespaces or -- more commonly -- assemblies (DLL or EXE). Each module uses a specific library or tool to accomplish a certain task to make a mod functional. Moreover, each module may be used either once or every time the game is run.

The PHP/PCP architecture is rather flexible: a working mod needs not consist of all the aforementioned modules, and multiple modules of the same type may be used in the same mod concurrently. For example, an in-game camera mod usually needs only a plugin module, while a mod that edits in-game parameters on the fly might (but does not have to) require the usage of all the module types. In either case, both mods can be considered as a specialised version of the PHP/PCP architecture.

Below is an overview of all the modules.

#### Patcher
As discussed earlier, the patcher's job is to modify the content of a game assembly to change the behaviour thereof. The patcher is made using ReiPatcher's API and is always separated into its own DLL.

The actual function of the patcher depends on the nature of the mod, but one can mention that usually the purpose of the patcher is to link the hook (or core) module with some of the game's assemblies.

As mentioned, the patcher is not a necessity to create a mod.

#### Hook/Core
The main methods of the mod are located in the hook/core module. The naming convention is unclear, but a basic rule of thumb is that

* if the module only contains event handlers and methods that invoke those handles, the module is called a *hook module*.
* if the module contains methods that execute some other logic that is considered "core" of the mod, the module is called a *core module*.

Given the definition, it is often up to the developer as to how to name this module -- all in all, the module is always used the same way its name notwithstanding.

The module is often located in the same folder as the game's assemblies and is directly linked to some game assembly through, for instance, a method call. The linking is done using the patcher module.

As with the patcher module, the hook/core module is not necessary to create a functioning mod. If the mod does not need to modify the game's behaviour directly or if such modification can be done using only either a patcher or a plugin module, the hook/core module can be left out.

#### Plugin
The plugin is a module that uses UnityInjector to sideload a custom `MonoBehaviour` and use the Unity Scripting API to modify the game's behaviour. In addition, the plugin may use reflection to accommodate basic assembly modification -- some of the more extensive and advanced changes may still require to use a patcher module.

Since the plugin is basically a custom `MonoBehaviour`, you can use the module to create in-game GUIs and modify other `MonoBehaviour`s using the Unity Scripting API.

The plugin module is placed into a separate UnityInjector folder and is loaded every time the game is run.

For more information on how to build the plugin module, refer to the [UnityInjector guide]({{ site.baseurl }}{{ site.guides_unityinjector[0].url }}).

## Naming conventions
Since ReiPatcher nor UnityInjector provides exact guidelines on how to name the modules, there are no exact naming conventions. However it seems that most mod developers follow the naming pattern defined in this section.

Every module of a mod is usually named as follows:

{% highlight bat %}
GAME.Name.Type
{% endhighlight %}

where `GAME` is the code or an abbreviation of the game (e.g. CM3D2 or SBPR); `Name` is the name of the mod; and `Type` is the module type. Common values of `Type` for each module are

* `Patcher` or `Patch` for the *Patcher module*,
* `Hook` for the *Hook module*,
* `Core` or none for the *Core module*, and
* `Plugin` for the *Plugin module*.

<div class="alert alert-info" role="alert">
<span class="glyphicon glyphicon-exclamation-sign" aria-hidden="true"></span>
<span class="sr-only">Note:</span>
<span class="alert-text">Some developers use `Managed` as a `Type` name for hook and core modules. While not being as descriptive, the name can be used if the developer doesn't want to differentiate between the hook and the core module.</span>
</div>

<div class="alert alert-info" role="alert">
<span class="glyphicon glyphicon-exclamation-sign" aria-hidden="true"></span>
<span class="sr-only">Note:</span>
<span class="alert-text">If you are using Sybaris instead of ReiPatcher for the patcher module, always use `Patcher` as its `Type` name. Otherwise Sybaris **will not load** the module!</span>
</div>

## General architecture guidelines

Below are some important tips when creating a mod using the PHP/PCP architecture.

* **Avoid overcomplicating projects.** Attempt to use as few modules as possible -- that way not only will it be easier to install, but also easier to maintain.
* **Prefer using UnityInjector to ReiPatcher.** The patcher and the hook/core modules heavily rely on the implementation of the game's assembly. If the implementation of some method is changed during the update, the mod may become unusable. Thus when planning out a mod, think whether the desired outcome can be achieved by using the plugin module only.
* **Follow the naming convention.** Naming the mods the same way the others developers do helps users to deduce how to install the mod even if there weren't a separate guide.
---
title: "ReiPatcher API: Setup"
---

<div class="alert alert-warning" role="alert">
<span class="glyphicon glyphicon-alert" aria-hidden="true"></span>
<span class="sr-only">Warning:</span>
<span class="alert-text">
**HERE BE DRAGONS**<br/><br/>
The end-user guide ends here.<br/>
From now on, only the technical aspects of ReiPatcher are discussed.
</span>
</div>

# ReiPatcher API: Setup

In this and subsequent chapters we will discuss how to use ReiPatcher's API to create patchers.

This chapter mainly lists the requirements and suggestions on tools to use.

## Tools and requirements

The following tools and libraries are required to create patchers:

* Target assemblies (the ones that are to be patched).
* [ReiPatcher](http://www.hongfire.com/forum/showthread.php/444101-%28KISS%29-Custom-Maid-3D-2?p=3703144#post3703144).
* .NET Framework 3.5 or later.
* Mono.Cecil (included in ReiPatcher).
* Basic knowledge of C# (or other language supporting .NET).

These tools and libraries are optional but will make patching much easier:

* Knowledge of CIL (Common Intermediate Language).
* IDE to program with. Some of the more popular ones are [Visual Studio](https://www.visualstudio.com/), [MonoDevelop](http://www.monodevelop.com/) and [SharpDevelop](http://www.icsharpcode.net/OpenSource/SD/).
* [ReSharper](https://www.jetbrains.com/resharper/) -- a productivity tool for Visual Studio. Makes working with C# much faster.
* A .NET decompiler to view target assemblies' code. Such tools are for instance [ILSpy](http://www.ilspy.com/), [Cecil Studio](http://code.google.com/p/cecilstudio), [dotPeek](https://www.jetbrains.com/decompiler/) and [.NET Reflector](http://www.red-gate.com/products/dotnet-development/reflector/).
* Additional libraries to make patching easier.

In this tutorial we will use Visual Studio and C# 6.0 to create patchers.

<div class="alert alert-info" role="alert">
<span class="glyphicon glyphicon-exclamation-sign" aria-hidden="true"></span>
<span class="sr-only">Note:</span>
<span class="alert-text">This tutorial only covers the usage of ReiPatcher. Make sure you are familiar with any other additional tools (like IDEs) before proceeding.</span>
</div>

## List of additional patcher tools

Below is a (not finished) list of pre-made libraries and templates to make patching easier.

| Name | Links | Description |
| ---- | ----- | ----------- |
| PatcherHelper.cs | [GitHub](https://github.com/neguse11/cm3d2_plugins_okiba/blob/master/Lib/PatcherHelper.cs) | A compilation of common patching methods for Mono.Cecil. Created by [neguse11](https://github.com/neguse11) and is used in majority of his mods for CM3D2. |
| Cecil.Inject | [GitHub](https://github.com/denikson/Mono.Cecil.Inject), [Documentation](http://denikson.github.io/Mono.Cecil.Inject/) | A separate library to extend the functionality of Mono.Cecil and provide patching capabilities. Used mainly as a separate DLL. <br/> Can be thought of as PatcherHelper.cs, but with more methods and proper documentation.  |
| Mono.Cecil.* Extensions | Found within Mono.Cecil | Additional minor helpers to work with IL within Mono.Cecil. For instance, Mono.Cecil.Rocks provides some methods to optimize IL code and so forth. |

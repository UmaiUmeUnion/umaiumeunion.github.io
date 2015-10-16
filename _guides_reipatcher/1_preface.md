---
title: "Preface"
next: 2_how_to_use
---

<div class="alert alert-warning" role="alert">
<span class="glyphicon glyphicon-alert" aria-hidden="true"></span>
<span class="sr-only">Warning:</span>
<span class="alert-text">
**SOME PARTS OF THIS TUTORIAL ARE STILL NOT READY**<br/><br/>
The following tutorial is not yet complete and some parts are subjected to change.<br/>
Remember to check for the latest news on the tutorials!
</span>
</div>

# Preface

`Mono.Cecil` is one of the key tools when it comes to patching .NET assemblies. However, to build a standalone patcher means to write the basic initialisation code that is usually found in any patcher -- the so called "boilerplate code".

Writing it is usually no big deal, but if one developers maintains a multitude of standalone patchers, that code becomes a nuisance. Moreover, applying said patches one-by-one is quite a derring-do.
So not only is it hard for the developer, but so it is for the userbase. Thus one could ask: isn't there a general-purpose patching tool that would make both users and developers happy?

And there is.

### ReiPatcher

ReiPatcher is a generic patching utility for both the developers and the users to make patching easier. 
Namelly, it has two key functions

* **For developers** ReiPatcher gives a simple and robust API to expose the contents of .NET assemblies using `Mono.Cecil`. 
The tool does all the routine work, like locating and loading assemblies into `Mono.Cecil` -- no more, no less. Thus the developer needs only focus on writing the patch itself.
* **For users** ReiPatcher provides a command-line interface to manage and apply said patches. ReiPatcher can be configured for any Unity Engine game and other programs utilising .NET.

Everything considered, it is a win-win situation, is it not?

### About this tutorial

In this tutorial we shall take a thorough look at ReiPatcher. To be specific, we shall discuss the following topics:

* How to install and configure the tool
* How to inspect existing assemblies and plan the patch accordingly
* ReiPatcher's API and general guidelines
* Basics of `Mono.Cecil` and basic IL
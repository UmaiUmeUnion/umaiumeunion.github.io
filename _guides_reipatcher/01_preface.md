---
title: "Preface"
---

<div class="alert alert-warning" role="alert">
<span class="glyphicon glyphicon-alert" aria-hidden="true"></span>
<span class="sr-only">Warning:</span>
<span class="alert-text">
**THIS IS THE FIRST RELEASE**<br/><br/>
This is the first version of the tutorial.<br/>
Please report any typos and other mistakes alike.
</span>
</div>

# Preface

`Mono.Cecil` is one of the key tools when it comes to patching .NET assemblies. However, to build a standalone patcher means to write the basic initialisation code that is usually found in any patcher -- the so called "boilerplate code".

Writing it is usually no big deal, but if one developers maintains a multitude of standalone patchers, that code becomes a nuisance. Moreover, applying said patchers one-by-one is quite a derring-do.
So not only is it hard for the developer, but so it is for the userbase. Thus one could ask: isn't there a general-purpose patching tool that would make both users and developers happy?

## ReiPatcher

ReiPatcher is a generic patching utility for both the developers and the users to make patching easier. 
Namely, it has two key functions

* **For developers** ReiPatcher gives a simple and a robust API to modify the contents of .NET assemblies using `Mono.Cecil`. 
The tool does all the routine work, like locating and loading assemblies into `Mono.Cecil` -- no more, no less. Thus the developer needs only focus on writing the patch itself.
* **For users** ReiPatcher provides a command-line interface to manage and apply said patchers. ReiPatcher can be configured for any Unity Engine game and other programs utilising .NET.

## About this tutorial

In this tutorial we shall take a look at ReiPatcher. To be specific, we shall discuss the following topics:

* How to install and configure the tool
* ReiPatcher's API and general guidelines
* How make pathers using the patcher-hook-plugin mod layout
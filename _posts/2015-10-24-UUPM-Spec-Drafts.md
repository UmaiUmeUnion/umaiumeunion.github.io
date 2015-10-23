---
layout: post
title: "Drafts for UmaiUme Package Manager"
date: 2015-10-24
author: denikson
---

So far UmaiUme has been quite vaguely described as the toolkit to "unify modding tools and experience for all Unity games". Initially an idea to combine ReiPatcher with UnityInjector was proposed to achieve the goal, but after some dispute no consensus was achieved.
After some planning and thinking, I deduced that UmaiUme needs its own separate tool as the core -- something that must be easy to extend, use and maintain.

In this post I shall present the main points and drafts for the core and some notes to keep in mind when the development of the core tools begin.

Other members may edit this post to add more information or their views.
**Note:** If you want to edit the post, it would be nice if you marked the place you've edited along with a short reason.

<!--more-->

### The modular approach

As **Dr Yoshi** originally proposed, we should consider two ways of building UmaiUme:

*"1. Design a full-featured core to expand on by merging ReiPatcher + ReiPatcherPlus + UnityInjector.
2. Keep a modular approach, with separate code base and projects for the IL injector, its extensions and the plugin injector."*

Having discussed the points of both briefly, we never managed to come a consensus -- after all, both options have their pros and cons.
However, after some thinking, I decided that the second option is the best suited one for UmaiUme.

To elaborate, here are the main benefits of making UmaiUme as modular as possible:

* **Encourages more abstract and generic tools** -- UmaiUme, as its name stands, is an *unified application interface*. By creating modular tools we at the same time make generic (and provide for a more unified) API that is easier to adapt to any other modding tools.
* **Simplifies development, clean code hierarchy** -- With a modular approach we can forget about the existence of ReiPatcher, UnityInjector or any other tool. The core will provide functionality of its own, and any code specific to the execution of a single tool or file type can be moved to external assemblies. That way the code is divided into the core and the extensions.
* **More developer-friendly** -- By exposing a rich API not only can we develop tool-specific extensions, but also any other developer can provide their own one.

To make UmaiUme as modular as possible, we need a modular core.
Below is the draft of the specification for exactly that core: the package manager.

### The core: UUPM

**UUPM** (UmaiUme Package Manager) is (currently) the name for the package manager that will most likely be the core of UmaiUme.

#### **Basic description**

UUPM is a tool that functions like any other package management utility (ie. Aptitude, Cabal, Bower) that **finds, installs, configures and manages** packages. 

A **package** is an archive that UUPM processes. The package has a UUPM-supported configuration file and one or more files or directories. In most cases the files of a package are plug-ins, textures or model replacements for a Unity game or some external utilities.

UUPM also comes with an exposed API to provide support for any modding tools and formats. UUPM is divided into the core and the front-end.

#### **Front-end interface**

The core of UUPM should be separated from the front-end to allow both CIL and GUI implementations. UmaiUme Union will provide an simple command-line interface to interact with **all** of UUPM functions.

#### **Modularity**

Like ReiPatcher, UUPM should support game-specific configuration. Moreover, package-specific configuration should be supported as well to allow for different installation methods and user-configurable mods.

One important aspect of UUPM is that it **does not** patch or install anything. Instead, it only does important housekeeping and mod managing, while delegating the patching or modding itself to external modules.

#### **Modules**

A **module** is a DLL that contains methods to apply a certain type of mod to a game. The methods and procedures are linked to UUPM through an API it provides.

Key points for modules

* A module provides only two functionalities: *the core*  and the *communication layer*. The core applies the contents of a package according to implementation and specification set by the developer of the module. The communication layer allows the module to receive instructions from UUPM. In addition, the module may send some instructions back to UUPM to allow advanced patching mechanisms.
* A module's core should only implement the main patching procedures. All configurations, paths and dependencies are parsed or resolved by UUPM and sent to the module through the communication layer.
* UUPM does not specify nor care about the implementation of the core nor the structure of the packages. Therefore, all modules can use their own specific package structures. This allows not only to create generic modules for external tools, but also mod-specific modules to create packages to use only with certain in-game mods.
* The communication layer is strictly defined in UUPM's API. The configuration file is also part of the communication layer and is the only file that **must** be found in any package. Every module must support a basic set of configuration parameters, but modules can also specify parameters of their own.

UmaiUme Union will provide the following basic modules to install mods into most Unity Engine games: `ModuleReiPatcher` to install and apply mods with ReiPatcher; `ModuleUnityInjector` to install, manage and remove plug-ins for UnityInjector; `ModuleSB3UGS.Script` to run SB3UGS scripts; and `ModuleUUPM` to install tools and modules for use with UUPM.

#### **Configurations**

Like ReiPatcher, UUPM will come the ability to configure the tool with game-specific behaviour. In addition every package that will be installable through UUPM must provide a configuration file of its own. Those configuration files found in packages will contain the information about the package, like its name, version, dependencies, modules to use and configurations specific to modules.

Users may edit the configurations manually through either an external text editor or with the help of UUPM.

Currently, no format for configuration files has been decided upon, but it might be that UUPM will use some form of ExIni with additional syntax.
Other possible formats are JSON and YAML.

#### **Other features to consider**

* Being a package manager, we could consider implementing a package repository for UUPM. That way users could install mods by simply using the front-end of UUPM instead of downloading the packages one-by-one. However that would require to have a server(s) that would house the packages.
* Currently UUPM is mainly aimed at games made in Unity Engine. However, the modularity of the core allows to create modules to install mods for any other game, as the game-dependent code is outsourced to the modules.

#### **Implementation**

Currently, the roadmap is as follows:

1. Implement the internal core to manage, process and sort the packages.
2. Build the UUPM's CIL front-end.
3. Implement the communication layer API and devise the specification for configuration files.
4. Work on basic set of modules mentioned earlier.
5. Release into the public with at least one module ready. Work on the documentation.
6. Additional features?

### About source-code and external tools

As the last (but not least) thing to touch upon is the source-code of UmaiUme and licensing.

To keep it simple and fair, here is what I propose:

1. Every tool built within UmaiUme Union shall be published under either the MIT or The Unilicense. That is, if the member of UmaiUme Union posts the code on UmaiUme's repository, it must include either of the two licenses.
2. Every external tool or module built for UUPM can be licensed in any way the original developer wants, as long as it permits the use of such tool in UUPM. That means that if the tool or some assembly for UUPM does not allow it to be distributed with UUPM, it may not be included in the official UmaiUme bundle or the public UmaiUme package repository (if there will be one).

Of course that would create an issue for example with ReiPatcher, which has no license. That, however, can be easily resolved by either simply redirecting people to install the tool manually or getting the permission to include the tool in UmaiUme from **usagirei**.

All in all, I think this is the most fair solution for all of us.
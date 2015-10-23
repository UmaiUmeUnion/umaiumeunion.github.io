---
layout: main
title: "UmaiUme"
---

<div markdown="1" class="col-md-4">
## All things Unity modding

UmaiUme is an effort to create a solid, simple and generic solution to work with
any game made for Unity Engine. The package includes various tools created by the HF community, like
[ReiPatcher](http://usagirei.github.io/#!/downloads/reipatcher/main), [UnityInjector](http://usagirei.github.io/#!/downloads/unityinjector/main) and [SB3Utility(GUI+Script)](http://www.hongfire.com/forum/showthread.php/112039-SB3Utility-Releases-and-Discussion/page61?p=3352965#post3352965). To glue all the tools together there is the true core of UmaiUme -- **mod package manager**.


In addition, not only does UmaiUme provides the tools,
but documentations, guides and utilities for the developers and basic end-users alike.

The tutorials cover all the base tools used by UmaiUme, like ReiPatcher, UnityInjector, Mono.Cecil and SB3Utility(G+S).
Even if you are not a mod developer, you might still enjoy step-by-step guides for tools found in UmaiUme base.
</div>
<div markdown="1" class="col-md-4">
## Built to be built on

Not any game can be edited efficiently by the base tools alone -- sometimes more specific or more advanced tools are needed.

This is why extendibility is the true philosophy behind UmaiUme.

UmaiUme does the tedious task of processing and managing mods. Everything else -- installation, configuration and removal -- is made to be fully editable by anyone.

To support basic modding for any game, UmaiUme is bundled with a generic plug-in patching modules. 
However, real power-users and developers can make their own custom modules to handle game-specific or advanced mod and plug-in installation.
</div>
<div markdown="1" class="col-md-4">
## Follow the development

UmaiUme is still in development.

Here is a nice progress bar (roughly) specifying the progress:

{% include progressbar.html percentage='15' %}

To see more exact news concerning the development, visit the *Dev news* page.

<br/>

#### **Latest post:** [{{ site.posts[0].date | date: "%b %-d, %Y" }} - {{ site.posts[0].title }}]({{ site.posts[0].url }})

<br/>
<a class="btn btn-default" href="{{ site.baseurl }}/devnews" role="button">Development news</a>
</div>
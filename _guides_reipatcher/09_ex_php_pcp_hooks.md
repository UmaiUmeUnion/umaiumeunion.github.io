---
title: "Example: Making hook-based patchers"
---

# Example: Making hook-based patchers

As mentioned in the previous chapter, hooks (custom events for in-game methods) form their own part of the PHP/PCP architecture. In this chapter we will briefly discuss how to find the methods to hook, how to create an appropriate hook for a certain method and how to patch the hook calls into the game using ReiPatcher.

<div class="alert alert-info" role="alert">
<span class="glyphicon glyphicon-exclamation-sign" aria-hidden="true"></span>
<span class="sr-only">Note:</span>
<span class="alert-text">This is merely an example of a PHP/PCP architecture-based workflow. For more examples consider visiting the [examples project page on GitHub](https://github.com/UmaiUmeUnion/UsageExamples).</span>
</div>

## Workflow overview
Using the PHP architecture, mod creation can be performed as follows:

1. Identify target assemblies and methods.
2. Write the appropriate hook methods in the hook module.
3. Write the appropriate patching procedures in the patcher module.
4. Write the appropriate logic and event handlers in the plugin module.
5. Iterate if needed.

In this chapter we will only cover the creation of the hook and patcher module. For information on how to make a plugin module, refer to the [UnityInjector guide]({{ site.baseurl }}{{ site.guides_unityinjector[0].url }}).

### Target searching using decompilers

Before writing hooks -- or even editing in-game methods using Mono.Cecil -- you will most likely want to locate the methods of interest. While most IDEs provide a simple assembly overview, they usually hide "unwanted" classes and its members, like private or internal ones. Moreover, the source code is hidden as well. Fortunately, .NET assemblies are compiled into CIL (Common Intermediate Language) -- a language with an open specification and ASM-like syntax, which makes it easy to decompile said assemblies.

In this section we will use [ILSpy](http://www.ilspy.com/) to view the code and locate the method to hook. A list of different decompiler can be found in the [requirements section](06_api_guide_setup.html#tools-and-requirements).

To view the code of a target assembly, do the following:

1. Download and launch ILSpy.
2. Click `File->Open` (or press Ctrl+O) and navigate to the location where the target assembly is.
3. Choose the target assembly and click `Open`.
4. Click on the "+" button left to the loaded assembly to expand the view and see namespaces contained in the assembly.

From here it's all detective's work: simply look through the assembly's namespaces and types trying to locate the method/types of interest.

The following tips may help searching:

* Clicking on the "+" left to any item will expand it to show the sub-items. Namespaces expand into types (classes, structs, etc) and those expand into members (fields, methods, properties). Clicking on the type or its member will show its definition in C#, VB.NET or pure CIL depending on the chosen settings.
* Right-clicking any item and selecting `Analyze` will bring up the analyser window which will contain such information as where the item is used, where it is defined and where it is exposed to other types. That will significantly speed up the process of finding methods to hook or use.
* Read the names and definitions, for most of the time they reveal the purpose of the type or the method.
* By changing the settings you can view the source code in pure CIL. Use this when planning out the patcher module.

When you have found what you need, you can either proceed to patch it or plan out the hooks.

### Writing a basic hook module

Assuming the hook classes are placed in a separate project (and therefore a separate DLL), consider which assemblies are required to make the hooks function. Since the hook DLL is usually placed in the same place as the target DLL, referencing the target assembly is quite usual. In addition, you may want to reference some addition assemblies from the same folder to make the hooks functional.

The structure of the project is not well defined and may depend on the mod. However, most of the hook projects contain static hook methods the call to which will be injected into the game's method of interest through the patcher. As an example, here is a possible structure of a single hook class located in the hook DLL:

{% highlight csharp %}
namespace GAME.MyPlugin.Hook
{
    public class CustomEventArgs : EventArgs
    {
        public int A { get; private set; }
        public byte B { get; private set; }
    }

    public static class Hooks
    {
        public static event EventHandler<CustomEventArgs> MyOtherHookHandler;

        private static void NotAHook()
        {
            // Some other logic
        }

        public static void MyHook()
        {
            // Hook logic goes here
        }

        public static void MyOtherHook(int a, byte b)
        {
            // AN EXAMPLE OF HOOK LOGIC: Using C# events to create an event handler.

            CustomEventArgs args = new CustomEventArgs { A = a, B = b };
            MyOtherHookHandler?.Invoke(null, args);
        }
    }
}
{% endhighlight %}

A few important observations:

* A proper hook method **must** be static in order to be patchable. Otherwise the patcher will be unable to inject it into the game's assembly without complex CIL manipulation.
* Not all methods must be hooks. Instead they may contain some logic performed by the main hooks themselves.
* The hook classes should be set to be static. That way the C# will make sure the hook methods are all marked as static.
* The hooks may receive parameters and may return them, but the more complex the signature is, the bigger the patcher is and the harder it is to adapt to the changes in the target method.

In the PHP architecture, the hook is created as a bridge between the game's code and the plugin module. That is why it is convenient to use [C# events](https://msdn.microsoft.com/en-us/library/aa645739(v=vs.71).aspx) to create seamless event handlers for the plugin module to use.

### Writing a patcher
Next, the methods should be linked to the target assemblies using the patcher module. Refer to the [ReiPatcher API overview](07_api_guide_patcher.html) to see how to create a basic patcher.

In this particular example, let us consider that `Assembly-CSharp.dll` has a class `Bar` found in the `Foos` namespace. Let us further assume that the class contains a non-static method `Baz(int a, byte b)` and we want to add a call to a `MyOtherHook(int a, byte b)` method in such a way that the hook method receives the parameters of `Baz(int a, byte b)`. The `Patch` method in the patcher will the look something like this (for example):

{% highlight csharp %}
public override void Patch(PatcherArguments args)
{
    // Load type definition for Bar
    TypeDefinition bar = args.Assembly.MainModule.GetType("Foos.Bar");

    // Load type definition for Hooks
    TypeDefinition hooks = hookAssembly.MainModule.GetType("GAME.MyPlugin.Hook.Hooks");

    // Get method Baz
    MethodDefinition baz = bar.Methods.FirstOrDefault("Baz");
    
    // Get a reference to MyOtherHook
    MethodReference hookRef = baz.Module.Import(hooks.Methods.FirstOrDefault("MyOtherHook"));

    // Get the first IL instruction of Baz
    Instruction startInstruction = baz.Body.Instructions[0];

    // Get the IL processor (makes instruction injection a bit easier)
    ILProcessor il = baz.Body.GetILProcessor();

    // Add IL
    // Reminder: since Baz is not static, Ldarg_0 pushes "this" onto ES
    il.InsertBefore(startInstruction, il.Create(OpCodes.Ldarg_1));           // Pass the first parameter of Baz
    il.InsertBefore(startInstruction, il.Create(OpCodes.Ldarg_2));           // Pass the second parameter of Baz
    il.InsertBefore(startInstruction, il.Create(OpCodes.Call, hookRef));     // Create a call to MyOtherHook

    // Do some other patching...

    // Add a tag to the assembly to specify that it has been patched
    SetPatchedAttribute(args.Assembly, "MY_PLUGIN_TAG");
}
{% endhighlight %}

Similarly the same can be done with other hooks and targets.

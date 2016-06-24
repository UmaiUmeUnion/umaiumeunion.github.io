---
title: "ReiPatcher API: Writing patchers"
---

# ReiPatcher API: Writing patchers

So far we discussed only the basics of writing patchers, but we haven't touched upon ReiPatcher's API yet. In this chapter we will discuss how to work with ReiPatcher's API and will cover the basics of writing a patcher.

## The patcher project layout
Patchers should be always made as a separate project (and therefore a separate DLL). Name of the project (and the DLL) can be chosen by the developer, but it is advised to follow the [PHP/PCP naming convention](08_guideline_php_pcp_architecture.html#naming-conventions).

As for assembly references, the patcher must always have a reference to ReiPatcher.exe and Mono.Cecil.dll. Additionally, the patcher can reference helper libraries, extensions and assemblies found in the folder specified by the ReiPatcher game configuration's `AssembliesDir` key.

The project itself can contain any classes possible, but ReiPatcher will only process those non-abstract classes that inherit `PatchBase` class found in ReiPatcher.exe. Therefore a single patcher DLL can contain multiple patchers.

## The `PatchBase` class
All the interaction between ReiPatcher and the patcher is done through the `PatchBase` class. Thus a generic patcher class usually looks as follows:

{% highlight csharp linenos %}
using Mono.Cecil;
using Mono.Cecil.Cil;
using ReiPatcher;
using ReiPatcher.Patch;

namespace GAME.MyPlugin.Patcher
{
    public class MyPluginPatcher : PatchBase
    {
        // (Optional) Name of the patcher
        public override string Name => "MyPlugin Patcher";

        // (Optional) Version of the patcher
        public override string Version => "1.0";

        public override bool CanPatch(PatcherArguments args)
        {
            // Check that the Patch method should be called
        }

        public override void Patch(PatcherArguments args)
        {
            // Perform patching using Mono.Cecil
        }

        public override void PostPatch()
        {
             // (Optional method) Do something after patching
        }

        public override void PrePatch()
        {
            // (Optional method) Do something before patching
        }
    }
}
{% endhighlight %}

You can use the presented patcher class as a generic template for all your patchers. Note that the methods and properties marked with `(Optional)` can be removed -- in that case ReiPatcher will use its own default implementation.

## How to: write a basic patch

Now, let us take a look at how to use ReiPatcher's API and create a working patcher. Here we assume that you have already set up the patcher project with at least one patcher class that inherits `PatchBase` and that you added the methods according to the template above.

**Do the following:**

#### 1. Request the target assembly to be patched and load additional assemblies.

The assembly requesting and loading should be done in the `PrePatch()` method. Requesting can be done by using `RPConfig.RequestAssembly(string name)` method. In addition, you can load assemblies using .NET.

As an example, we shall request an assembly named `Assembly-CSharp.dll` and load an assembly called `SomeOtherAssembly.dll`.

{% highlight csharp linenos %}
private AssemblyDefinition hookAssembly;

public override void PrePatch()
{
    // Request Assembly-CSharp.dll to be patched
    RPConfig.RequestAssembly("Assembly-CSharp.dll");

    // Load the hook assembly
    string path = Path.Combine(AssembliesDir, "SomeOtherAssembly.dll");
    if(!File.Exists(path))
        throw new FileNotFoundException("Missing hook dll!");

    using(Stream s = File.OpenRead(path))
    {
        result = AssemblyDefinition.ReadAssembly(s);
    }

    if(result == null)
         throw new NullReferenceException("Failed to read hook assembly!");
}
{% endhighlight %}

Note the following:

* The requested assembly must be found from the path specified by `AssembliesDir` property in the patcher configuration.
* In `PatchBase`, the `AssembliesDir` property is the value of `AssembliesDir` in the patcher configuration that is being used.
* `RPConfig` class provides methods to read and edit current patcher configuration.

#### 2. Check that an assembly should be patched
Since different patchers may request different assemblies, ReiPatcher must determine which ones should be patched by which patchers. In addition, if the patcher has already been used on the assembly, we need to prevent ReiPatcher from running it through the same patcher twice. All these checks are done by overwriting `CanPatch(PatcherArguments args)` method. Simply put, the method should return `true` if ReiPatcher should patch the assembly specified in `args` with this patcher and `false` otherwise.

Continuing with the example, we use `GetPatchedAttributes` method found in `PatchBase` and LINQ to check that our patch hasn't been applied yet and a simple name comparison to check whether we need to patch the specified assembly.

{% highlight csharp linenos %}
public override CanPatch(PatcherArguments args)
{
    return args.Assembly.Name.Name == "Assembly-CSharp" 
           && GetPatchedAttributes(args.Assembly).All(att => att.Info != "MY_PLUGIN_TAG");
}
{% endhighlight %}

Note how we check if `"MY_PLUGIN_TAG"` is set to the assembly. This tag will be added to a patched assembly in the next step.

#### 3. Perform patching
If ReiPatcher determines that an assembly can be patched, it calls `Patch(PatcherArguments args)` method with the same `PatcherArguments` as in step 2. In the `Patch(PatcherArguments args)`, method you can use Mono.Cecil, extension libraries and previously loaded assemblies to manipulate the assembly passed into the `args` parameter.

Since the patching procedures depend from mod to mod, it is advised that you visit the [examples project on GitHub](https://github.com/UmaiUmeUnion/UsageExamples) to see how different patchers work.

Note the following when overwriting the `Patch` method:

* Using pure Mono.Cecil requires to inject raw CIL instructions into the target method. For simple patches this is easy, but more complex methods may require more IL opcodes to be injected. For more information on how to work with IL instructions, refer to [the list of all available instructions on MSDN](https://msdn.microsoft.com/en-us/library/system.reflection.emit.opcodes_fields(v=vs.110).aspx) and tutorials on IL (one of them can be found [here](http://www.codeproject.com/Articles/3778/Introduction-to-IL-Assembly-Language), for instance).
Alternatively, you can consider using some helpers or extension libraries, like (a list of them can be found [here](06_api_guide_setup.html#list-of-additional-patcher-tools)).
* In the end you will want to use `SetPatchedAttribute` to add the `"MY_PLUGIN_TAG"` to the assembly. **This step is important!** Forgetting to set the attribute may case unpredictable behaviour, since the patcher would be run every time ReiPatcher is used.

#### 4. (Optional) Finalise patching
After the patching is done, ReiPatcher will call `PostPatch()` method on every patcher whether or not the patching actually commenced. Here you can do finishing touches, like moving files or editing configuration files.

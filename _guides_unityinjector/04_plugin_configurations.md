---
title: "Plugin configurations"
---

# Plugin configurations

The `PluginBase` class in UnityInjector provides `Preferences` property that is used to easily create and read one INI configuration file.

The INI file that is exposed by the `Preferences` property is saved into `<UnityInjector folder>\Config` folder and is named `<PluginName>.ini`, where `<PluginName>` is the name of the plugin class.

By default, UnityInjector uses ExIni to manipulate the configuration file referenced by `Preferences` property. In this chapter we cover the basic use of ExIni and some tricks to read the configuration file.

## Reading INI plugin preferences with ExIni
ExIni comes bundled with UnityInjector -- in order to use it, remember to add a reference to `ExIni.dll` in your plugin project.

### Basic method
Most of the time you will only want to read the configuration file and obtain plugin-specific settings (and possibly fix faulty ones). This is usually done in a single method, commonly called `LoadConfig()`. As an example, let us consider the following configuration file for a plugin.

{% highlight ini linenos %}
[Baz]
Foo=Bar
Qus=True

[Groot]
CatAge=25
{% endhighlight %}


The following snippet shows one of the ways to load, parse and possibly fix the INI.

{% highlight csharp linenos %}
// Default values for plugin's parameters
private const string DEFAULT_FOO = "Qux";
private const bool DEFAULT_QUS = false;
private const int DEFAULT_CAT_AGE = 10;

// Values that will be parsed from plugin's preferences
private string foo;
private bool qus;
private int catAge;

// Loads and parses the configuration file
private void LoadConfig()
{
    // Using Preferences property definied in PluginBase.
    // Gets property Foo defined in section Baz.
    IniKey key = Preferences["Baz"]["Foo"];
    
    // If Foo wasn't defined in the preferences, ExIni creates a property named Foo with 'null' as a value.
    // If that is the case (or the value of Foo is empty), just set the value to default and save the preferences
    // by calling SaveConfig() (also defined in PluginBase).
    if(string.IsNullOrEmpty(key.Value))
    {
        foo = DEFAULT_FOO;
        key.Value = DEFAULT_FOO;
        SaveConfig();
    }
    else
        foo = key.Value;
    
    // Get next property.
    key = Preferences["Baz"]["Qus"];
    
    // Note that here we also parse the value of Qus straight into field qus.
    // If parsing fails (or the property was either null or empty), replace it with its default value.
    if(string.IsNullOrEmpty(key.Value) || !bool.TryParse(key.Value, out qus))
    {
        qus = DEFAULT_QUS;
        key.Value = DEFAULT_QUS.ToString();
        SaveConfig();
    }
    
    // Do the same for CatAge
    key = Preferences["Groot"]["CatAge"];

    if(string.IsNullOrEmpty(key.Value) || !int.TryParse(key.Value, out catAge))
    {
        catAge = DEFAULT_CAT_AGE;
        key.Value = DEFAULT_CAT_AGE.ToString(CultureInfo.InvariantCulture);
        SaveConfig();
    }
}
{% endhighlight %}

Given the previous snippet, here are some important notes regarding using the `Preferences` property:

* The `LoadConfig()` method is usually called in the `Awake()` method. You may also use the constructor.
* Remember to add `using ExIni;` before using ExIni methods.
* `Preferences` is accessible from the plugin class and is of type `IniFile` (from `ExIni`).
* [**Line 16**] Calling/referencing `Preferences` for the first time will attempt to load the configuration file `<PluginName>.ini` (`<PluginName>` is the name of the plugin class) from `UnityInjector\Config`. If there are no such file, a new file is created.
* [**Lines 16, 31, 43**] A property (key) within `Preferences` can be loaded by calling `Preferences["<Section>"]["<Property>"]` where `<Section>` is the section and `<Property>` is the property.
* Using `Preferences["<Section>"]["<Property>"]` is null-safe: if a property/section with such name does not exist, it is created automatically.
* The property is of type `IniKey`, and you can get its value from `Value` property.
* [**Lines 21, 35, 45**] If a property did not exist before, its value is `null`. You can use `String.IsNullOrEmpty(string str)` to check that the value is not empty and is not `null`.
* [**Lines 25, 39, 49**] `PluginBase` provides `SaveConfig()` method to save `Preferences` to a file. The file's name and save are the same as discussed earlier.

The above method combines two functions: checking if a property exists and setting a property to the default value if it does not. Sometimes, however, you may only want to check whether a property is present in the configuration file. This can be done with the following helper method:

{% highlight csharp linenos %}
public bool HasKey(string section, string key)
{
    return Preferences.HasSection(section) 
           && Preferences[section].HasKey(key) 
           && !string.IsNullOrEmpty(Preferences[section][key].Value);
}
{% endhighlight %}

### Using C# properties
Another way of accessing properties within `Preferences` (and ExIni altogether) is by creating wrapper properties in the plugin class. That way the properties inside the configuration file are automatically parsed from strings into their native types and vice versa.

Using this method is very simple: for each property inside the INI configuration, create an appropriate property inside the plugin's class. The getter of the property will read the property from the INI file, and the setter will set it and save the configuration file.

As an example, let us wrap property `CatAge` from the previous INI file. One way to do this is as follows:

{% highlight csharp linenos %}
// Define the default value for cat age
private const int DEFAULT_CAT_AGE = 10;

// Wrap CatAge into C# property
public int CatAge
{
    get
    {
        // Using Preferences property definied in PluginBase.
        // Gets property CatAge defined in section Groot.
        IniKey key = Preferences["Groot"]["CatAge"];
        int result;

        // If CatAge wasn't defined in the preferences, ExIni creates a property named CatAge with 'null' as a value.
        // If the value is not 'null' or empty, attempt to parse it as integer.
        // If parsing fails or value is either 'null' or empty, set it to the default value and save the INI
        // by calling SaveConfig() (also defined in PluginBase).
        if(string.IsNullOrEmpty(key.Value) || !int.TryParse(key.Value, out result))
        {
            result = DEFAULT_CAT_AGE;
            key.Value = DEFAULT_CAT_AGE.ToString(CultureInfo.InvariantCulture);
            SaveConfig();
        }

        return result;
    }

    set
    {
        // If CatAge wasn't defined in the preferences, ExIni creates a the property automatically.
        // Thus it is enough to just set it to the value.
        Preferences["Groot"]["CatAge"].Value = value.ToString(CultureInfo.InvariantCulture);

        // Remember to save the configuration file after the value has been set
        SaveConfig();
    }
}

public void Awake()
{
    // Reading the property is as easy as accessing a variable
    Console.WriteLine("The cat is " + CatAge + " years old.");
    
    // The value is set and saved automatically
    CatAge = 5;
}
{% endhighlight %}

Refer to the notes above for more information on ExIni.

### More abstract approaches
While the above-mentioned methods are easy to implement, you may want to make configuration loading more streamlined. With C#, there are many ways to achieve this, but we shall not delve in exact implementation details for the sake of keeping this tutorial simple.

Here are some suggestions on how to make configuration parsing more automated:

#### 1. You can use `IConvertible` interface to convert types to others
`System.String` implement `IConvertible` interface which provides `ToType(Type, IFormatProvider)` method to (attempt to) convert a string to any type. In addition, any type implements `ToString()` method to convert any type to its string representation. You can use these interfaces -- and the fact that all native types (`byte`, `int`, etc.) are convertible from a string -- to create wrapper/unwrapper methods.

#### 2. You can also use Reflection and configuration wrapper structures to read/write configuration files
With `System.Reflection` you can create, access and modify types dynamically. That way you can, for instance, create a structure that contains fields with the same names as the properties found in the INI, and write a method that creates an instance of said structure, scans through its fields and assigns a property of the same name to them. That way adding new properties is as easy as adding a new field into the configuration structures.

#### 3. Consider using pre-made libraries to suit your needs
UnityInjector does not enforce the usage of its own `Preferences` property, nor do you need ExIni to read the configuration files. You can just as well use the Profile API, XML, YAML or any other data serialization technique for your plugin.

In this chapter we shall take a brief look at one of these pre-made libraries that uses Reflection to read INI files.

## Using Reflection to read into classes and structures: `ExPluginBase`
[ExPluginBase](https://github.com/asm256/ExPluginBase) is an extension to `PluginBase` found in UnityInjector. The extension provides methods to read configuration files and to write debug messages to the Debugger console.

### How to use

To use `ExPluginBase`, download the .cs file and place it into your project. Finally, for every plugin class you have, change it to inherit `ExPluginBase` instead of `PluginBase`.

##### Updaing the INI
<div class="alert alert-info" role="alert">
<span class="glyphicon glyphicon-exclamation-sign" aria-hidden="true"></span>
<span class="sr-only">Note:</span>
<span class="alert-text">As you later will notice, you don't need to do any changes to the INI file in order to make it work with `ExPluginBase`. However by doing these changes configuration loading will be as easy as making a single method call.</span>
</div>

Next, we shall edit our [previous INI file](#basic-method) to make it a bit easier to load with `ExPluginBase`. To do that, simply move all properties under `Config` section.
Thus the example INI will look something like this:

{% highlight ini linenos %}
[Config]
Foo=Bar
Qus=True
CatAge=25
{% endhighlight %}

##### Creating a wrapper
Next, you will want to create a wrapper structure (or class) for the configuration properties. The wrapper structure must have the properties that have the same name as those found in the INI file. In our case we create the following structure:

{% highlight csharp linenos %}
// Note that we define default values inside the structure itself
public struct PluginConfiguration
{
    public string Foo = "Qux";
    public bool Qus = false;
    public int CatAge = 10;
}
{% endhighlight %}

<div class="alert alert-info" role="alert">
<span class="glyphicon glyphicon-exclamation-sign" aria-hidden="true"></span>
<span class="sr-only">Note:</span>
<span class="alert-text">The names are not case-sensitive. Thus, for example, a field `foo` will get the same value as field `Foo` when the configuration file is loaded.</span>
</div>
<div class="alert alert-info" role="alert">
<span class="glyphicon glyphicon-exclamation-sign" aria-hidden="true"></span>
<span class="sr-only">Note:</span>
<span class="alert-text">`ExPluginBase` will attempt to parse only those properties that are defined in a wrapper structure. Thus you don't need to define all the properties.</span>
</div>

##### Parsing and saving the configuration
Finally, to actually load the configuration, you will need to call `ReadConfig<T>()` method defined in `ExPluginBase`. You can do that, for instance, in the `Awake()` method (used by `MonoBehaviour`):

{% highlight csharp linenos %}
public void Awake()
{
    // Reads the configuration file, parses the properties found in the 'Config' section
    // and assigns them to a new instance of PluginConfiguration
    PluginConfiguration cfg = ReadConfig<PluginConfiguration>();

    // Reading the values is now easy
    Console.WriteLine("The cat is " + cfg.CatAge + " years old.");

    // Assigning too
    cfg.CatAge = 5;
    
    // Saving is done with 'SaveConfig' method
    SaveConfig<PluginConfiguration>(cfg);
}
{% endhighlight %}

Note the following:

* If you have multiple sections, you can read them by using `SharedConfig.ReadConfig<T>(string section, string fileName)`, where `fileName` is `Name + ".ini"`.
* You can have multiple wrapper classes with different fields, since `ReadConfig<T>()` will parse only those properties that are defined in the wrapper structure itself.


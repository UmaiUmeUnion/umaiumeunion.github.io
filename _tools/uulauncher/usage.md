## How to set-up

#### 1. Download UULauncher
Download the latest release of UULauncher from the links above.
The clean version of UULauncher comes only with the following files: `README.txt` that contains similar guide as this one, `LICENSE` that contains the
MIT licence and `UULauncher.exe` that is the UmaiUme Launcher executable.

Copy the executable to the game's root directory (where the game's executable is located).
<div class="alert alert-info" role="alert">
<span class="glyphicon glyphicon-exclamation-sign" aria-hidden="true"></span>
<span class="sr-only">Note:</span>
<span class="alert-text">
**ADVANCED:** You can also place `UULauncher.exe` in some other folder than the game's root, since you will be able to specify the game's path in the configuration file later. 
</span>
</div>

#### 2. Download patcher tools
Currently UULauncher requires only ReiPatcher's assemblies to work.
Thus you need to only download ReiPatcher and the assemblies that come with it.

After downloading, unpack ReiPatcher (`ReiPatcher.exe`, `ExIni.dll`, `Mono.Cecil.dll` and other assemblies) into `<Game Root>\ReiPatcher` or other folder.

#### 3. Run UULauncher for the first time
Next, double-click `UULauncher.exe` to run it.
If everything works, UULauncher will prompt you to create a default configuration file -- select `Yes` to create it.

<div class="alert alert-info" role="alert">
<span class="glyphicon glyphicon-exclamation-sign" aria-hidden="true"></span>
<span class="sr-only">Note:</span>
<span class="alert-text">
**ADVANCED:** In Command Prompt, running `UULauncher.exe -h` will show a help box with all the advanced arguments that UULauncher currently supports. 
</span>
</div>

#### 4. Edit the configuration file
Open the default configuration file (named `UULauncher.ini`) in the text editor of your preference.
Read the editing guide contained withing the configuration file and edit it to your need/preference.

Most importantly, edit the keys specified by `;EDIT ME` comment. Those keys include the paths to ReiPatcher, the patches and the game's executable.

<div class="alert alert-info" role="alert">
<span class="glyphicon glyphicon-exclamation-sign" aria-hidden="true"></span>
<span class="sr-only">Note:</span>
<span class="alert-text">
**ADVANCED:** You can specify a locale emulator (or another executable) to run instead of the game when the patching is complete.
In that case you **must** specify the `GameExecutableName` key with the game's executable name. If that key is specified, UULauncher
will attempt to find a process with the specified name to bind to. If `GameExecutableName` is not specified, UULauncher will be unable to
restore the game's assemblies after the game closes.
</span>
</div>

**If you already have a pre-made configuration file**, simply replace `UULauncher.ini` with it.

#### 5. Install patches and run UULauncher
Finally, run UULauncher every time you want to run a patched game.

<div class="alert alert-info" role="alert">
<span class="glyphicon glyphicon-exclamation-sign" aria-hidden="true"></span>
<span class="sr-only">Note:</span>
<span class="alert-text">
**ADVANCED:** Double-clicking `UULauncher.exe` will run UULauncher with the default configuration. Run `UULauncher.exe -c <PATH>` (where `<PATH>` is the path to the configuration file) to run UULauncher with a custom configuration file.
</span>
</div>

## Advanced: Current supported arguments
UULauncher supports command-line arguments.

Run `UULauncher.exe [ARGUMENTS]` with the one of the following arguments to change UULauncher's behaviour:

| Argument | Definition |
| ---- | ---------- |
| `No arguments` | Runs UULauncher with the default configuration. Creates the configuration file if it does not exist. |
| `-h` | Displays the help window. |
| `-c <PATH>` | Runs UULauncher with the configuration file located in `<PATH>`. |
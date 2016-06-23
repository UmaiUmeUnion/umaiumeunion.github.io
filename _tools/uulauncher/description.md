## UmaiUme Launcher (**UULauncher**)

UULauncher is an Unity Engine game launcher that allows to patch game's assemblies and manage them while the game is running.

UULauncher is designed to be used alongside game patchers to make patching experience much smoother.
Currently UULauncher has pre-built support for ReiPatcher, but other custom patcher modules will be supported in the future!

### Key features

* **Easy to use:** Simply run it instead of the game's executable to automatically patch and run the game
* **Easy to set up:** Just edit a configuration file (instructions inside) and you're good to go!
* **Some sweets for power-users:** Customize the behaviour of the tool through command line switches and the configuration file!
* **Automatically restores assemblies** to unpatched state when the game exits
* **Logs** every launch
* **Compatible** with other patchers (Sybaris, IPA, ReiPatcher, etc.)
* **Open-source!**


### Differences from ReiPatcher and other patching tools
As a comparison, here is a table that displays key differences between running pure ReiPatcher versus using UULauncher with ReiPatcher support:

|  | UULauncher (with ReiPatcher support) | ReiPatcher |
| ---- | ---------- | --------- |
| Running | Runs by default with a double-click. Supports additional switches for more precise control. | Requires te be run either through Command Prompt or by creating a custom batch file. |
| Patching assemblies | Done automatically before the game is run. | Must be executed manually before running the game executable. |
| Restoring assemblies | Done automatically after the game is closed (or when UULauncher is used the next time). | Must be done manually. |
| Removing patches | Remove the patch DLL from the patches folder | Remove the patch DLL and restore the game's assemblies from backup. |
| Running unpatched game | Simply run the game's executable. | Restore the original assemblies from backup and then run the game's executable. |
| Backups | Creates a single backup of the *original* assembly and puts it in an easy-to-find place. | Creates backups every time ReiPatcher is run, creating a myriad of backups in the long run. |
| Logging | Logs every run automatically, making it easy to troubleshoot later | Does not log nor allows logging through stream redirection (as of v0.9.0.8). |
| Interface | Command-line interface, *GUI will be implemented in the future*. | Command-line interface only. |

<div class="alert alert-info" role="alert">
<span class="glyphicon glyphicon-exclamation-sign" aria-hidden="true"></span>
<span class="sr-only">Note:</span>
<span class="alert-text">
Even with these differences, UULauncher **is not** a replacement for ReiPatcher (or other patching tools), as UULauncher will still utilize the code/assemblies
of the original tools. That means that you will still have to download ReiPatcher to make UULauncher work. However, you **will not** need to install and set-up ReiPatcher, as UULauncher will do that instead.
</span>
</div>

### Planned features

* Modular patcher approach that will allow to create patchers for other tools
* GUI interface (possibly with support to change the game's settings alongside)
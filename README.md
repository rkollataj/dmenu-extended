# dmenu-extended

An extension to the original [dmenu](http://tools.suckless.org/dmenu/) allowing super fast access to your files, folders, and programs. dmenu-extended has support for plugins, command aliasing, file filtering, and customisation. You can also use dmenu-extended with [rofi](https://davedavenport.github.io/rofi/)!.

## See it in action

<p align="center">
  <img src="https://raw.github.com/markjones112358/dmenu-extended/master/demo.gif" alt="Dmenu-extended demo"/>
</p>

## Feature Summary:
* Indexes the files and folders you specify
* Built in support for plugins (internet search, sudo, system package management)
* Systemd integration for cache automatic rebuilding
* Ability to swap the menu to Rofi
* Support for scanning alias files (e.g. .bashrc)

## Dependencies
* **Python**, compatible with versions 2 and 3.
* **dmenu**, preferably version 4.5 or later.
* **terminus font**, a clear and clean bitmap font (optional).

### Quick dependency install:
* Ubuntu - `sudo apt-get install suckless-tools xfonts-terminus`


## Installation
Clone this repository *or* download the zip and extract its contents.

You can try dmenu-extended without installation by running `python dmenu_extended.py` from within the extracted folder.

An AUR package is available here: [dmenu-extended-git](https://aur.archlinux.org/packages/dmenu-extended-git/).

### Global install
Execute `sudo python setup.py install` from within the dmenu-extended directory.

#### Systemd background cache rebuild

Run the separate install script to install systemd background update integration.

    sudo bash systemd-install.sh


### Local installation
#### Virtualenv
Execute `python setup.py install` from within the dmenu-extended directory
#### Manual
Move both `dmenu_extended.py` and `dmenu_extended_run` into a folder that is in your system path.

*For example*:

Create a folder called bin in your home directory (if you don't already have one)

    mkdir ~/bin

Add this folder to your systems `$PATH` variable

    export PATH=$PATH:$HOME/bin

Copy the required dmenu-extended files into your local bin folder

    cp ~/Downloads/dmenu-extended-master/dmenu_extended* ~/bin

To keep your ~/bin folder on the path after restart, add `export PATH=$PATH:$HOME/bin` to the end of `~/.bash_profile`


#### Systemd background cache rebuild

Run the separate install script to install systemd background update integration.

    bash systemd-install.sh --user

# Usage

## Create a keybinding

Test that the new menu has been installed by running `dmenu_extended_run` from your terminal. **NOTE:** The first run will be slow as it is set to scan you home folder recursively to build the cache.

The most productive way to use dmenu-extended is to bind the command `dmenu_extended_run` to an easy to reach key combo. The way in which you do this will be different depending on your desktop environment but here is a brief list.

### Ubuntu (Unity), Debian (Gnome), Mint (Cinnamon)
1. Open **System settings** -> **Keyboard** -> **Shortcuts**
2. Click **Custom shortcuts** and then the **+** (*add custom shortcut*) to add a new command
3. Enter "dmenu_extended" as the name
4. Enter "dmenu_extended_run" as the command and click apply
5. Click next *disabled* (*unassigned*)
6. Press the desired combination of keys (e.g. Alt+Enter)

### Tiling window managers
If you use a tiling window manager, you may already have a key-combination bound to launch dmenu (i.e. Ctrl+P). Edit your window managers configuration file to launch `dmenu_extended_run` instead.

### Advanced keybinds
`dmenu_extended_run` supports automated menu item selection via argument passing. If, for example, you frequently use the 'Internet Search' plugin with a particular search provider, you may want a binding that takes you directly to that provider.
To do this you might bind the following command to an alternate key combination:

  `dmenu_extended_run "-> Internet search: " "Wikipedia"`

The arguments must be written exactly as they would appear in the menu. Any number of arguments can be passed and each will be executed in the order they are passed. Each item represents one item selection from a menu. Remember to quote each item so they are passed to the menu correctly.

## General Configuration

Menu configuration is contained in a JSON formatted file found at `~/.config/dmenu-extended/config/dmenuExtended_preferences.txt` that controls the appearance and functionality of the menu. This file is also accessible from the `-> Menu configuration` submenu as `* Edit menu preferences`

Functions of the items are as follows.

* `"valid_extensions"` list of file extensions of files to include in the cache
* `"watch_folders"` list of base paths to recursively search through for items to include
* `"follow_symlinks"` boolean option controlling whether to follow a link while scanning
* `"ignore_folders"` list of paths to be excluded from the cache
* `"global_ignore_folders"` names of folders to exclude from cache (regardless of location)
* `"scan_hidden_folders"` boolean value controlling whether to enter hidden folders when scanning
* `"include_hidden_files"` boolean value controlling whether to include hidden files in the cache
* `"include_hidden_folders"` boolean value controlling whether to include hidden folders in the cache
* `"include_items"` list of extra items to include in the cache
* `"exclude_items"` list of items to be excluded from the cache
* `"include_binaries"` add items found in the system path
* `"filter_binaries"` boolean that causes binaries not associated with destkop applications (those having a .desktop file) to be omitted from the cache (e.g. `cp`, `mv`)
* `"include_applications"` add items found under /usr/share/applications
* `"alias_applications"` alias applications with their intended names
* `"path_aliasFile"` path to a file containing aliases (e.g. ~/.bash_aliases)
* `"frequently_used"` the number of your most frequently used commands to show at the top of the menu
* `"alias_display_format"` how to format aliased commands (e.g. `"{name} ({command})"`)
* `"path_shellCommand"` path to use for creating terminal helper script (e.g. "~/.dmenuEextended_shellCommand.sh")
* `"menu"` executable to open the menu (dmenu)
* `"menu_arguments"` list of parameters to launch the menu with
* `"fileopener"` application to handle opening files
* `"filebrowser"` application to handle opening folders
* `"webbrowser"` application to handle opening urls (web browser)
* `"terminal"` terminal emulator application
* `"indicator_submenu"` symbol to indicate a submenu item in the cache
* `"indicator_edit"` symbol to indicate an item will launch an editor in the cache
* `"indicator_alias"` symbol to indicate an aliased command in the cache

Adding the item `""` to `"valid_extensions"` will cause files with no extension to be included in the cache.
Adding the item `"*"` to  `"valid_extensions"` will cause **all** files to be included in the cache.

## Rebuild the cache from terminal
It is possible to rebuild the cache from the terminal by running:

    dmenu_extended_cache_build

You could run this script directly to rebuild your cache or call it from [cron](http://en.wikipedia.org/wiki/Cron).
Dmenu has [systemd](http://en.wikipedia.org/wiki/Systemd) integration so you can set it rebuild your cache every 20 mins from the settings menu within dmenu-extended.

## Background cache rebuild with Incron

Have [Incron](https://wiki.archlinux.org/index.php/Incron) up and running. Edit your incrontab `incrontab -e` and add following line:

    <PATH_TO_MONITOR>  IN_CREATE,IN_DELETE,IN_MOVE     flock <PATH_TO_MONITOR> -c dmenu_extended_cache_build

This will update your cache everytime you create, delete or move a file from or to the monitored path. The `flock` command locks the command during runtime in the case of multiple events triggered, as this could lead to an incomplete cache. Check out incrontab(5) for more event symbols.

## Rebuild cache via pacman hook

You can update your application cache after installing/uninstalling a package via a [pacman hook](https://wiki.archlinux.org/index.php/Pacman#Hooks) for immediate access/removal.
Create a file `/usr/share/libalpm/hooks/dmenu-cache-rebuild.hook`:

```
[Trigger]
Type = Package
Operation = Install
Operation = Remove
Target = *

[Action]
Description = Rebuilds Cache of dmenu-extended after package installation or removal
When = PostTransaction
Exec = /usr/bin/sudo -u <USER> /usr/bin/dmenu_extended_cache_build
Depends = dmenu-extended-git
```

You need to execute the rebuild command via `sudo -u <USER>`, because pacman runs as root and would therefore not update userfiles.

## Running Dmenu-extended with Rofi
Ensure you have Rofi installed and edit the following two configuration options as so:

    "menu": "rofi",
    "menu_arguments": [
      "-dmenu",
      "-i"
    ],

You may also need to add

    "prompt": "Open",

to remove the extra colon depending on your Rofi version.

## Advanced usage
Dmenu-extended understands the following modifier characters when entering a special command:

1. **+** (plus) - Manually add an entry to the cache
2. **-** (minus) - Remove a manually added entry from the cache
3. **:** (colon) - Open with
4. **;** (semi-colon) - Execute in terminal

These modifiers are entered into the menu; examples follow.

### **+** (plus) - Manually add an entry to the cache
If there is something you wish to run that isn't in the menu then you can add it by prepending with a +.

* `+htop;` adds htop to the cache.
* `+libreoffice` adds libreoffice to the cache.
* `+http://youtube.com` adds a link to Youtube to the cache.

Once added these commands are stored in the preferences file (see general configuration) and will persist upon a rebuild of the cache. These items can also be manually edited within this file.

#### Built-in support for aliases

In addidion to adding items manually, dmenu_extended allows the addition of a more descriptive label for a stored command.
For instance:
* `+htop;#View running processes` displays as `# View system processes (htop)`
* `+libreoffice --writer#Writer` displays as `# Writer (libreoffice --writer)`
* `+http://youtube.com#Youtube` displays as `# Youtube (http://www.youtube.com)`

### **-** (minus) - Remove a manually added entry from the cache
This applies to items that have previously saved to the store. If the item is not found in the store you will be given the chance to add it.

### **:** (colon) - Open with
There are a few different ways to use the colon operator, summarised by example below. In these examples `gedit` is the name of a text editing application.

* `gedit:` - Use gEdit to open a file. A list of all files and folders will be returned to select from.
* `gedit:.txt` - Use gEdit to open a text file. A *filtered list* containing only text files will be shown to select from.
* `/home/me/Documents/writing.txt:` - Open this file using... Returns a list of applications to launch the given file
* `/home/me/Documents/writing.txt:gedit` - Open this file with gedit.
* `gedit:/home/me/Documents/writing.txt` - Open this file with gedit.

### **;** (semi-colon) - Execute in terminal
Dmenu-extended doesn't know when the application you enter needs to be executed in a terminal window. To tell dmenu-extended to launch the following in a terminal, append a semi-colon to the end. Once the terminal program has exited the terminal will close.

For example,

* `htop;` - Launches htop in a terminal window. Without the semi-colon nothing would happen.
* `alsamixer;` - Launches the ALSA sound volume manager in a terminal. Without the semicolon nothing would happen.

#### Holding the terminal open on exit
By using two semicolons (`;;`) at the end of a command the terminal window will remain open once the executed command has completed. This is useful for running programs like `inxi` that exit on completion. You'll want to use this if you see your program flash up and disappear before you get a chance to see the output.

### Loading an alias file
If you have an aliases file (for example ~/.bash_aliases or ~/.zsh_aliases) that you would like loaded into dmenu-extended, set it's path in the `"path_aliasFile"` field in of the preferences. Aliased items found in that file will show up in the menu once the cache has been rebuilt.

### Controlling aliased command formatting
By default, all aliased commands are displayed prefixed without a prefix and no indication of the command being launched is given (for example `ff`). If you would prefer to have the aliased command appear with a prefix and some indication of what the command will launch (e.g. `# ff (firefox)`) - make the following alterations to your preferences file and rebuild the cache.

* `"indicator_alias": ""` -> `"indicator_alias": "#"`
* `"alias_display_format": "{name}"` -> `"alias_display_format": "{name} ({command})"`

### Using a password helper for sudo commands
Password helper programs, such as zenity, can be used to prompt the user for a password outside of a terminal process whenever a password is required. Getting one setup requires creating a small bash script and configuration file. The following instructions step you through using zenity as the password helper program.

1. Check you have zenity installed by running `whereis zenity`. You should see something like /usr/bin/zenity amongst other paths.
2. Fill the password helper script `sudo nano /usr/local/bin/zenity_askpass` with:
```
#!/bin/bash
zenity --password --title="Sudo password prompt"
```
3. Make the askpass script executable by running `sudo chmod +x /usr/local/bin/zenity_askpass`
4. Create/edit the sudo configuration file `sudo nano /etc/sudo.conf` and make sure it contains the line `Path askpass /usr/local/bin/zenity_askpass`

Test the configuration by running a sudo command via dmenu-extended (e.g., `sudo gedit`). You should be prompted grapically for the password before the program opens.

<p align="center">
  <img src="https://raw.github.com/markjones112358/dmenu-extended/master/sc-password.png" alt="Password manager screenshot"/>
</p>

## Acknowledgements
* **Alad** from the [CrunchBang forums](http://crunchbang.org/forums/viewtopic.php?id=36484) for advice on packaging.
* **Head_on_a_Stick** also from the [CrunchBang forums](http://crunchbang.org/forums/viewtopic.php?id=36484) for advice on packaging.
* [**EDI9999**](https://github.com/edi9999) for performance improvements to cache scanning.
* **Pandya** from [this stackexchange answer](https://unix.stackexchange.com/a/254073) for infomation on how to set-up a password helper
* [**nanobecquerel**](https://github.com/nanobecquerel) for improving systemd integration
* [**fat-fighter**](https://github.com/fat-fighter) for bug fixes and solving GitHub issues

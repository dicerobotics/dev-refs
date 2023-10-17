
### Keyboard Customization. (Switching keyboard types, options, layouts, etc.)
Switching the Ctrl, Shift, Alt, and Super keys is one of the things one can change through XKB options. The line XKBOPTIONS in /etc/default/keyboard sets these systemwide. But it's not a good option because it makes the change permanent and difficult to revert by changing XKBOPTIONS again. Moreover, the Gnome Shell desktop can override that with its own dconf setting in 
```shell
gsettings get org.gnome.desktop.input-sources xkb-options.
```
The setting is not exposed in the user interface, but is available through the Gnome Tweaks utility, Keyboard tab. You can also use "dconf-editor" or a "settings" command to access the setting.
By default on Ubuntu, the setting is empty, so you could undo any change made with XKBOPTIONS in /etc/default/keyboard  with
```shell
gsettings reset org.gnome.desktop.input-sources xkb-options
```
The best strategy to manage keyboard and to customize is as per your requirements is through gnome-tweak utility. This utility can be installed with apt package manager

```shell
sudo add-apt-repository universe #Add's universe repository for updates
sudo apt install gnome-tweaks
```

Add Universe Repository
```shell
sudo add-apt-repository universe
```
Gnome Tweaks
```shell
sudo apt install gnome-tweaks
```
Swithcing keyboard options, layouts, etc.
Switching the Ctrl, Shift, Alt, and Super keys is one of the things one can change through XKB options. The line XKBOPTIONS in /etc/default/keyboard sets these systemwide. The Gnome Shell desktop can override that with its own dconf setting in 
```shell
gsettings get org.gnome.desktop.input-sources xkb-options.
```
The setting is not exposed in the user interface, but is available through the Gnome Tweaks utility, Keyboard tab. You can also use "dconf-editor" or a "settings" command to access the setting.
By default on Ubuntu, the setting is empty, so you could undo any change made with XKBOPTIONS in /etc/default/keyboard  with
```shell
gsettings reset org.gnome.desktop.input-sources xkb-options
```

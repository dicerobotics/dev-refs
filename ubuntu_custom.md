## Keyboard Customization. (Switching keyboard types, options, layouts, etc.)

### Approach A
Switching the Ctrl, Shift, Alt, and Super keys is one of the things one can change through XKB options. The line XKBOPTIONS in /etc/default/keyboard sets these systemwide. But it's not a good option because it makes the change permanent and difficult to revert by changing XKBOPTIONS again. Moreover, the Gnome Shell desktop can override that with its own dconf setting in 
``` shell
gsettings get org.gnome.desktop.input-sources xkb-options
```
The setting is not exposed in the user interface, but is available through the Gnome Tweaks utility, Keyboard tab. You can also use "dconf-editor" or a "settings" command to access the setting.
By default on Ubuntu, the setting is empty, so you could undo any change made with XKBOPTIONS in /etc/default/keyboard  with
``` shell
gsettings reset org.gnome.desktop.input-sources xkb-options
```

### Approach B
A practically better strategy to manage the keyboard and to customize it as per your requirements is through gnome-tweak utility. This utility can be installed with the apt package manager

``` shell
sudo add-apt-repository universe #Add's universe repository for updates
sudo apt install gnome-tweaks
```
## Workspace Behaviour
Workspaces only work on primary display be default. If you need to include all connected monitors to the current workspace, use following commands in terminal.
``` shell
gsettings get org.gnome.mutter workspaces-only-on-primary # test current configuration where is "workspace-only-on-primary" is "true" by default
gsettings set org.gnome.mutter workspaces-only-on-primary false # update configuration
```
This behavior may also be configured in Settings->Multitasking. YOu may also consider making fixed number of static workspaces instead of using dynamic workspaces in Ubuntu.

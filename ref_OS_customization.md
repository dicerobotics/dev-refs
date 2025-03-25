## Keyboard Customization. (Switching keyboard types, options, layouts, etc.)

### Ubuntu
**Approach A:**
Switching the Ctrl, Shift, Alt, and Super keys is one of the things one can change through XKB options. The line XKBOPTIONS in /etc/default/keyboard sets these systemwide. But it's not a good option because it makes the change permanent and difficult to revert by changing XKBOPTIONS again. Moreover, the Gnome Shell desktop can override that with its own dconf setting in 
``` shell
gsettings get org.gnome.desktop.input-sources xkb-options
```
The setting is not exposed in the user interface, but is available through the Gnome Tweaks utility, Keyboard tab. You can also use "dconf-editor" or a "settings" command to access the setting.
By default on Ubuntu, the setting is empty, so you could undo any change made with XKBOPTIONS in /etc/default/keyboard  with
``` shell
gsettings reset org.gnome.desktop.input-sources xkb-options
```

**Approach B:**
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


## Personal Tweaks

### Custom keyboard shortcuts for inter-OS compatibility

Keyboard: Apple Keyboard.  
Key Remap: Map **"CMD"** key to **"Ctrl"** key and remap **Ctrl** key to **Win** key in Windows OS.  
Shortcuts: Default + Custom (overwritten to default for consistency across operating systems).  

| Custom Shortcuts | Scope | MacOS | Windows OS | Linux (Ubuntu) | Remark |
|-|-|-|-|-|-|
|Lock Screen	| System	| **CMD+L**	| Ctrl+L |	**Ctrl+L**	| Consistent |
| Address Bar |	Safari/Chrome |	**CMD+K** |	Ctrl+K |	Ctrl+K |	Consistent |
|Volume Up	|System|	F12|	**F12**|	Volume Up|	Consistent|
|Volume Down|	System|	F11|	**F11**|	Volume Down|	Consistent|
|Volume Mute|	System|	F10	| **F10**	|Volume Mute|	Consistent|
|Play/Pause Media	| System|	F8	|**F8**|	Play/Pause Media|	Consistent|
|Previous Track|	System|	F7|	**F7**|	Previous Track|	Consistent|
|Next Track	|System	|F9| **F9** |	Next Track|	Consistent|
|Print Screen|	System|	CMD+Shift+4|	**Ctrl+Shift+4**	|||	
|Bookmark	|Safari/Chrome|		|Ctrl+D		|||
|Next Tab|	Safari/Chrome|	CMD+Shift+]	| **Ctrl+Shift+]**	|||	
|Previous Tab|	Safari/Chrome	|CMD+Shift+[|	**Ctrl+Shift+[**	|||	
|Spotlight Search|	System|	CMD+Space|	**Ctrl+Space** |	**Ctrl+Space** |	Consistent|
|Switch desktop Right|	System|	Ctrl+Right|	**Win+Right**	| **Win+Right** |	Consistent|
|Switch Desktop Left|	System	|Ctrl+Left|	**Win+Left** |	**Win+Left**	|Consistent|  

Note: The shortcuts in **Bold** are the ones changed from the defult.

Current Limitations
- Can’t use Native “Ctrl+Right” and “Ctrl+Left” on Windows OS. It can be reprogrammed but the task is yet pending.
- Can’t use native F7, F8, F9, F10, F11, F12 on Windows OS. There is no solution in mind yet.  

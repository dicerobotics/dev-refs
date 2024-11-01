# Linux Commands and Concepts
This file lists the fundamental concepts to grasp and important commands to operate Linux systems

## Basic Linux commands
``` shell
ls    # List/Display Directory Listings.
cd    #Change Directories.
cp    #Copies a file or directory
mv    #move files/folders
pwd    #present working directory
cat    #Display the Contents of a File to the Terminal Output.
rm    #remove a file/folder
mkdir  #create a directory
df  #view free disk information
du  #view disk usage
find
grep  #Search Files and Directories for Strings or Regular Expressions.
echo
chmod  #Change Access Controls for Files and Directories.
chown  #change ownership
top  #command
w  #command
ssh  #command
su  #switch user
uptime  #command
ps  #Displays information about running processes
systemctl  #Control system services
ifconfig  
usermod  #Modify user account properties.
ping  #Test network connectivity.
kill  #Terminate processes
```

## File compression commands
### Tar Commands
Common flags for tar commands are
c= This flag is used to create a new archive file.
f= This is the file name type of the archive file.
v= This is used to print the output of the tar command verbosely.
r= Used to append or update the existing tar file.

``` shell
tar -cvf backup.tar test      # create a tar file in Linux
tar -xvf backup.tar          # extract the contents of the tar file
tar -cvzf folder.tar.gz test        # create a tar.gz compression
tar -tvf folder.tar.gz    # list the contents of a tar.gz file
tar -xvf backup.tar.gz –wildcards ‘*.png’ #Use of wildcards in the tar command
tar -rvf backup.tar a.txt        # add a file to an existing tar file
```
### Zip commands

``` shell
zip -r linuxlearninghubfiles.zip linuxlearninghubfiles    # How to create a zip file in Linux?
unzip linuxlearninghubfiles.zip    # unzip the zip file above created use the unzip command
zip -u linuxlearninghubfiles.zip newfile.txt  # How to add a file to an existing zip file?
zip -d linuxlearninghubfiles.zip removefile.txt   # How to remove a file from an existing zip file?
unzip linuxlearninghubfiles.zip -d /home/testfolder   # Extract Zip File to a Specific or Different Directory
zipinfo myzip.zip      # List the contents of a zip file
```
## File Transfer Commands
### SCP Command
### Rsync Command




# Concepts
#### Linux and operating system
- Kernal vs. OS tools, Unix and linux, Linux Distributions (distros), Standard linux base
- saftware environment, system preparations, sudo

#### Graphical environment and interfaces
- Graphical layers and interfaces, Using KDE desktop, Using multiple workspaces, Using multiple tabs in a terminal window

#### Getting help
- whatis
- help
- --help
- man
- info
- GUIs: GNOME and KDE
- Multiple man pages

#### Text editing
- echo
- cat, tac
- vi
- vim
- emacs
- gedit
- nano

#### Shells, bash, and the command line
- shells
- shell initialization
- aliases
- Environment variables
- Customizing the command line prompt
- Special characters
- Redirection
- cmd1 | cmd2 | cmd3
- Command substitution and expressions
- which, whereis, echo

#### Filessystem layout, partitions, paths, and links
- Filesystem Layout
- partitions
- fdisk
- partitioning considerations
- Paths
- Hard and soft(symbolik) links
- Adding to path

#### System initialization
- System boot
- /boot Directory
- Using GRUB
- System initialization
- Killing the Graphical User Interface

#### Memory
- Memory
- Swap
- Using Swap and OOM
- Threading Models
- Invoking the OOM Killer

#### Networking
- Networking and Network Interfaces
- Using predictable network interface device names
- ip vs ipconfig
- Static configuration of a network interface

#### System monitoring
- Basic commands and utilities
- File transfer tools
- Monitoring and performance utilities
- Graphical monitoring tools
- Installing and running ksysguard

#### Kernel modules and device management
- Loading/unloading kernel modules
- Device management
- Using udev
- Managing system services
- Using systemctl
- Using stress

#### System Administration
- System installation
- Graphical package management
- Using Yast
- Software management and packaging
- Using rpm
- Using dpkg
- Using dnf
- Upgrading and patching
- User directories, Environments, etc.
- Logging files

#### Users and Groups
- Basics of users and groups
- Adding and removing users and groups
- Creating, modifying, and removing user accounts
- Files, users, and permissions
- root (super) user, su, and sudo

## Linux Tools
Source: "Linux Tools for Developers" course on Coursera. 
Caution: Please be careful if you purchase the above course on Coursera. You can't access course material after your paid subscription ends even if you complete the course and achieve the certificate.
Note: This file is not an exact copy of the above course. It is made for personal use and will be updated continuously.

#### Essential command line tools
- Tools used to list, create, delete, and rename files and directories
- Finding files: find and locate
- grep command
- sed command

#### File and text manipulation tools
- clear
- cat, tac, less, head
- cut
- man
- cd
- using cat interactively
- echo
- working with large files
- head
- tail
- viewing compressed files
- Introduction to sed and awk
- File manipulation utilities
- sort
- uniq
- paste, join, and split
- grep
- strig
- tr
- tee
- tree
- touch
- wc
- ls
- ln
- dirs
- pushd
- popd
- rmdir
- exit
- login
- mkdir
- pwd
  
#### Bash scripting
- Script bash
- Conditions
- Loops
- Functions
- Creating a simple bash shell script
- A simple backup utility
  
#### File and filesystem
- Types of files
- Permission and access rights
- Changing permission and ownership
- umask
- chmod
- setuid

#### Linux Filesystems
- Filesystems and the VFS
- Mounting filesystems
- RAID and LVM
- Loopback filesystem

#### Compiling, Linking, and Libraries
- gcc and other compilers
- Major gcc options
- Static Libraries
- Shared Libraries
- Linking to libraries
- Debugging with gdb

#### Java Installation and Environment
- Package management
- RPM Creation
- The RPM spec file
- RPM dependencies
- Debian Package Creation Workflow
- Building RPM
- Building Debian package from source

# Linux Commands and Concepts
This file lists the fundamental concepts to grasp and important commands to operate Linux systems

Sources:
[Medium](https://medium.com/@linuxlearninghub/mastering-linux-the-ultimate-guide-to-becoming-a-linux-expert-44f9c4a8505a)

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

Transferring files and directories to a remote server as __root__ user
``` shell
scp [options] [source] [destination]
scp [options] [source_file] [username@remote_host:destination_directory]

#[options] with any desired flags or options (e.g., for custom SSH port or verbose output).
#[source_file] with the path and filename of the file you want to copy.
#[username] with your username on the remote server.
#[remote_host] with the IP address or hostname of the remote server.
#[destination_directory] with the path to the directory on the remote server where you want to place the file.

scp backup.tar.gz root@192.168.1.100:/home  # copy file backup.tar.gz from the present directory to another server's /home directory with default ssh port no. 22.
scp -P11208 backup.tar.gz root@192.923.223:/home # The server destination server with custom SSH port 11208.
scp -r documents root@192.923.223:/home          # copy the documents directory contents from your local server to the remote server having the IP address 192.923.223
scp -C documents root@192.923.223:/home           # Compress the files with -C flag over the network and restore to the original size at the destination.
scp -pv documents root@192.923.223:/home         # -p flag prints the estimated time and the connection speed and -v flag is used to print the debug information.

```

Copying Files from the Remote Server as __root__ user
``` shell
scp [options] [username@remote_host:source_file] [destination_directory]

scp root@192.168.1.100:/home/backup.tar.gz /home/download  # copy the file backup.tar.gz from the home directory of a remote server having IP address 192.168.1.100 to the /home/download directory of your local server/system
```
Copying files directly between two remote servers.
``` shell
scp user1@host1.com: backup.tar.gz user2@host2.com:/downloads # copy the backup file from remote host1 to the destination directory downloads of host2
```
You will be prompted to enter the password of both servers

### Rsync Command

-r Allows to sync data recursively inside a directory.
-z Used to compress data during transfers to save space.
–b Performs a backup during data synchronization.
-h Shows the numbers in the output in a human-readable format.
-e Instructs the rsync to use the SSH protocol for remote transfers.
–v Verbose output. Displays the details of the transfer.

Copy files/folders within our system
``` shell
rsync options source destination # syntax

#-r Allows to sync data recursively inside a directory.
#-z Used to compress data during transfers to save space.
#–b Performs a backup during data synchronization.
#-h Shows the numbers in the output in a human-readable format.
#-e Instructs the rsync to use the SSH protocol for remote transfers.
#–v Verbose output. Displays the details of the transfer.


rsync -v test.txt /home/rsync/ # copy the test.txt file in the CWD to the directory /home/rsync
rsync -avzh /home/linuxlearninghub/Download /home/linuxlearninghub/Documents # copy a directory from one location to another
```
Copy files/folders from one server to another
``` shell
rsync -avzhe ssh –progress /path/to/dir root@:/path/to/paste/dir
rsync -avzh /home/test.txt root@107.23.196.25:/home # copy files and directories from our local server to a remote server.
rsync -avzh root@107.23.196.25:/home/backupfolder /home/todaysbackup # copy files/folders from the remote server to our local server.
```
Copy files/folders from one server to another server using SSH
``` shell
rsync -avzhe ssh /home/test.txt root@107.23.196.25:/backup # Rsync over ssh to transfer files from our server to the remote server. -e flag for the secure transfer
rsync -avzhe ssh root@107.23.196.25:/home/sample.txt /home/files # Rsync over ssh to transfer files from a remote server to our local server.
rsync -avzhe ssh –progress /home/test.txt root@107.23.196.25:/backup # -progress flag to track the progress of this transfer.
```
Transfer files from one server to remote server using the custom ssh port and ssh key’s
``` shell
rsync -avzhe “ssh -i key.pem” –progress /home/backup.tar.gz ajay@172.168.10:/home/backup
# The key.pem is the server's private key with IP address 172.168.10 so it will match along with the respected public key in the server. The backup.tar.gz file present in the /home directory of the local server is transferred to the /home/backup folder of the remote server.

rsync -avzhe ‘ssh -p 5522’ –progress /home/folder.tar.gz root@94.150.78.214:/home/  # Rsync command used to transfer files using a custom ssh port. Here 5522 is the custom SSH port of the server with IP address 94.150.78.214 . Here we are transferring folder.tar.gz from the local server to the remote server’s home directory
```
## Network Commands
### Dig command
The “dig” command is a powerful tool for performing DNS queries in Linux. It is part of the dnsutils package and is used to look up the DNS records for a specific domain or host.

Using “dig,” you can retrieve information such as the IP address of a domain, the mail exchange (MX) records for a domain, or the name servers for a domain. You can also use “dig” to perform reverse DNS lookups, which can be useful for troubleshooting network issues or determining the hostname of a particular IP address.



``` shell
dig linuxlearninghub.com  # Linux command output with all data
dig linuxlearninghub.com +noall +answer

dig linuxlearninghub.com +short # Query A record of a domain
dig ns linuxlearninghub.com +short # Find the nameserver of a domain.
dig @ns1.nameserver.com linuxlearninghub.com +short # Specifying nameservers to query
dig mx google.com +short # Query the mx record of a domain
dig google.com ANY # Query all the DNS records of a domain
dig +trace google.com # Trace the dns resolution process
dig -x 8.8.8.8 +noall +answer # Reverse dns lookup

vi datafile.txt # This file contains the list of all domains that needed to be queried.
dig -f datafile.txt +noall +answer # Then run this command to get the IP address of all the domains listed in the data.txt file.
```

### Nslookup command
NSlookup (Name Server Lookup) acts as a tool to interact with DNS servers, allowing you to manually query them for information about domain names and IP addresses.
``` shell
sudo apt install dnsutils # install dnsutils that contains nslookup command
cat /etc/resolv.conf # to varify our dns server

nslookup [domain.com] # view domain name
nslookup 142.250.71.46 # Perform a reverse DNS lookup
nslookup -type=any [domain.com] # View full details of a domain name
nslookup -type=mx domain.com # View the MX record of a domain
nslookup -type=ns google.com # View Nameserver records of a domain
nslookup domain.com [nameserver] # View information about a specific nameserver
nslookup -debug [domain.com] # View debugging information

```

## Monitoring Network Connections and Open Files
## Netstat command
``` shell
sudo apt-get update
sudo apt-get install net-tools

netstat -i # Netstat command used to list all the network interfaces of a system.
netstat -a | more # Netstat command used to list all the listening and non-listening sockets of both TCP and UDP.
# If you want to list only the TCP ports use Netstat -at and if you want to display only the UDP ports use Netstat -au.

netstat -l # Netstat command used to list only the listening connections.
# similarly, if you want to view only the listening TCP ports use the command netstat -lt and for UDP ports use the command netstat -lu.

netstat -p # Netstat command used to display the process id of all connections.
# Also, you can use netstat -pt command to display the process id of all TCP connections and netstat -tu command to display the process id of all UDP connections.

netstat -pnltu # Netstat command used to display the list of networks, their current states, and their associated ports.
# Similarly you can use the grep command along with netstat command to see the status of a particular port and which service is listening on that port.
netstat -pnltu | grep 53

netstat -an | grep ‘LISTEN’ # How to use Netstat command to check all the listening connections.

Netstat -r # How to see the kernel routing table information using Netstat command.
```

## Lsof command
The lsof the command is used to list all open files on a Linux system. This can be useful for identifying which files are currently in use, and by which processes.

``` shell
lsof -u username # To List, all the files opened by a specific user.
lsof -u ^ username # List all the files opened by users except a particular user.
lsof -c chrome # To list files opened by a specific command
lsof -c ssh # to see all the opened files of program ssh.

lsof D /home/directoryname # To find all processes that are running in a specific directory
lsof /path/to/file # To find a process that has a particular file open
lsof -i TCP:80 # Finding a process that is listening on a particular port
lsof /dev/nvme0n1p5 # To list all open files of a specific device
lsof -m # Detecting and diagnosing memory leaks
lsof -p <PID> # To list the files opened by a specific PID
```

## Disk Management
``` shell
fdisk -l # List the current partitions available using fdisk
lsblk

sudo fdisk /dev/xvdf # Select the storage disk.
fdisk -l # Verify the partition’s created.

mkfs -t ext4 /dev/xvdf1 # Format the partitions with ext4 filesystem
mkfs -t ext4 /dev/xvdf2 # Format the partitions with ext4 filesystem

mount /dev/xvdf1 /home/ # Mounting the partitions.
mount /dev/xvdf2 /backup/ # Mounting the partitions.
```

## Network File system (NFS)
``` shell
# usecases
```





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

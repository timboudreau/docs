---
layout: page
title: An intro to the GUI
---

## Local State

Left side of the screen shows the local state (your device state). 

You can see a list of folders you have, and as you click on one folder name, it expands to show you about that folder.

Next to folder name, you can will see the state of the folder:  
 - **Unknown**  
 - **Unshared**  
You have not shared this folder  
 - **Stopped**  
 - **Scanning**  
    Scanning local folder files and seeking changes  
 - **Up to Date**  
    This device has the latest version of this folder. The folder is synchronized.  
    If it's a Folder Master, you will always see "Up to Date" and never "Syncing"  
 - **Syncing**  
    Synchronizing and downloading updates to the folder from a remote device  

In the information about each folder, you can see information about number of files and the size of folder:  
 - **Global State**  
    Show how many files there are between all devices in this folder.  
 - **Local State**  
    Show how many files this devices currently has. This represents the number of files that this device has downloaded locally for the given folder.  

> :warning: If this number is less than the one shown in Global State, that implies that this folder it's not yet synchronized or that you have defined **ignore patterns** and not all files for the given folder will be synchronized.

##Remote State

Bottom right side of the screen shows the overall state of the remote devices (overall completion percentage over all of the common folders between your device and the remote device.)

If a device has one or more folders to sync, you will see the message: Syncing and the remaining percentage.

> :warning: You can know when you are uploading files to one remote device by looking at the bandwidth numbers, but **you cannot know which folders are out of sync in each device**.

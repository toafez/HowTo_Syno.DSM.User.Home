English | [Deutsch](README.md) | [HowTo's: Table of contents](https://github.com/toafez/Tutorials/blob/main/README_en.md)

## HowTo: The User Home Service of Synology DiskStation Manager
[![Hits](https://hits.seeyoufarm.com/api/count/incr/badge.svg?url=https%3A%2F%2Fgithub.com%2Ftoafez%2FHowTo_Syno.DSM.User.Home%2Fblob%2Fmain%2FREADME_en.md&count_bg=%2379C83D&title_bg=%23555555&icon=&icon_color=%23E7E7E7&title=hits&edge_flat=false)](https://hits.seeyoufarm.com)

## What it is
The following guide describes the setup and features of the User Home service of DiskStation Manager, and the differences in how Synology handles home directories compared to other Linux operating systems.

#### _Note: Text in uppercase within square brackets is a placeholder and must be replaced with your own information, but may be used in some places for information purposes only. Please note that the square brackets are part of the placeholder and must also be removed when replacing with your own information._

## Introduction
Linux is designed as a **multi-user system** and is therefore optimised for use by multiple users. For this reason, each user is given a **personal directory** with full read and write permissions, in which they can store their own files and user-specific data and settings, **the so-called home directory**.

This home directory is usually located just below the root directory (/) and is called `/home`. Within the home directory there are directories named after the users created. For example, if a user's name is _tommes_, their home directory will be in the path `/home/tommes`. The short form `~/` is also often used in this context. The **tilde** (**~**) character used here is an abbreviation that always refers to the home directory `/home/[USERNAME]`. For example, if you wanted to change from anywhere in the file system to the `/home/[USERNAME]/Documents` directory, you could also use the command `cd ~/Documents`.

- #### An example
  After logging into the graphical user interface of any Linux operating system and running a terminal program, you will be redirected to your own home directory on the console (recognisable by the user name before the @ sign and the tilde character within the command prompt) `tommes@Linux-Client:~$`. By typing the command `pwd` (which stands for print working directory) you can display the path you are currently in.

  ```
  tommes@Linux-Client:~$ pwd
  /home/tommes
  ```
  The command `cd /` (cd stands for change directory) will change to the root directory of the file system. Type `pwd` again to confirm. The command prompt will also change, replacing the tilde with a forward slash / to indicate the root directory.

  ```
  tommes@Linux-Client:~$ cd /
  tommes@Linux-Client:/$ pwd
  /
  ```
  With the command `cd ~/` you switch back to your own home directory. This can also be confirmed with the command `pwd`.

  ```
  tommes@Linux-Client:/$ cd ~/
  tommes@Linux-Client:~$ pwd
  /home/tommes
  ```
## The User Home Service of Synology DiskStation Manager
With the DiskStation Manager, which is also based on Linux, **Synology** deviates from the structure of commonly used Linux home directories because the DiskStation Manager (DSM for short) has its own user management, which is initially independent of the Linux multi-user system described above, even though the same principles are applied in the background. Put simply, the default configuration of DSM does not create home directories as shown above, but these can be activated and used at any time via the DSM control panel.

The setup and use of the user home service is explained below, as well as the differences and special features of the `/home` and `/homes` folders, and the `/var/services/homes` and `/root` directories.

- Initial situation
  As mentioned at the beginning, the default configuration of DSM does not create any home directories. This is easy to check as there is no folder called **home** in **DSM** under **Control Panel > Shared Folders**. The folder is also not displayed in File Station on the left-hand side of the folder overview, and if you log in to the DSM console using a terminal program via SSH, you will be greeted with, among other things, the following message

  `Could not chdir to home directory /var/services/homes/[USERNAME]: No such file or directory`

  It was therefore not possible to change to the home directory of the currently logged in SSH user, as this directory does not exist. Interestingly, the message does not refer to the `/home/[USERNAME]` folder mentioned at the beginning, but to the `/var/services/homes/[USERNAME]` path. More on this later.

- Enabling the user home service
  To be able to use the home directories, the **User Home Service** must be enabled. To do so, log in to the DSM of your Synology NAS with an account belonging to the Administrators group. Then navigate to **DSM Main Menu > Control Panel > Users & Groups _(1)_** and switch to the **Advanced _(2)_** tab. Under the **User Base** menu item, select the **Enable user home service _(3)_** checkbox. Directly below this, you can select the **Location of homes _(4)_**, i.e. the volume on which the home directories of the users are to be stored. The default is `/volume1` and can be changed if more than one volume is available.

  ![01_DSM_User_and_Group](/images/01_DSM_User_and_Group_en.png)

  After enabling the user home service, a new **shared folder** named **homes** has been created on the previously specified volume and is now also displayed under **Control Panel > Shared Folder**.

  ![02_DSM_Shared_Folder](/images/02_DSM_Shared_Folder_en.png)

  This folder will also appear in File Station on the left side of the Folder Browser. If you right-click on the **homes _(1)_** folder and select **Properties _(2)_** from the context menu that appears, the path will be displayed in the **General _(3)_** tab under Location. In this example the path is `/volume1/homes`.

  ![03_DSM_FileStation_homes](/images/03_DSM_FileStation_homes_en.png)

  In addition, another folder named **home** appears in File Station just above the **homes** folder. Interestingly, this supposedly _shared_ folder does not appear in **Control Panel > Shared Folder**. If you right-click on the **home** folder in File Station on the left-hand side of the folder overview, you will notice that the Properties item is not available in the context menu that appears.

  ![04_DSM_FileStation_home](/images/04_DSM_FileStation_home_en.png)

  This may seem strange at first, so the difference between `/home` and `/homes` is explained below.

## The /homes folder
The **homes** folder or directory `/volume[x]/homes` is the counterpart to the usual Linux home directory `/home`. In the directory `/volume[x]/homes`, as usual under Linux, there are directories named after the users created in DSM. If you now log in to the DSM console with a terminal program via SSH, you will notice that the message 'Could not chdir to home directory /var/services/homes/[USERNAME]: No such file or directory' no longer appears, as you are now in the home directory of the logged-in user.

***Why*** is the console now in the home directory `/var/services/homes/[USERNAME]` and not in `/volume[x]/homes/[USERNAME]`? Now it gets a bit tricky for the inexperienced user, because the directory `/var/services/homes` is just a symbolic link to the directory `/volume[x]/homes`. So when you try to access the symbolic link `/var/services/homes/[USERNAME]`, you are actually accessing the path that the symbolic link points to, which is `/volume[x]/homes/[USERNAME]`. The reason for this is that in DSM the home directories are not located directly under the root directory (/) in the `/home` folder, as is usual under Linux, but on a freely selectable volume under `/volume[x]/homes`. The symbolic link ensures that all accesses to the home directory are redirected to the correct location on the console.

**To repeat:_** The home directories which are grouped under Linux by default are not located in `/home` as usual on a Synology NAS, but in `/volume[x]/homes`. To ensure that the reference to this folder is not lost on the DSM console, a symbolic link to this folder is created in the `/var/services/homes` directory. The symbolic link can be viewed on the console using the command `ls -l'.
```
root@SynologyNAS:/var/services# ls -l
total 0
lrwxrwxrwx+ 1 root root 14 Nov 26 17:00 homes -> /volume1/homes
```
#### _Note: All users belonging to the Administrators group will always have access to all users' personal folders. The **homes** folder is not visible to all other users and access to it is denied.

## The /home folder
If you are logged in to the DSM of your Synology NAS with an account belonging to the administrators group, the **home** folder will also be displayed in the File Station next to the **homes** folder, otherwise only the **home** folder will be displayed.

As mentioned above, if you right-click on the **home** folder in File Station on the left side of the Folder Browser, the Properties item will not appear in the context menu that appears. This is because the personal user folder **home** does not exist. Instead, a personal **virtual** and **temporary** folder called **home** is displayed for each user logged into DSM, which is actually located at `/volume[x]/homes/[USERNAME]` and points to it like a symbolic link. However, the **home** directory is the actual working directory for any user logged into DSM, including users in the Administrators group.

**_Note:_** Changes to the **homes** folder should only be made for administrative or backup and restore purposes, otherwise the personal **home** folder should be used instead.

## The /root folder
The superuser **root** has his own home directory outside the user home directories, which is located directly under the root directory (/) and is called `/root'. However, this directory is not accessible from the DSM GUI, but only via a terminal program that accesses the DSM console via an SSH or Telnet connection.

## One common feature remains - the tilde character (~)
Despite all the differences and adversities, the user can still quickly switch to the home directory by using the tilde character, with the difference that typing `cd ~/` on the DiskStation Manager console will take you to the `/var/services/homes/[USERNAME]` directory, not the `/home/[USERNAME]` folder. This does not make using the console unnecessarily difficult. After all!

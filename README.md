# Linux PAM 1.1.1 -- Opengear CM71xx support edition

#### Introduction
I've been working on properly configuring an Opengear CM71xx for deployment in a university lab setting. We required certain functionality that required specific Linux PAM modules. However, Opengear firmware currently does not come with the modules we need (specifically pam_exec).

I'm putting this here as a backup in case the lab owners need backups for the firmware I customized. However, I also have noticed that a few other people have had issues with compiling software properly for the CM71xx (more specifically, devices using uClinux/uClibc). While compiling for the CM71xx is not as difficult (as it comes with a CDK with its own maketools), it can still be fairly difficult for a beginner (as I am).

This version is intended to be used with the Opengear CM71xx CDK. You can put specific library files into the `romfs` folder and then use `make image` to create a firmware image to be uploaded to the device. Keep in mind that when you upload your new custom firmware to the device, you will have to use the `-i` option to upload firmware that is of the same version as the currently-used firmware.

I do not know if this matters or not, but I compiled these libraries for the CM71xx's **v4.2.0** firmware. The firmware for 4.3.0 is not available yet as 4.3.0 was only released recently.

Anyways, this version of PAM is intended to be used as a resource for modules that are compiled to work with the Opengear CM71xx. As such, you basically just have to pick and choose the modules you want to install. 

#### Installation
The files of your choosing in "modules" should be installed to `/lib/security` in the romfs folder. Additional config files are available and are listed in the correct directories as to where they should be installed into `romfs`.

The patches folder is an alternate option in case you want to compile your own version of PAM using the CDK. Keep in mind that you will need to use Linux-PAM 1.1.1, which is available through the Linux PAM website. On the CDK, you will need to create a folder and a Makefile (for autotools) inside of said folder. We will call this folder `pam`. In `pam`, add the following to the Makefile:

  ```
  URL = http://www.linux-pam.org/library/Linux-PAM-1.1.1.tar.gz
  include $(ROOTDIR)/tools/automake.inc
  ```  

In the `pam` folder (same directory as the Makefile), create a folder called `patches` and add all the files from the patches folder into that folder (do not forget the `series` file as this is how the CDK's autotools determines what patch files to use).

Then, return to the top directory of the CDK and run `make user/pam_only`. Everything should compile perfectly. You do not have to run `./configure` as the CDK will handle this process for you. You may need to run the make command again as it tends to fail the first time due to some text file. It is fine to run the compilation process again.

You should now have a installation folder containing the compiled files in `pam/build/Linux-PAM-1.1.1-install`. All the PAM files are contained there. 

**DO NOT USE THE ROMFS COMMAND FROM THE CDK'S MAKETOOLS**. You do not want to overwrite the pre-existing libpam libraries. This will cause issues with the CM and will cause it to stop functioning entirely (you will need to netboot it; I would know, I have bricked my CM71xx many times through experimentation). 

#### Notes
This version of PAM does not come with pam_limit, pam_pwhistory, or pam_timestamp. These do not compile at all with uClibc. 

The patches specifically 

As an additional note, if you are to compile the files yourself, it *must* be compiled with `pam_adduser`. The files for this are in the "src" folder. The patches included will add a folder for `pam_adduser` and will look for the makefile for it. If that makefile isn't present, PAM's compilation will fail. All you have to do is take the .c and makefile in the pam_adduser folder and move them to the pam_adduser folder at `Linux-PAM-1.1.1/modules/pam_adduser`.

The source code for pam_adduser was provided to me by Opengear. We needed it to be modified to prevent it from removing RADIUS-authenticated users from a group if they were already logged in with another group. 

Please keep in mind that I **have not tested the functionality of these modules, except for pam_exec and pam_env**. I do not know how any of the other modules interact with the pre-existing PAM libraries on the device. As I said earlier, *do not replace the pre-existing libraries in the romfs folder in the CDK*. This *especially* includes the libpam libraries.

`pam_group` may not work, as pam_adduser provides group management functionality on the device. I do not recommend removing pam_adduser as a workaround, as this will prevent you from logging into the CLI (pam_adduser is very engrained in the functionality of the device, unfortunately). However, if you do intend on using pam_group instead of pam_user, I have provided the .c file for pam_adduser. That being said, pam_adduser is very deeply entangled in the device's framework, so I would avoid using pam_group unless absolutely necessary. pam_adduser can be modified and compiled and work perfectly fine, so I almost consider pam_adduser a "replacement" for pam_group on this device.

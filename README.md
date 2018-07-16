### Linux PAM 1.1.1 -- Opengear CM71xx support edition ###

I've been working on properly configuring an Opengear CM71xx for deployment in a university lab setting. We required certain functionality that required specific Linux PAM modules. However, Opengear firmware currently does not come with the modules we need (specifically pam_exec).

I'm putting this here as a backup in case the lab owners need backups for the firmware I customized.

This version is intended to be used with the Opengear CM71xx CDK. Do not use the DIRECTORY_romfs command that the CDK tells you to use with this version of PAM. This version of PAM is intended to be used as a resource for modules that are compiled to work with the Opengear CM71xx. As such, you basically just have to pick and choose the modules you want to install.

The files in modules should be installed to /lib/security in the romfs folder. Additional config files are available and are listed in the correct directories as to where they should be installed.

The patches folder is an alternate option in case you want to compile your own version of PAM using the CDK. Keep in mind that you will need to use PAM 1.1.1. On the CDK, you will need to create a folder and a Makefile (for autotools). In that folder, create a file called "Makefile" and add the following to it:

  ```
  URL = http://www.linux-pam.org/library/Linux-PAM-1.1.1.tar.gz
  include $(ROOTDIR)/tools/automake.inc
  ```  

In the same directory as Makefile, create a folder called "patches" and add all the files from the patches folder into that folder.

Then, return to the top directory of the CDK and run "make user/(foldername)_only". Everything should compile perfectly. You may need to run the make command again as it tends to fail the first time due to some text file. It is fine to run the compilation process again.

You should get a installation folder in (foldername)/build/Linux-PAM-1.1.1-install. All the PAM files are contained there. 

DO NOT USE THE ROMFS FUNCTION IN THE CDK'S CUSTOM MAKE. You do not want to overwrite the pre-existing libpam libraries. This will cause issues with the CM and will stop it from functioning properly. 

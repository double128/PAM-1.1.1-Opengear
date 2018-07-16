# Linux PAM 1.1.1 -- Opengear CM71xx support edition

I've been working on properly configuring an Opengear CM71xx for deployment in a university lab setting. We required certain functionality that required specific Linux PAM modules. However, Opengear firmware currently does not come with the modules we need (specifically pam_exec).

I'm putting this here as a backup in case the lab owners need backups for the firmware I customized.

This version is intended to be used with the Opengear CM71xx CDK. Do not use the DIRECTORY_romfs command that the CDK tells you to use with this version of PAM. This version of PAM is intended to be used as a resource for modules that are compiled to work with the Opengear CM71xx. As such, you basically just have to pick and choose the modules you want to install.

The files are already in the correct folders and should be installed to the same directory in the romfs file.

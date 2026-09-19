# TunaOS Installer Walkthrough

TunaOS has a GUI installer that is easy to understand. It makes it as easy as it can be to put a bootc operating system on bare metal or on a virtual machine.

Below are step-by-step visual guides of the installation flow for both the standard GNOME/XFCE variant and the Cosmic Desktop variant.

> A workflow makes these images; no person takes them. Every Monday, the
> [Installer Walkthrough Screenshots](../.github/workflows/installer-screenshots.yml)
> workflow boots a new live ISO in QEMU, drives the installer with
> `scripts/run-walkthrough.sh`, and commits the screendumps here. If a
> screenshot looks old or wrong, dispatch that workflow to make new ones. You
> can also run the script on your own machine against any ISO you built.

## GNOME / XFCE Installer Flow

This carousel shows each step of an installation of the standard TunaOS desktop:

````carousel
### 1. Welcome Screen
The installer welcomes the user and prompts them to begin the setup.
![01_welcome](images/installer/01_welcome.png)
<!-- slide -->
### 2. Disk Selection
Select the target disk drive where TunaOS will be installed.
![02_disk_select](images/installer/02_disk_select.png)
<!-- slide -->
### 3. Installation Confirmation
Confirm the settings and the target disk before the installer formats it.
![03_confirm](images/installer/03_confirm.png)
<!-- slide -->
### 4. Setup Initiated
The installer starts to prepare the partitions and the file system.
![04_installing](images/installer/04_installing.png)
<!-- slide -->
### 5. Installing Packages
The installer copies the system files and the bootc chunks to the disk.
![05_installing_progress](images/installer/05_installing_progress.png)
<!-- slide -->
### 6. Installation Complete
The installation has finished successfully. Reboot to start using TunaOS!
![06_done](images/installer/06_done.png)
````

---

## Cosmic Desktop Installer Flow

This carousel shows the installation flow customized for the Cosmic desktop:

````carousel
### 1. Welcome Screen
The Cosmic-themed welcome screen.
![cosmic_01_welcome](images/installer/cosmic_01_welcome.png)
<!-- slide -->
### 2. Disk Selection
Select the destination drive in the Cosmic installer.
![cosmic_02_disk_select](images/installer/cosmic_02_disk_select.png)
<!-- slide -->
### 3. Installation Confirmation
Review partition layout and confirm deployment.
![cosmic_03_confirm](images/installer/cosmic_03_confirm.png)
<!-- slide -->
### 4. Setup Initiated
Cosmic installer prepares the block devices.
![cosmic_04_installing](images/installer/cosmic_04_installing.png)
<!-- slide -->
### 5. Deployment Progress
The installer writes the Cosmic system image and the bootloader configuration.
![cosmic_05_installing_progress](images/installer/cosmic_05_installing_progress.png)
<!-- slide -->
### 6. Finished
Installation completes successfully. Ready for the first boot!
![cosmic_06_done](images/installer/cosmic_06_done.png)
````

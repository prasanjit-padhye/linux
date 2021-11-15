Assignment 1

Prasanjit Padhye (SJSU ID: 015227771)



Steps involved in completing the assignment:

•	Download and install VMware Fusion.

•	Download ubuntu iso file.

•	Create a ubuntu VM on VMware workstation.

•	Setup Ubuntu selecting 200 GB of space and 5GB of memory.

•	Go into settings of the VM, in the processor section, check the checkboxes under Advanced Options.

•	Fork the original linux repository from torvalds/linux into your Github account.

•	In the ubuntu VM terminal run the command cat /proc/cpuinfo to check whether vmx flags are present and virtualization capabilities are present for the VM.

•	Install git on the VM by using the command "sudo apt install git-all".

•	Clone the repository which we forked earlier from torvalds/linux on your machine.

•	Run cd linux to switch to the repo which we just cloned.

•	In the cmpe283-1.c file downloaded from Canvas add the sections of code for primary procbased controls, secondary procbased controls, entry and exit controls after referring to the SDM volume 3 module 23.6.x and 23.7 and 23.8.

•	Add the modified cmpe283-1.c file and the Makefile to a folder CMPE283 and put the folder under the cloned linux directory.

•	Switch to the linux directory.

•	Run "uname -a" command to check the version (check if there is a version mismatch between the cloned version and the current VM linux distribution we are running).

•	To run certain make commands, we need to run the install command "sudo apt-get install libncurses-dev flex bison openssl libssl-dev dkms libelf-dev libudev-dev libpci-dev libiberty-dev autoconf".

•	Follow the following sequence of commands to build and install the kernel modules:

1.	"make menuconfig" (do not save any changes in the UI and exit from it).
2.	"cp /boot/config- {current version we got from "uname -a" command without {} braces} .config".
3.	"make oldconfig" (press enter to answer all the questions).
4.	"make prepare".
5.	"make -j {no. of cores} modules" e.g.: "make -j 6 modules"
6.	If there is an error like "*** No rule to make target 'debian/canonical-certs.pem'", vi the .config file and set the system TRUSTED_KEYS and REVOCATION_KEYS strings = "".
7.	After the "make -j 6 modules" command is successfully finished run "make -j 4".
8.	After the command is successfully executed we will see a "is ready" statement.
9.	"sudo make INSTALL_MOD_STRIP=1 modules_install".
10.	"sudo make install".
11.	"sudo reboot".
12.	Run "uname -a" to check whether the version has changed (updated to our new complied module).
13.	Change directory to the directory we created earlier "cd CMPE283"
14.	Run "make" command check if there are any errors in the file or an error asking to include license. In case of an error for license, go to the cmpe283-1.c file and at the bottom add "MODULE_LICENSE ("GPL v2");" and save the file.
15.	After the make command is successfully executed, check that the .ko file is created using "lsmod | grep CMPE283".
16.	"sudo insmod cmpe283-1.ko" to insert our module.
17.	Run "dmesg" command to check whether we can see the capability info for all the MSR which we have defined in the cmpe283-1.c file under the heading "CMPE 283 Assignment 1 Module Start".
18.	To remove the module run "sudo rmmod cmpe283-1".
19.	Run "dmesg" to check the exit message ""CMPE 283 Assignment 1 Module Exits".

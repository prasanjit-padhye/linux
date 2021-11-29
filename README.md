CMPE 283 Assignment 1

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

-------------------------------------------------------------------------------------------------------------------------------------------------------------------

Assignment 2

Prasanjit Padhye
SJSU ID: 015227771

Steps to Complete Assignment 2:

•	Start with Assignment 1 setup.

•	Modify cupid.c and vmx.c as follows:

o	Return total number of exits in eax register for the leaf node for eax values as 0x4fffffff.

o	Return number of exits in eax for exit type provided in ecx when leaf node value is 0x4ffffffd.

o	We can find the modified values for cupid.c and vmx.c under arch/x86/kvm/cupid.c and arch/ x86/kvm/vmx.c respectively.

o	We declare 2 variables (extern) in vmx.c under arch/ x86/kvm/vmx.c.

o	Increment total exits each time there is an exit, increment its corresponding value for the reasons index in the array.

o	Save the modification made in both files.

o	Run following sequence of commands:
1.	make -j 6 modules
2.	make INSTALL_MOD_STRIP=1 modules_install && make install
3.	lsmod | grep kvm – to check whether module is already loaded
4.	If the module is already loaded, run ‘rmmod kvm_intel’ and rmmod kvm
5.	modprobe kvm

o	To test the modifications, we install a VM inside our VM.

o	Install Virtual Machine Manager from Ubuntu Software Center.

o	Download the Ubuntu .iso file and create Ubuntu VM following the same steps as in Assignment 1.

o	Once VM is created, install cupid package using the command ‘sudo apt-get install cpuid’.

o	We use the command ‘cpuid -l 0x4fffffff’ to check the total exits returned in eax register.

<img width="1792" alt="Screen Shot 2021-11-22 at 9 36 04 PM" src="https://user-images.githubusercontent.com/91384351/142980666-a85e24a9-fde3-48f4-8197-dd501d1ef253.png">


o	We use the command ‘cpuid -l 0x4ffffffd -s {exit_code}’ (e.g.: cpuid -l 0x4ffffffd -s 71) for which we want to check the number of exits. 

<img width="1792" alt="Screen Shot 2021-11-22 at 9 38 38 PM" src="https://user-images.githubusercontent.com/91384351/142980689-46d33507-7fe8-4e18-9117-fa989f38c13a.png">

Answers for 0x4ffffffd:

o	Increase in total number of exits is not stable. It is 420 at times and other times there is an increase of 670-700. Exits for MSR access, EPT violations, IO     Instructions are observed frequently. For a VM Reboot, around 1174500 exits were observed.

o	Most frequent exits are MSR access and EPT violations, least frequent exits are 0 (Triple Fault, VMWRITE, ets.), very few exits for DR_Access, APIC Access.


-------------------------------------------------------------------------------------------------------------------------------------------------------------------
Assignment 3

Prasanjit Padhye

SJSU ID: 015227771

Steps to Complete Assignment 3:

o	Start with Assignment 2 setup.

o	Modify cpuid.c file and vmx.c file as follows:

  -	When the leafnode eax value is 0x4FFFFFFE,return the high 32 bits of the total time spent processing all exits in %ebx and the low 32 bits of the total time     spent processing all exits in %ecx. We achieve this by declaring a variable to store the total time and adding the time spent processing each exit to this       total time.

  -	When the leafnode eax value is 0x4FFFFFFC and there is an exit number provided in ecx as input, return time spent processing that particular exit, return         high 32 bits of the total time spent for that exit in %ebx and the low 32 bits of the total time spent for that exit in %ecx. We achieve this by                 declaring an array for all the exits and storing time spent for each exit in the corresponding array element.

o	Save the modifications made in both the files.

o	Run the following sequence of commands:

  -	make -j 4 modules

  -	make INSTALL_MOD_STRIP=1 modules_install && make install

  -	run "lsmod | grep kvm" to see if the module is already loaded

  -	if the comamnd returns that the module is already loaded run "rmmod kvm" and "rmmod kvm_intel" if both are present, run "modprobe kvm"

o	Now in order to test the modifications we made we open the nested VM.

o	In the terminal we use the commands "cpuid -l 0x4FFFFFFE" to see the total time spent processing all exits in ebx and ecx.

o	We use the command "cpuid -l 0x4FFFFFFC -s {exit_reason}" to see the total time spent processing the exit provided in {exit_reason}.



o	Questions for 0x4fffffffc :

1. Increase in the number of total exits is not stable. We notice a very high number of exits for MSR access.For a full VM reboot we noted around 1174550            exits.

2. Most frequent exits are EPT violations and MSR access and the least frequent exits are 0 (for VMWRITE, Triple Fault, etc.) and there are few exits for            DR_Access,APIC access.

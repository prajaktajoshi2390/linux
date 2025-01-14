Linux kernel
============

There are several guides for kernel developers and users. These guides can
be rendered in a number of formats, like HTML and PDF. Please read
Documentation/admin-guide/README.rst first.

In order to build the documentation, use ``make htmldocs`` or
``make pdfdocs``.  The formatted documentation can also be read online at:

    https://www.kernel.org/doc/html/latest/

There are various text files in the Documentation/ subdirectory,
several of them using the Restructured Text markup notation.

Please read the Documentation/process/changes.rst file, as it contains the
requirements for building and running the kernel, and information about
the problems which may result by upgrading your kernel.


# 283-assignment3
Q1: For each member in your team, provide 1 paragraph detailing what parts of the lab that member
implemented / researched.

- This assignment is implemented by 2 members:
  -- Prajakta Joshi
  -- Umashankar Kumar
- Activities By Prajakta Joshi
  -- Done the setup required for the assignment
  -- Implemented code to find out exit reasons not defined by SDM
  -- Implemented cheanges related to cpuid.c to set conditions on leaf node 0x4FFFFFFE for different functionalities
- Activities By Umashankar Kumar
  -- Done the setup required for the assignment
  -- Implemented code to in vmx.c to set different exit reasons
  -- Created Readme document
- Both the team members worked together for the setup issues
- While integration, both of the team members worked together by setting up the zoom call
- Code has been developed by both of the team members together and committed by Prajakta onto the GitHub Repo


Q2: Describe in detail the steps you used to complete the assignment.

As part of assignment, first complete the setup as done in Assignment 2, the steps executed are as follows:
- Create fork branch of torvalds / linux
- On the VMWare Terminal, get the clone of the forked branch created above
- Go Inside linux folder
- Execute following commands:
-- sudo bash
-- apt-get install build-essential kernel-package fakeroot libncurses5-dev libssl-dev ccache bison flex libelf-dev
-- uname -a  ---// Note down the kernel version e.g. 5.4.0-52-generic
-- cp /boot/config-5.4.0-52-generic    ./.config  ---// Substitute the version as you obtained
- make oldconfig
- make && make modules && make install && make modules_install 
- reboot
-- If you face problems after reboot, execute following commands
-- reboot -f
-- Select advance option
-- Select the newly added kernel version
-- You will be able to login after above implementation
-- Open terminal and goto linux folder
-- sudo bash
-- nproc
-- sudo make -j 1 && sudo make modules_install -j 1 && sudo make install -j 1  // If the output of nproc is 2, replace all 1's with 2
-- update-grub 
-- reboot
- sudo apt-get install qemu-kvm libvirt-bin bridge-utils virt-manager
- sudo apt-get install virt-manager
- Now Create inner VM using virt manager application
- Give 2 CPU and 3000MB space to inner VM
- Once inner VM is created install cpuid for quick testing
- sudo apt install cpuid
- On the outer VM, make following changes:
-- Go to linux/include/linux/kvm_host.h and declare the struct for vcpu
-- Go to linux/arch/x86/kvm/vmx/vmx.c and make required changes into vmx_handle_exit function
-- Go to linux/arch/x86/kvm/cpuid.c and make required changes into kvm_emulate_cpuid function
-- Save all the files
- make && make modules && make install && make modules_install
- reboot
- Goto Inner VM and execute command as cpuid -l 0x4FFFFFFF // This will give updated register values of eax, ebx, ecx, edx
- Go to Outer VM Terminal and execute dmesg, you will see the logs with exit count and cycles
- Commit and Push the changes to the Git Repo

- Till this point, Assignment 2 is in working state
- Go to linux/arch/x86/kvm/vmx/vmx.c and make required changes into vmx_handle_exit function and into different exit handeling methods
- Go to linux/arch/x86/kvm/cpuid.c and make required changes into kvm_emulate_cpuid function
- make && make modules && make install && make modules_install
- reboot
- Goto Inner VM and execute different commands to test register values at different conditions
- Go to Outer VM Terminal and execute dmesg, you will see the logs with exit number and exit count
- Commit and Push the changes to the Git Repo

Please find below the observation for different scenarios:
1. Register Information for Exit Number 1 and Exit Number 7
![Picture1](https://raw.githubusercontent.com/prajaktajoshi2390/linux/master/screenshots/Picture1.png)

2. Number of Exits for Exit 1 and Exit 7
![Picture2](https://raw.githubusercontent.com/prajaktajoshi2390/linux/master/screenshots/Picture2.png)

3. Register Information for Exit Number 35, 38 and 69
![Picture3](https://raw.githubusercontent.com/prajaktajoshi2390/linux/master/screenshots/Picture3.png)

4. Number of Exits for Exit 35, 28 and 69
![Picture4](https://raw.githubusercontent.com/prajaktajoshi2390/linux/master/screenshots/Picture4.png)

5. Register Information for Exit Number 3 and 4
![Picture5](https://raw.githubusercontent.com/prajaktajoshi2390/linux/master/screenshots/Picture5.png)

6. Number of Exits for Exit 3 and 4
![Picture6](https://raw.githubusercontent.com/prajaktajoshi2390/linux/master/screenshots/Picture6.png)


Q3: Comment on the frequency of exits – does the number of exits increase at a stable rate? Or are there more exits performed during certain VM operations? Approximately how many exits does a full VM boot entail?
Find below the observation:
[  122.281317]  total number of exits CPU0: 486892, total number of cycles: 13820529
[  122.281568]  total number of exits CPU1: 305823, total number of cycles: 10413782

[  318.926948]  total number of exits CPU0: 513998, total number of cycles: 16907289
[  318.927307]  total number of exits CPU1: 327909, total number of cycles: 12937032

[  423.375499]  total number of exits CPU0: 524251, total number of cycles: 18633435
[  423.375887]  total number of exits CPU1: 335247, total number of cycles: 14044814

Register Information Observation:
CPU 0:
   0x4fffffff 0x00: eax=0x00076dec ebx=0x00000000 ecx=0x00d2e271 edx=0xffff99ab
CPU 1:
   0x4fffffff 0x00: eax=0x0004aa9f ebx=0x00000000 ecx=0x009ee6d6 edx=0xffff99ab

CPU 0:
   0x4fffffff 0x00: eax=0x0007d7ce ebx=0x00000000 ecx=0x0101fc19 edx=0xffff99ab
CPU 1:
   0x4fffffff 0x00: eax=0x000500e5 ebx=0x00000000 ecx=0x00c56748 edx=0xffff99ab

CPU 0:
   0x4fffffff 0x00: eax=0x0007ffdb ebx=0x00000000 ecx=0x011c52db edx=0xffff99ab
CPU 1:
   0x4fffffff 0x00: eax=0x00051d8f ebx=0x00000000 ecx=0x00d64e8e edx=0xffff99ab

It is observed that exits are increasing at a stable rate. 
Total Exits with VM boot, for CPU0: 486892, CPU1: 305823

Q4: Of the exit types defined in the SDM, which are the most frequent? Least?
According to test results, we can figure out following:
- Few exits that are frequent are External Interrupt, EPT Violation and WRMSR
- Least Frequent Exits are: MOV DR, XSETBV.
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


# 283-assignment4
Q1: For each member in your team, provide 1 paragraph detailing what parts of the lab that member
implemented / researched.

- This assignment is implemented by 2 members:
  -- Prajakta Joshi
  -- Umashankar Kumar
- All the activities are for Assignment 4 are done by both the team members together
- While integration, both of the team members worked together by setting up the zoom call
- Both the team members did setup of repo, developed and worked on testing scenarios
- Documentation is created by Prajakta Joshi

Q2. Include a sample of your print of exit count output from dmesg from “with ept” and “without ept”.
- Code repo and setup is completed till Assignment No. 3
- Shudown the Inner VM and reboot outer VM
- Test the exit scenario with Exit Number and Exit Count inside Inner VM
- Following Output was obtained on dmesg with EPT
![Picture7](https://raw.githubusercontent.com/prajaktajoshi2390/linux/master/screenshots/Picture7.png)

- On the outer VM, execute following commands:
- rmmod kvm-intel
- insmod /lib/modules/5.9.0+/kernel/arch/x86/kvm/kvm-intel.ko ept=0 // Use the version of kernel that was built
![Picture8](https://raw.githubusercontent.com/prajaktajoshi2390/linux/master/screenshots/Picture8.png)

- After setting ept=0, reboot the system
- On inner VM perform the exit test with same exit number
- On outer VM following output was obtained after setting EPT as 0
![Picture9](https://raw.githubusercontent.com/prajaktajoshi2390/linux/master/screenshots/Picture9.png)

Q3. What did you learn from the count of exits? Was the count what you expected? If not, why not?
- It is observed that exit count increased when EPT set as 0. The count is generated as expected.
- In nested paging, we have 2 tables to navigate. One, table maintained by VM itself for converting GVA to GPA.
  -- And another one is maintained by VMM, for converting GPA to HPA
  -- TLB gets updated as an when there is a successful entry found in any of the table
  -- This paging technology uses 2 table navigation and involves complex memory management. 
  -- But number of exits required and handled in nested paging are far less than that of shadow paging and same we have observed over here
- In shadow paging, there is a concept of shadow page table. CR3 points to the shadow page table maintained by VMMM.
  -- VM adds the entries of address translation to inactive page table having no idea about shadow page table.
  -- Shadow page table is marked empty at the start, on the instruction having memory involvement, will search for address in shadow page table.
  -- Due to no entry found, it will generate page fault. Page fault exit is enebled in shadow page table, control will come to VMM with VM exit and by walking through inactive page table, VMM will convert the the guest generated physical address to host physical address
  -- It will add corresponding entry to TLB and shadow page table.
  -- Along with enabling exit on page fault, shadow paging will require enabling exit on CR3 load, TLB flushing, CR3 read etc.
  -- There are lot of exits that are need to be handled in shadow paging comapred to nested paging.
- Hence while navigating from nested paging to shadow paging, we observed that the exit count increased.

Q4. What changed between the two runs (ept vs no-ept)?
- We observed that when we moved from ept to no ept, due to shadow paging, exit count increased.


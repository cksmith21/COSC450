### Memory Management 
- **Ideal Memory**: infinitely large, fast, non-volatile 
- **Memory Hierarchy**: 
	- Registers
	- Cache
	- RAM
	- Hard disks

#### Mono-process
- Only memory sharing is between a user program and the OS
- Only one program can be loaded into memory 
- Long term scheduler chooses job from pool of jobs to be loaded into memory

#### Multi-process
- **Fixed partition**: memory is divided into $n$ partitions of possibly unequal size
	- **Separate input queues**: when job arrives, can be put into smallest queue large enough for it 
		- Issue comes from large queues being empty while small jobs are waiting for smaller queues 
	- **Single input queue**: closest queue that fits in it is chosen and loaded into the empty partition
		- Search whole queue and find out what job is a best fit (discriminates against small jobs) --> solution is to create a partition for small jobs and keep a counter to make sure that small jobs aren't skipped too often![[Screenshot 2023-03-28 at 19.52.24.png]]

#### Modeling Multiprogramming 
- **Probablistic model**: let $p$ be the fraction of time a process is waiting for I/O. If there are $n$ processes, the probability that all processes are waiting for I/O is $p^n$. 
	- **CPU Utilization** = 1- $p^n$ 
- EXAMPLE: 
	A computer has 32MB of memory, the OS takes up 16MB and each program takes up 4MB. 
	So, with a $p$ = 80%, you have about 60% = $1-0.8^4$ of CPU utitlization. 

#### Ways of Memory Management 
- **Swapping**: bring each process in its entirety, running it for a while, then putting it back on disk
	- **Fixed Partition**: the number, location, and size of partitions is fixed. OS knows the address of each process, simple to manage the memory, and the internal fragmentation wastes space. 
	- **Variable Partition**: number, location, and size of partitions vary dynamically based on the size of the process; OS needs to keep track of partition information dynamically for allocation/deallocvation. Can create multiple holes --> solution can be to combine them into one big hole. 
- ![[Screenshot 2023-03-28 at 20.02.22.png]]
	- How much memory should be allocated for a process when it is created or swapped? 
		- Fixed partition: simple, base off that size
		- Dynamic allocation: using the heap/recursion; you have to allocate extra memory for each process, use adjacent hole to manage growing size. Processes may be swapped out or killed. 
- ![[Screenshot 2023-03-28 at 20.04.15.png]]

### Free Memory Space Management 
- **Bitmap**: memory is divided into allocation units (*words* that are $k$ bytes). 
	- Corresponding to each of the allocation units is a bit in the bitmap that show whether the unit is free (0) or occupied (1).
	- Size of allocation unit:
		- Smaller allocation = larger bitmap 
		- Large allocation = smaller bitmap, memory may be wasted in the last unit of the process if the allocation units are not a multiple of the process size 
	- **Advantages**: simple way to keep track of words in a fixed amount of memory since size of bitmap depends on the size of memory and the size of allocation units
	- **Disadvantages**: to allocate a process withh $k$ unit size, the memory manager has to search for $k$ consecutive 0 bits in the map
- **Free list**: OS maintains a linked list of allocated and free memory segments
	- Each entry has 4 bits: hole or process, starting address, length, and a pointer to the next entry
	- If the segments are sorted by address, then it is simple to update the list when a process terminates or is swapped out of memory
	- ![[Screenshot 2023-03-28 at 20.36.27.png]]
- EXAMPLE
128MB of memory is allocated in units of 2KB. For the linked list, the memory consists of alternating sequences of segements of processes and holes, each 64KB. Each node in the linked list contains a 32 bit address, 16 bit length, and a 16 bit next node. 

**Bitmap** 

*# of allocation units* = $128MB/2KB$ = $(128 * 2^{20})/(2*2^{10}) = 2^{27} * 2^{11} = 2^{16}$ 
*size of bitmap* = $2^{16}$ bits = $2^{13}$ bytes

**Free list**

*# number of nodes in linked list* = $128MB/64KB$ = $2^{27}/2^{16}$ = $2^{11}$ nodes
*size of each node* = 32+16+16 = 64 bits = 8 bytes = $2^3$ bytes
*total size of linked list* = **number of nodes * size of a node** = $2^{11} * 2^3$ = $2^{14}$ bytes

- Algorithms for memory space allocation with free-list:
	- **First fit**: scans list of segments from the beginning until it finds a whole that is big enough
	- **Next fit**: starts searching the list from where it left off last time
	- **Best fit**: searches entire list and takes the smallest hole
	- **Worst fit**: always takes largest free hole
- Can speed up the algorithms by maintaining two lists, one for the holes that is sorted by size and the other for processes
	- **Quick fit**: takes the separate list for holes which might have a table with $n$ entries, where each entry is a pointer to a head of a certain size ($4KB$ holes, etc.) which means it is quick to find the hole of a required size but it is expensive. 

### Virtual Memory 

- **Memory sizes in bytes**
	- 1 byte = 8 bits
	- 1 KB = $2^{10}$ bytes
	- 1 MB = $2^{20}$ bytes
	- 1 GB = $2^{30}$ bytes
	- 1 TB = $2^{40}$ bytes
	- 1 PB = $2^{50}$ bytes
	- 1 EB = $2^{60}$ bytes
	- 1 ZB = $2^{70}$ bytes
	- 1 YB = $2^{80}$ bytes

- **Motivation**: the entire logical address space of a process must be in physical memory before the process can access, but since the size of a program grows and physical memory is limited, it is not always possible
- Virtual memory is a technique that allows the execution of processes that may not be entirely in memory
	- OS needs to keep track of the parts of the program currently located in main memory and the rest of the disk
	- Can be used in multiprogramming
- Approaches: 
	- Paging
	- Segmentation
	- Segmentation with paging

#### Paging 
- **Pages**: units of the virtual address space
	- Size is usually between $512$ bytes and $64KB$
- **Page frames**: corresponding units in physical memory 
- Page frame size and page size must be thhe same so you can calculate the physical address from the virtual address
- How to map virtual address to physical memory address: 
	1. PC saves the address of the next instruction (virtual address in virtual space)
	2. Virtual address goes into the memory management unit (MMU) that maps the virtual address onto the physical memory addresses based on the memory map
![[Screenshot 2023-03-28 at 20.58.03.png]]![[Screenshot 2023-03-28 at 20.58.33.png]]
- **Finding number of pages:** possible virtual space/page size
- **Finding number of page frames:** size of memory/page size
- EXAMPLE

The computer generates a 16-bit address, so the system supports a 64KB virtual space since: 0 ~ $2^{16}-1$ = 0 ~ $64*2^{10}$ . The page size is 4KB and the computer has 32KB memory. 

*Number of pages:* 16 = (64KB/4KB)
*Number of page frames:* 8 = (32KB/4KB)

- EXAMPLE

The computer generates a 32-bute address, so the system supports a 4GB virtual space since: 0 ~ $2^{32}-1$ = $4*2^{30}$ . The page size is 4KB and the computer has 2GB of RAM. 

*Number of pages:* 4GB/4KB = ($4*2^{30}$)/($4*2^{10}$) = $2^{20}$ pages 
*Number of page frames:* 2GB/4KB = ($2*2^{30}$)/($4*2^{10}$) = $2^{19}$ page frames \

- EXAMPLE 

![[Pasted image 20230328221523.png]]

MOV REG, 8900;; 

The virtual address 8900 is sent to the MMU. The MMU then calculates the address 8900 belongs to virtual page 2 (8192 ~ 12288). The MMU then checks the memory map such that virtual page 2 maps to physical frame 6 (24576 ~ 28672). 

The MMU then sends the address 24576 + (8900 - 8192) = 25287. This is the physical address. 

**Calculating physical address** = beginning range of physical frame + (location provided - beginning range of virtual frame)

##### Physical Address Mapping 
- How to map virtual address into physical address by the MMU
- EXAMPLE

The computer generates a 16-bit address, with a page size of 4KB. The computer has 32KB of RAM. There are 16 pages and 8 page frames. 

The incoming 16 bit address is split into 4-bit page numbers, since there are 16 pages and a 12-bite offset, since the page size is 4KB. 

The first 4-bits are used as an index into the page table (since $2^4$ = 16). With the 12-bit offset, you can address $2^{12}$ = 4KB within a page.

![[Pasted image 20230328223036.png]]

The first 4 incoming bits are indexed into the table. In the table, there is a value that then becomes the first 4 bits of the outgoing (physical) address. The 12 bits of the original address are then appended to the new first 4 bits. 

- EXAMPLE 

System generates a 32-bit virtual address with a memory size of 1GB and a page size of 4KB. 

Size of the virtual space = $2^{32}$
Number of pages = $2^{32}$/($4*2^{10}$) = $2^{20}$ so the page table has $2^{20}$ entries. 
Number of page frames = ($2^{30}$)/($4*2^{10}$) = $2^{18}$

For example, if the system generates a virtual address 10101111100010101110111111001111, the first 20 bits are used for the index of the page table, and the remaining 12 bits (page size  = 4KB = $2^{12}$) will be used for the offset. Because there are $2^{18}$ page frames, 18 bits must be reserved for saving page frame number in the page table entry. 

![[Pasted image 20230328224229.png]]

#### Page Table
- The purpose of the page table is to map virtual pages onto page frame
- Two major issues: 
	- Page table can be extremely large
	- The mapping must be fast
		- Building a single table consisting of an array of fast, hardware registers, with one entry for each virtual page, indexed by virtual page numbers can be very expensive. 
		- The page table can be entirely in main memory, all of the hardware needs is a single register that points to the start of the page table, but you need one or more memory references to read page table entries. 

#### Page Table with Hardware Support 
- OS maintains a page table per process; a pointer to the page table is stored in the process table.
- When the short term scheduler selects a process for execution, its page table must be loaded into memory. 
- **Hardware implementation:** 
	- Dedicated high-speed registers, which makes the page-address translation very efficient, but it increases context-switch time, since each register must be exchanged during a context-switch
	- Page-table based register, which is used to save a pointer to a page table that is kept in memory and changing page table requires only a chance in the content of the register

##### Translation Look-Aside Buffer
- Small, special, fast-lookup hardware cache with a key and a value. 
- Used with a page table by saving a few of the page table entries
- TLB contains only a few of the page-table entries
	- When a logical address is generated by the CPU, the MMU first checks if its page number is present in the TLB; if it is found, then the frame number is immediately available (hit) and the MMU can map the physical address which is used to access memory
	- If a TLB miss happens, then address translation occurs:
		1. Search page table and get a page frame number to map to the physical address
		2. The search result will be added to the TLB, so that they will be used quickly on the next reference
		3. If the TLB is already full of entries, an existing entry must be selected for replacement based on replacement policies (LRU, round-robin, random)
	- **Effective access time:** (hit ratio)$*$ memory access time + (1-hit ratio)$*$ 2$*$ memory access time
![[Pasted image 20230328225917.png]]

#### Page Table Continued 
- Advantage of paging is sharing common code
![[Screenshot 2023-03-29 at 10.06.35.png]]
#### Structure of a Page Table
- **Multilevel page table:** avoiding keeping all the page tables in memory all of the time
- EXAMPLE: second level page tables

The system generates a 32-bit virtual address with a page size of 4KB. Since you have a 4KB page size, the offset is 12-bits, and the remaining 20 bits are partitioned into a 10-bit PT1 field and a 10-bit PT2 field.

The **first level** is a page table for page tables. Since there are $2^{10}$ entries, there are $2^{10}$ page tables. The present/absent bit is used to save whether the table is in memory or not. 

The **second level** is the page table for pages. Each second level page table has $2^{10}$ entries and since a page size is 4KB, each second level page table supports $2^{10} * 4KB = 4MB$ virtual space. ![[Screenshot 2023-03-29 at 10.13.28.png]]

- **Hashed page table:** one approach for handling address spaces larger than 32 bits; the hash is the virtual page number.
	- Reduces the number of entries in the page table
	- Each entry in the hash table contains a linked list of elements that hash to the same location
	- Each element has three fields (page number, page frame number, pointer to the next element in the linked list)
	- **Algorithm**
		1. Virtual page number in the virtual address is hashed into the hash table
		2. Virtual page number is compared with field 1 in the first element in the linked list
		3. If there is a match, the corresponding page frame is used to form the desired physical address
		4. If there is no match, subsequent entries in the linked list are searched for a matching virtual page number
![[Screenshot 2023-03-29 at 10.22.35.png]]
- **Inverted page table:** contains one page table entry for every frame of the main memory
	- Number of page table entries in the inverted page table is the number of page frames in physical memory
	- A single page table is used to represent the paging information of all of the processes
	- Overhead of storing an individual page table for every process gets eliminated and only a fixed portion of memory is required to store all of the paging information
	- Each entry has a virtual page number, process ID, and control bits 
		- Given the process ID and virtual page number, you can search the inverted page table for a match
		- If the match is found at the $i^{th}$ entry, then this page is located in the $i^{th}$ frame and you can calculate the physical address to access memory.
	- Increases the amount of time needed to search since it's based on physical address but lookups occur based on virtual address. 
		- Can alleviate this problem by using a hash table, though it adds a memory reference (one virtual memory reference requires at least two real memory reads, one for the hash table entry and one for the page table)
	- Can have an issue with shared memory, since there is only one virtual page for every physical page, one physical page cannot have two or more shared virtual pages. 
		- A reference by another process sharing the memory will result in a page fault and will replace the mapping with a different virtual address
	![[Screenshot 2023-03-29 at 10.28.58.png]]
	
### Demand Paging and Page Fault
- **Demand paging:** type of swapping done in virtual memory; the data is not copied from the disk to main memory until they are needed or being demanded by some program. 
	- While a process is executing, some pages will be in memory and some will be in secondary storage
- **Page fault:** a process tries to access a page that was not brought into memory
- **Algorithm for accessing memory:** 
	1. Check the location of the referenced page in the page table
	2. If a page fault occurred, call on the OS to fix it
	3. Using the frame replacement algorithm, find the frame location
	4. Read the data from disk to memory 
	5. Update the page map table for the process
	6. The instruction that caused the page fault is restarted when the process resumes execution
- **Pure demand paging:** after a page is brought into memory, the process continues to execute, faulting as necessary until every page that it needs is in memory
	- It can then execute withh no more faults (never bringing a page into memory until it is required)
	- **Algorithm:**
		1. Trap the OS (control change from process to kernel)
		2. Check page fault or not by checking the page table for the process
		3. Check that the page reference was legal and determine the location of the page on the disk 
		4. Issue a read from the disk to a free frame
		5. Coorrect the page table
		6. Restore process status
		7. The process starts to run the instruction
	- Three major task components of the page-fault service time
		1. Service the page-fault interrupt (save the process status, find the free page frame or find victim page frame)
		2. Read in page
		3. Restart process
	- **Effective access time with page faults:** 
		- $p$: page fault rate
		- $m$: memory access rate
		- $s$: page switch time
		- formula: $(1-p)*m + s*p$
		- Directly proportional to the page fault rate. 
	- Usage of the swap space:  I/O to swap space tends to be faster than to the file system because it is allocated in much larger blocks, and file lookups and indirect allocation methods are not used.

#### Swapping with Paging
- Process instructions and data must be in memory to be executed, but a process or a portion of it may be swapped temporarily uot of a memory to a backing store and then brought back into memory for continued execution.
	- Swapping makes it possible for the total physical memory address space of all processes to exceed the real physical memory
	- Most systems use a version of swapping that takes out individual pages in a process rather than an entire process

#### Free-Frame List
- When a page fault occurs, the OS must bring the desired page from secondary storage into a free page frame in memory
	- OS must create a free frame if there is non
	- Most OS maintains a free-frame list, which is a pool of free frames

#### Copy-on-Write between parent and child
- When using the fork() command, allows parent and child processes to initially share the same pages. 
	- These pages are marked as copy-on-write pages, whichh means that if either process writes a shared page, a copy of the shahred page is created.
- When using vork(), it doesn't use copy-on-write, so if the child proocess modifies any pages of the parent's address space, the modified page will be visible to the parent once it resumes. 

#### Structure of a Page Table Entry
![[Screenshot 2023-03-29 at 11.21.53.png]]

### Page Replacement Algorithms
- When a page fault occurs and there is no free frame: 
	1. Choose a victim page current allocated in a free frame (dependent on replacement algorithmh)
	2. If the page has been modified in memory, rewrite to the disk
	3. A page is allocated into the page frame which was used by the victim page
	4. Change page table
- If no frames are free, two page transfers (one for page out and one for page in) are required 
- A process's memory access can be characterized by a list of page numbers, which is called the **reference string**
- Three items to characterize the paging system: 
	1. The reference string of the executing process
	2. The page replacement algoritmh
	3. The number of page frames available in memory for a process

#### Optimal Algorithm 
- Replace the page that will not be used for the longest time
![[Screenshot 2023-03-29 at 11.44.07.png]]

#### Not Recently Used
- When a page fault occurs, the OS inspects all pages and classifies them into four groups based on the page table information (modified, reference)
	1. Class 0: not referenced, not modified
	2. Class 1: not referenced, modified
	3. Class 2: referenced, not modified
	4. Class 3: referenced, modified 
- Removes a page at randdom from the lowest numbered, non empty class

#### First In, First Out
- Replace the oldest page frame
![[Screenshot 2023-03-29 at 11.48.47.png]]

#### Second Chance
- Simple modification to FIFO that avoids the problem of throwing out a heavily used frame by inspecting the R bit of the oldest page. 
	- If the oldest page's R = 0 and is not referenced, it is removed
	- If the oldest page's R = 1, it is old but referenced, so you set R = 0 and it moved from oldest to newest
![[Screenshot 2023-03-29 at 11.51.16.png]]

#### Clock Replacement Algorithm
- Similar to second change, but you keep all of the page frames in a circular list
- When a page fault occurs, the page the hand is pointing to is inspected
	- If R = 0, evict the page
	- If R = 1, clear R and advance the hand
![[Screenshot 2023-03-29 at 11.56.00.png]]

#### Least Recently Used
- Replace the page that has not bee used for the longest time
- Optimal page replacement looking backward in time
- Requires use of a counter (to save the reference time) and a stack (to keep a stack of page numbers)
![[Screenshot 2023-03-29 at 11.57.35.png]]
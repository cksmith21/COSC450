### Midterm Review 

- Memory Management
	- Memory management with page
		- Page table
		- Multiple page tables
	- Page replacement algorithms
		- Optimal
		- Not Recently Used
		- First In First Out
		- Second Chance
		- Least Recently Used
	- Modeling Page Replacement Algorithm 
		- Belady's Anomaly 
		- Stack algorithm 
			- Model for stack algorithm
			- Property of stack algorithm 
	- Design issues for page system
		- Local versus Global allocation policies 
		- Page size 
	- Segmentation
		- Segmentation implementation 
		- Advantage of segementation
			- Protection 
			- Sharing
	- Segmentation with paging
		- MULTICS 
- File System
	- File name, structure, type, access, attributes, operation
	- Directories
		- DIrectory operations
	- File System Layout
	- Implementing Files
		- Contiguous allocation
		- Linked list allocation
		- Linked list allocation with FAT
		- Index-Nodes
	- Shared Files
		- Save i-node index
		- Symbolic link
	- Log Structured File System
	- Disk Space Management 
		- Block size
		- Free block management 
			- Linked List
			- Bit Map
	- File System Backup 
		- Physical backup
		- Logical backup 
- Deadlocks 
	- Resources for a process
	- Deadlock condition 
	- Resource allocation graph
	- Four strategies for dealing with deadlock
	- Deadlock Detection and recovery
		- Deadlock with one resource of each type
		- Deadlock with multiple resources of each type
			- Deadlock detection algorithm
		- Recovery from deadlock 
	- Deadlock avoidance
		- Safe and unsafe state
		- Banker's Algorithm
	- Deadlock Prevention
		- Attack mutual exclusion
		- Attack hold and wait
		- Attack no preemption
		- Attack circular wait 
	
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

### Lecture 16: Modeling Page Replacement Algorithms 

- Belady's Anomaly
	- Initutively, it might seem that the more page frames the memory has, the fewer page faults a program will get. 
	- Belady shows the case with the FIFO: the more page frames the memory has, the more page faults a program has
![[Screenshot 2023-04-20 at 09.42.02.png]]
- Stack Algorithm 
	- A paging system can be characterized by three items: 
		1. The reference string of the executing process
		2. The page replacement algorithm
		3. The number of page frames available in memory 
	- Model for stack algorithm
		- Maintains an internal array M that keeps track of the state of memory
		- M has as many virtual memory pages n
		- Top m entries contain all the pages currently in the memory (page frames)
			- n: # of pages, m: # of page frames
		- Bottom n-m entries contains all the pages that have been referenced but have been swapped out and are not currently in memory
		- Properties of the model:
			1. When a page is referenced, it is always moved to the top entry in M
			2. If the page referenced was already in M, all pages above it moves down one position
			3. A transition from within the box to outside of it corresponds to a page being evicted from the memory
			4. The pages that were below the referenced page are not moved
	- Property of Stack Algorithm 
		- If any page replacement algorithm has the following property, we call it a stack algorithm: 
			$M(m,r)$ subset $M(m+1, r)$
		- If we increase memory size by one page frame and re-execute the process, at every point during the execution, all the pages that were present in the first run are also present in the second run, along with one additional page
		- If a replacement algorithm is a stack algorithm, it does noot suffer from Belady's algorithm 
- Design Issues for Paging System
	- Load control: even though we use the best replacement algorithm with global allocation policies, if the combined working set of all processes exceed the capacity of memory (if the system supports a high degree of multiprogramming with a small main memory), thrashing can be expected
		- Thrashing: two of more processes accing a shared resource repeatedly such that serious system performance degredation occurs because the system is spending a disproportionate amount of time just accessing the shared resource
		- Possible concern is reducing the degree of multiprogramming - swapping out somee of the processes into the disk 
	- Page size: 
		- Smaller page size leads to smaller internal fragementation 
		- Larger size will cause more unused programs to be in memory than a small page size
		- Smaller pages mean that programs will need many pages (hence a large page table)
		- Many pages wastes time for seek and rotation
		- Some system need to load the epage table into the hardware registers to execute - many small pages, need more page tables for loading time
		- Overhead of paging system
			- Page table
			- Internal fragmentation loss 
	- Mathematical analysis: 
		- $S$: average size of a process (bytes)
		- $P$: the size of a page (bytes)
		- $E$: amount each page table entry needs (bytes)

		$S/P$: average number of pages per process
		$(S/P) * E$: average page table space
		$P/2$: the wasted memory in the last page of the process 

		**Total overhead by page table and internal fragementation loss is:** 
		1. Overhead(P): $(SE/P) + P/2$
		2. Overhead'(P): $-(SE/P^2)+1/2 = 0$
		3. Solve for P = $P = sqrt(2SE)$ 
- **Segmentation**
	- Segmentation is a logical entity, which the programmer knows and uses as a logical entity
	- Segmentation is a memory management scheme that supports the user's view of memory
	![[Screenshot 2023-04-20 at 18.39.23.png]]
	- A logical addreess space is a collection of segments; each segment has a name and a length
	- Each segment might have a different size
	- A segment might contain a procedure or an array or a stack or a collection of scalar variables, but usually it does not containa mixture of different types
	- Normally, when the user program is compiled, the compiler automatically constructs segments reflecting the input program
	- Segmentation table saves the size of the segment and base address (beginning of physical addreess) for each segment 
	- EX: any compiler might create separate segments for the following: 
		1. The global variablse
		2. The procedure calls stack to store parameters and return address
		3. The code portion of each procedure or function
		4. The local variablse of each procedure and function
	![[Screenshot 2023-04-20 at 18.48.55.png]]
	- Advantages with segmentation: 
		- Protection: because the segment represents a semantically defined portion of the program, it is likely that all entries in the segment will be used for the same way
			- It is relatively easy to protect a segement 
			- Examples:
				1. In modern architecture, instructions are non-self-modifying, so the instruction segement can be defined as read or execute only; the memory maping hardware check the protection  bit associated with each segment table entry to prevent illegal accesses to memory 
				2. Big array is placed in its own segment; the memory management hardware will automatidally check that array indexes are legal and do not stray outside the array boundaries
		- Sharing: a segment can be shared by several process without allocated segment space for each process
			- Examples:
				- Shared library: modern work stations that can run advanced window systems have extremely large graphical libariese compiled into nearly every program' graphical libraries can be put into a segement
- Segmentation with paging
	- Reasons: 
		- If the segments are large, to keep them in the physical memory might be wasting memory space
		- If a segment's virtual space is larger than its physical space, it is not even possible to keep them in physical memory
		- Solution: each segement is divided into pages
	- MULTICS
		- For each program, a virtual memory of up to $2^{18}$ segments are allowed
		- Each program has a segmentation table
		- Since there are potentially $2^{18}$ entries in the segementation table, a segment table is a segment within the page table
![[Screenshot 2023-04-21 at 14.02.16.png]]
![[Screenshot 2023-04-21 at 14.02.27.png]]
![[Screenshot 2023-04-21 at 14.03.15.png]]
![[Screenshot 2023-04-21 at 14.03.29.png]]
### FIles and Directories 

#### File System

- Three essential requirements for long term information storage: 
	- To store a very large of infomration 
	- To store information permanently
	- To share the information with multiple processes
- File Names: 
	- \<filename>.\<extension>
	- Length: 255
	- UNIX: case sensitive
	- DOS, Windows: not case sensitive  
- File Structure 
	- UNIX, Linux, Windows: file is an unstructured sequence of bytes, OS does not know what is in the file
- FIle Types:
	- Regular files: for user's information 
		- ASCII: line of text; line is terminated by carriage return or special character
		- Binary file: 
			- Executable file (OS can execute a file if it has the proper format)
				- Header: all information regarding execution (magic number -- identifying the file as an executable and size of file, address where execution starts)
				- Text
				- Data
			- Archive file (consists of a collection of library functions compiled but not linked)
				- Data file
	- Directories: system files for maintaining the stucture
- File Access: 
	- Sequential access: a program can read all of the bytes in a file in order, starting from the beginning
		- Read operation changes the poointer to the next location for read
	- Random access: a proocess can read all bytes in a file in any order
		- Seek operation can move the pointer point to the location (HDD, SSD)
- File Operations: create, delete, open, close, read, write, append, seek, get/set attributes, rename

#### Directories

- File systems use directories to keep track of files
- Types of directories: 
	- Single level directory system: one directory containing all files
	- Two level directory system: giving each user a proviate directory; users create a file under their directory; each file name must be unique 
	- Hierarchical directory system: user can create directories to group their files in a logicla way; same file name can be used under a different directory 
![[Screenshot 2023-04-10 at 13.49.21.png]]
- Directory operations: create, delete, open, close, rename, read, link

#### File System Layout

- When the computer is booted: 
	- BIOS reads in and executes MBR
	- The MBR (**master boot record**) locates the active partition
		- Read in the first block (boot block) in a partition and execute is
		- The program in the boot block loads the OS contained in that partiton (active partition)
- System layout of a disk partition: 
	- Super block: key parameters about the file system; magiv number to identify the file system type, the size/number of blocks
	- Free space management 
	- I-Node
	- Root directory
	- Directories and files

#### Implementing Files 

- Disk spaces are divided into blocks and one or more blocks are used to save a file
- Implementing a file: 
	- **Contiguous Allocation**: a file is saved in contiguous blocks
		- Advantages: 
			1. Simple implementatoon: system needs to keep two number for each file in the directory: disk address of the first block and the number of blocks used for the file
			2. Read performance is excellent: the is only one seek needed to read the entire file since the each block is allocated contiguously
		- Drawback: fragmentation; if a newly created file size is fixed, system just finds a big hole to fit the file - if it is not fixed, then there is a problem 
	![[Screenshot 2023-04-11 at 08.08.51.png]]
	- **Linked list allocation**: a block is used for saving data and the next block information (block number)
		- Drawbacks:
			1. Reading a file sequentially is straight forward, but random access is extremely slow 
			2. Since storage per block is not a power of 2, need effort for reading/writing in the block 
	![[Screenshot 2023-04-11 at 14.07.51.png]]
	- **Linked list allocation with File Allocation Table**: block information (which blocks are used for each file) is saved in the FAT
		- All link nformation for each file is written into the FAT which is located in main memory 
		- Primary disadvantage: 
			- Since entire table needs to be in memory all od the time, a lot of space needs to be used
			- EX: 20 GB harddrice with 1KB bloock size, need 20 million entries for FAT. If each entry is 3 bytes, we need 60 MB for the FAT
	![[Screenshot 2023-04-11 at 14.11.09.png]]
	- **Index-Node allocation**: block information for each file is saved in an I-node
		- Lists the attributes and disk addresses 
		- To open a file, a system only needs to load the corresponding i-noode into memory 
		- Issue: the i-node has room for a fixed number of addresses, so what happens when a file grows beyond this limit? 
			- Solution: reserve the last disk address for the address of a block containing more block addresses
	![[Screenshot 2023-04-11 at 14.15.13.png]]
- Different information needs to be saved in the directory for each file depending on how the file is implemented 
	- CA: first block number and size
	- LLA: first block number
	- LLA w/ FAT: just in FAT
	- INA: i-node number

#### Implementing DIrectories 

- Where should attributes for each file be saved? 
	- DOS/Windows: directory entry
	- UNIX: i-node entry
- How to handle different size of file names: 
	1. Set a limit on a file name stored in a directory entry or in an i-node entry (simple, wasting space)
	2. All directory enties start wth the length of the entry followed by a date with a fixed foormat (when a file is deleted/created, there is fragmentation)
	3. Make the directory entries a fixed length and keep the file names together in a heap at the end of the directory
- Searching directories: 
	- If a directory is designed in a linear way, for extremely long directories, linear searching can be slow
	- To speed up the search: 
		- Use hash table in each directory
		- Use cache
![[Screenshot 2023-04-11 at 15.36.52.png]]
### Shared File System and Disk Space Management 

- To share a file, it is convenient to appear simultaneouosly in different directories belonging to different users ![[Screenshot 2023-04-11 at 08.05.04.png]]
- It's convenient but it produces problems when a shared user modifies the shared file ![[Screenshot 2023-04-11 at 08.05.57.png]]
- Solutions (both have problems):
	1. Disk block information for a file is saved not in the directory but in the i-node
	2. A system can create a symbolic link to a shared file; the new file contains the path name of the shared file
- **Shared Files with i-node**
	- Assume that user C creates a file and it is shared with user B
	- i-node number must be saved in both directoories 
	- If the user C removes the shared file then the i-node used for the shared file can be used for the other new file 
	![[Screenshot 2023-04-11 at 08.08.51.png]]
	- Solution: 
		- System check the number of users for a file in the i-node
		- If the number of users is > 0, the file can be retained
		- Problem still: even though C removed a shared file, that space is counted as C's space since it is owned by C; used for counting the quota for C
			- Possible since system may allocate a specific amount of disk space per user
- **Shared Files with Symbolic Link**: 
	- The same problem with i-nodes does not arise since only the real owner has a pointer to the i-node
	- Extra overhead is required 
		- To read or write a file: get a path, parse the path from root 
		- Extra block for an i-node is required for each symbolic link to store only path, wasting disk space
- **Log Structured File Structure**: 
	- CPU speeds have increased dramatically while disk access times (seek times) have only improved slowly
	- Cause more and more applications to become disk-bound
		- Log Structured file system is designed to reduce the impact of this problem
	- Designed for HDD but also used for SSD
	- Based on the assumption that files are cached in main memory
		- DIsk traffic will be dominated by writes
		- Writes all new information to disk in a sequential structure called the log (contiguous blocks)
		- Increases write performance speed by eliminating almost all seeks 
	- The sequential nature of the log also permits much faster crash recovery:
		- UNIX: scans entire disk to restore consistency after a crash
		- LSF: examines thhe most recent portion of the log 
	- Some file systems use a log as a temporary storage to speed up write and crash recovery 
	- LSF system stores data permananently in the log: there is no other structure on disk 
	- It must ensure that there are always large amounts of free space available for writing new data
	- A **segment thread cleaner** continually regenerates empty segments:
		1. Read the summary of the first segment
		2. Check the i-node map to find out it is currently used segment 
		3. If not, it is set as an available segment for write operation 
	- **Basic idea**: structure the entire disk as a log
		- All writes are initially buffered in memory 
		- Periodically, all buffered writes are written to the disk in a single segment at the end of the log
		- A single segment contains i-nodes, directory blocks, and data blocks, all mixed together
	- In a UNIX system, each i-node is at a fixed location disk; in an LSF, each i-node is not at a fixed location, they are written to the log
		- The LSF uses a data structure called an i-node map to maintain the current location of the each i-node for each file
		- Opening a file consists of using the map to locate the i-node for the file 
	- Once an existing file is open for update, a segment of the file is loaded into memory (file cache)
		- It always writes back to the disk at the end of the log
		- Means that the previously updated file will not copy back to the previous location
- **Disk Space Management**: 
	- Disk is always divided into blocks of the same size
	- We need to decide the size of a block: larger block size == internal fragmentation; smaller block size == more seek and rotational delay 
	- **Larger block size**: 
		- Consider disk space efficiency:
			- With 4KB files with 1KB, 2KB, or 4KB per block - no wasting space 
			- With a 4KB file on an 8KB block - 50% wasting soace 
			- With a 5KB file with 1KB, 2KB, 4KB, or 8KB block - 
				- 1KB = 5 blocks = no wasting space 
				- 2KB = 3 blocks = 1KB wasting space 
				- 4KB = 2 blocks = 3KB wasting space 
				- 8KB = 1 block = 3KB wasting space
	- **Small Block Size**: 
		- Each file will consist of many blocks
		- Reading in each block requires more seek and rotation delay in HDD
		- EX: a disk with 1MB per track, a rotation time of 8.33 msec and an average seek time of 5 msec. The time in msec to read a block of k bytes oof the sum of the seek rotation and transfer time. 
			$5 + (2^{20}/k) * 8.33 + 4.165$
			seek time + number of blocks per track * rotation time + initial rotation time (half of rotation time)
- **Free Block Management**
	- System reserves spaces (blocks) to save free block information:
		- Linked list and bitmap 
	- **Linked List** 
		- Each block for free blocks list holds as many free disk block numbers as will fit 
		- EX:
			with a 1KB block size and a 32 bit disk block number
			a block can hold = $8 * 2^{10} / 32 = 256 - 1 = 255$ free blocks (1 slock for next free block pointer)
			16 GB disk has $2^{34}/2^{10} = 2^{24}$ blocks so you need $2^{24}/255 = 65794$ blocks to hold free block numbers 
		- When the free list method is used, only one block of pointers needs to be kept in main memory 
		- When a file is created, the needed blocks are taken from the block of pointers
		- When it runs out, a new block of pointers is read in from the disk
		- When a file is deleted, its blocks are freed and added to the block of pointers in main memory
	![[Screenshot 2023-04-11 at 08.39.54.png]]
	- **BItmap**: a disk with n blocks requires a bitmap with n bits. A free block is represented by a 1 in the map, allocated blocks are represented by 0 
		- EX: 
			16 GB disk, size of block = 1 KB
			there are $2^{34}/2^{10} = 2^{24}$ blocks 
			need $2^{24}$ bits for bitmap = $2^{24}/2^3$ bytes = $2^{11} * 2^{10}$ bytes = $2^{11}$ KB = 2048 blocks for the bit map 
		- EX: 
			**Linked List**
				4 KB block size and a 64 bit disk block number
				a block can hold $8*4*2^{10} / 64 = 512 - 1 = 511$ free block numbers 
				a 2 TB disk has $2*2^{40}/4*2^{10} =2^{41}/2^{12} = 2^{29}$ blocks 
				$2^{29}/511 = 1050628.007$ so you need to reserve 1050629 blocks to hold free block numbers 
			**Bitmap**
				2TB disk has $2^{29}$ blocks
				size of bitmap = $2^{29}$ bits = $2^{29}/2^3 = 2^{26}$ bytes = $2^{16}$ KB 
				$2^{16}KB / 4KB$ = $2^{14}$ = 16384 blocks 

#### DIsk Quota

- The system administrator assigns each user a maximum allotment of files and bloocks
- The OS makes sure that each user does not exceed their quotas
- When a user opens a file, the attributes and disk addresses are located and put into an open file table in main memoory 
- The **quota table** contains the quota record for every user with a currently opened file
- The record is extracted from a quota file on disk for the users whose files are currently open
- When all files are closed, the record is written back to the quota file
![[Screenshot 2023-04-12 at 09.15.51.png]]

#### File System Backup 

- File System Backup Design Issues:
	1. Should the entire file system be backed up or only part of it? 
	2. It is wasteful to backup files that have not changed since the last backuo
	3. Since a big amount of data is typically dumped, it may be desirable to compress the data before writing them to tape or disk 
	4. It is difficult to perform a backup on an active file system - making rapid snapshots of the file system
	5. Backup introduces many nontechnical problems into an organization - security problem 
- **Physical Dump**: 
	-  Start at block 0 of the disk, writes all of the disk blocks onto the output tape or disk in order, stop when it has copied the last one
		- How to handle skipping unused blocks
		- How to handle bad blocks
	- Advanrtage: simplicity, speed
	- Disadvantage: unable to skip selected directories, make incremental dumps, and restore individual files
- **Logical Dump**: starts at one or more specified directories and recursively dump all files and directories found there that have changed since some given base date
	- The dump algorithm maintains a bitmap indexed by the i-node number with several bits per i-node
	- The algorithm is in 4 phases
		1. Begins at the starting directory and examines all entries in it. For each modified file, its i-node is marked in the bitmap. Each directoory is also marked and recursively inspected
		2. Unmarking any directories that have no modified files or directories in them or under them 
		3. All marked directories are dumped
		4. All marked files are dumped 
	![[Screenshot 2023-04-12 at 09.48.01.png]]
	![[Screenshot 2023-04-12 at 09.48.11.png]]
	![[Screenshot 2023-04-12 at 09.48.22.png]]
	![[Screenshot 2023-04-12 at 09.48.30.png]]

### Deadlock
- **What is deadlock?**: several processes are competiting for a finite number of resources; as a process requests resources, if the resources are not available, the process enters a block state. Sometimes a blocked prcesses can never change statee again because the resources it has requested are held by other blocked processes. 
	- Deadlocks between processes occur because there is a limitation on the resources that must be shared 
	- Cannot avoid deadlock without proper management by the OS
- **Deadlocks**: a common problem in multiprocessing systems, parallel computing, and distributed systems, where software and hardware locks are used to handle shared resources and implement process synchronization 
	- EX: in a transactional database, a deadlock happens when two processes each within its own transcation updates two rows of information in the opposite order. 
	![[Screenshot 2023-04-13 at 08.06.21.png]]
- **Resource Allocation Graph**: 
	- This graph consists of a set of vertices V and a set of edges E
	- V is partitioned into two types: 
		- P = {$P_1, P_2, ... P_n$} = set a processes and 
		- R = {$R_1, R_2, ..., R_n$} = set of resources
	- Edge from a process to a resource
		- P --> R denotes a process P requesting resource R and is currently waiting 
	- Edge from a resource to a process 
		- R --> P denotes a resource R being currently held by a process P 
![[Screenshot 2023-04-13 at 08.09.38.png]]
- **Deadlocks (continued)**: there are two processes  ($P_1, P_2$) working on their job and two resources ($R_1, R_2$). Both  ($P_1, P_2$) need both ($R_1, R_2$) to finish their job.
	- $T_0$: $P_1$ requests $R_1$ and is granted
	-  $T_1$: $P_2$ requests $R_2$ and is granted
	- $T_2$: $P_1$ requests $R_2$ without releasing $R_1$; but, since $R_2$ is held by $P_2$, $P_1$ goes to the block state to waite for $R_2$ to release $R_2$ 
	- $T_3$: $P_2$ requests $R_1$ but it had not be released by $P_1$ yet. $P_2$ foes into the blocked state and both proceses will be blocked forever. 
	![[Screenshot 2023-04-13 at 08.15.41.png]]
	![[Screenshot 2023-04-13 at 08.15.52.png]]
- **Resources for a Process**: 
	- **Preemptive resources**: resources that can be taken away from a process without ill effect (ex: memory)
	- **Non-preemotive resources**: resoruces that cannot be taken away from a process that currently owns it until the process finishes with the resource and releases it (ex: printer, CD)
- **Sequence for Resource Use**: 
	- The sequence of events for using a resource: 
		1. Request the resource
		2. If availbale, hold and use the resource 
		3. Release the resource 
	- If a resource is not available when it is requested, the requesting process might be: 
		- Blocked and then awakendd when it is available 
		- Wait a little and try again 
	- **Implementation of resource request, use, and release**: 
		- High system dependent 
		- Usually the request/release of resources are system calls 
		- Request and release can be accomplished through the down and up operations on semaphoers 
		- If a resource is already allocated to a process, the process requesting the resource is added to a queue of waiting for this resource
		- EX: 
			- Let's say there are two processes $P_1, P_2$ and two resources $R_1, R_2$ 
			- Both processes need both resources to finish their job 
			- Each resource is associated with  a semaphore 
![[Screenshot 2023-04-13 at 08.22.36.png]]
![[Screenshot 2023-04-13 at 08.22.48.png]]
- **Deadlock Conditions**: a deadlock can arise if and only if the following four conditions hold simultaneously in a system: 
	1. Mutual exclusion 
	2. Hold and wait
	3. No preemptiion
	4. Circular wait
- **Deadlock Example**
![[Screenshot 2023-04-13 at 08.24.01.png]]
![[Screenshot 2023-04-13 at 08.24.24.png]]
- **Four Strategies of Dealing with Deadlocks** 
	1. Just ignore
	2. Detection and recover
	3. Dynamic avoidance by careful allocation
	4. Prevention by negating one of the four conditions necessary to cause deadlock 

### Deadlock Detection and Recovery 

- **Deadlock Detection and Recovery**: when a process requests a resource and if the resource the available, the system lets the process hold the resource 
	- For each resource allocation, the OS keeps track whether there is a deadlock or not (checks after each resource allocation)
	- When a deadlock is detected by the system, the system takes some action to recover from the deadlock 
	- **Deadlock detection with one resource of each type**: simple, done by keeping a resource allocation graph 
		- A simple graph algorithm which can detect. a cycle in a directed graph can detect a deadlock
			- Simply find a path from the node to the node itself is a cycle 
		 ![[Screenshot 2023-04-13 at 08.28.46.png]]
	- **Deadlock detection with multiple resources of each type**: the OS needs to keep matrices for deadlock detection algorithms 
		1. Existing resource matrix (one dimension): presents how many numbers of resource per each type
		2. Available resource matrix (one dimension): presents how many number of resources per each type are availabale at any moment, since some resources are assigned to processes that are not available
		3. Current allocation matrix (two dimension): $row_i$ in matrix C presents the resources currently held by process $P_1$ 
		4. Request matrix (two dimension): $row_1$ in matrix R presents how many more resources are needed for a process $P_1$ to finish its job 
	![[Screenshot 2023-04-13 at 08.32.44.png]]
		- The deadlock detection algorithm is based on comparing vectors.
			- Let's define the relationship $A \le B$ between two one-dimension vectors A and B, which means that each element of A is less than or equal to a corresponding element of B 
			- EX: A = (1, 2, 0, 2); B = (1, 3, 0, 2). Is $A \le B$ true? Yes
		- **Deadlock detection algorithm**: 
			1. All processes start unmarked 
			2. Look for an unmarked process $P_i$, for which the $i^{th}$ row of R (the resources needed by $P_1$) is less than or equal to A
			3. If such a process is found, add the $i^{th}$ row of C to A, mark the process and go back to step 1. 
			4. If no such process exists, the algorithm terminates
		![[Screenshot 2023-04-13 at 08.37.08.png]]
		- You add the columns of C to its corresponding A value to get E
		- Each row of C corresponds to a process ($P_3$ = third row of C)
- **Recovery from a deadlock** 
	- **Recovery through preemption** 
		- Take a resource from some other process, depends on the nature of the resource 
	- **Recovery through rollback**: 
		- Checkpoint a process periodically, use this save state, restart a process if it is found deadlocked 
	- **Recovery through killing processes**: 
		- Crudest but simplest way of breaking a deadlock
		- Kill one of the processes in the deadlock cycle
		- Other processes get its resources
		- Killed process needs to start from the beginning

### Deadlock Avoidance 

- When a process requests a resource, OS must be able to decide whether granting a resource is safe or not, and only make an allocation when its safe
- Is there any algorithm that can always avoid deadlock by making the right choice all of the time? 
	- Yes, when certain information is available in advance 
- **Safe and unsafe state**: a state is said to be the safe state if it is not deadlocked and there is some scheduling order in which every process can run to competion even if all of them suddenly request their maximum number of resources immediately 
- **Banker's Algorithm for a Single Resource**: 
	- Modeled by the way a small town banker might need to deal with a group of customers
	- What Banker's algorithm does is check to see whether granting the resource leads to an unsafe state or not. If granting the request leads to an unsafe state, the request is denied.
![[Screenshot 2023-04-18 at 08.46.55.png]]
![[Screenshot 2023-04-18 at 08.47.06.png]]

- **Banker's Algorithm for Multiple Resources** 
	- Using the three available matrices for checking a safe state: 
		1. Available resource matrix (A): the number of resources per each type that are available at any given memory, since some resources assigned to processes are not available
		2. Request matrix (R): how many resources are needed for processes to finish their jobs 
		3. Current allocation matrix (C): how many resources are currently held by processes 
	- Algorithm: 
		1. Look for a row of vector R whose unmet resouce needs are smaller or equal to A. IF not such row exists, the system will eventually deadlock since no process can run to completition 
		2. Assume the process of the row chosen requests all of the resources it needs and finishes. Mark that process as terminated and add all its resources to the A vector. 
		3. Repeat steps 1 and 2 until either all processes are marked terminated (safe state) or until a deadlock occurs (unsafe)
- **Deadlock Prevention**
	- Deadlock avoidance is essentially impossible to use for the OS in a complex system, since it requires information about future request which are usually not known. 
	- Deadlock prevention is a method to attack one of four deadlock conditions: 
		1. Mutual exclusion
		2. Hold and wait
		3. No preemption
		4. Circular wait 
	- **Attacking mutual exclusion**: the mutual exclusioon condition must hold for non-preemptive resources such as printer, CD ROM writer
		- Preemptive resources do not require mutual exlcusion such as a read only file 
	- **Attacking hold and wait**: to ensure the hold and wait condition never occurs in the system, we must guarantee that, whenever a threads requests a resource, it does not hold any furthere resources 
		- If a thread or process is currently holding any resources, it needs to release all resources and request all resources 
		- **Approach One:** 
			- Each process requests  all resources before starting execution. If everything is available, all resources (requested by the process) and located and finishes its job
			- If some resources are not available, no resources are allocated to the process
			- Problem with approach one: 
				- Many processes do not know how many resources they will need beefore execution
				- If poossible, Banker's algorithm can be applied 
				- Resources cannot be used optimally
		- **Approach two:** 
			- Allow a process to request resources only when the process has none. To get a new resource, first release all resources currently being held and request all at the same time.
			- Disadvantage: starvation is possible; a process that needed several popular resources may have to wait indefinitely since at least one of the resourcese that it needs is always allocoated to some other process
	- **Attacking No Preemption**: 
		- **Approach One**: 
			- If a process that is holding some resources requests another resource that cannot be immediately allocated, then all resourcees currently being held are preempted 
			- Preempted rersources are intentially released and enter the available resources list 
			- The process will be restarted only when it can regain its old resources as well as the new ones that it is requesting 
		- **Approach Two**
			- If a process requests some resources, first check whether they are available or not
				- If they are: allocate them to the process
				- If they aren't: check whether they are allocated to some other process that is waiting for additional resources
					- If so, preempt the desired resource from the waiting process and allocate them to the requesting process
					- If the resources are not either available or held by the waiting process, the requesting process must wait
		- **Attacking Circular Wait**: 
			- **Approach One**: 
				- A process can only hold one resource
				- If a process needs a second resource, the process needs to release the the first one
				- Sometimes this approach is not acceptable
					- EX. a process needs to copy a huge file from the disk so the process needs to hold a buffer and disk to read/write 
			- **Approach Two**: 
				- Global number is provided to each resource 
				- A proocess can request resources whenever they want, but all requests must be made in numerical order
	
	![[Screenshot 2023-04-19 at 15.51.03.png]]

### Midterm Two

#### Question One: In the file system, two methods are widely used to keep track of free blocks: a linked list and a bit map. Assume the block size is $2KB$ and there is a $32 bit$ disk block number in the file system 

- **TYPE OF PROBLEM: free blocks in memory** 

1. How many maximum blocks are needed to keep track of a 128 GB disk with linked lists?

	- Addresses per block = (($8*$block size)/number of bits for block numbers) $- 1$ 
	- Blocks on disk = disk size/size of block
	- Number of blocks needed = blocks on disk/addresses per block 

2. How many blocks are needed to keep track of a 128 GB disk with bitmap? 

	- Entries in the bitmap = size of disk/block size 
	- Translate to bytes by dividing by $2^3$ 
	- Number of blocks needed = size of bitmap in bytes/size of blocks 

3. What is the maximum disk size supported by this file system? 

	- Number of block addresses = $2^{bits}$ (bits in block address)
	- Size of disk = number of block addresses $*$ block size 

#### Question Two: Log Structured File System

- TYPE OF PROBLEM: knowledge based question 

1. The log structured file system can be applied based on this assumption. What is this assumption? 

	- That all files are cached in main memory.

2. In the LSF system, the i-node is not located in a specific location. Briefly discuss how LSF operating system could access a file.

	- The LSF keeps track of files using something called an i-node map, which stores the location of individual i-nodes on the disk. When the LSF wants to access a file, it loads up its respective i-node in the i-node map and loads that i-node into memory. 

#### Question Three: A computer system generatsee a 32-bit virtual address for a process. This system has 8GB of RAM and a page size is 4KB. 

- **TYPE OF QUESTION: Page tables** 

1. If each entry in the page table needs 64 bits per entry, calculate the possible size of the page table in bytes.

	- Size of virtual space = $2^{bits}$ where bits is the number of bits in the virtual address
	- Number of pages = size of virtual space/size of page 
	- Size of page table in bytes = $2^3$ $*$ number of pages

2. Page frame number information for each page must be saved in the page table. How many bits does it need to save page frame number information? 

	- Number of page frames = physical space/page size
	- Number of bits needed = the number of the exponent in the number of page frames is the number of bits needed to save page frame information

#### Question Four: Let's assume that a Linux system uses bitmap for maintaining free disk block information information. Assume that the bitmap was completely lost due to the crash. Is it possible to recover the bitmap? If possible, discuss your algorithm to recover the bitmap. If not, discuss why.

- **TYPE OF QUESTION: knowledge based, talking about bitmap**

- Yes, the bitmap can be recovered. If you go into the i-node segment of each partition on the disk, you can read into each i-node entry. When you look at each i-node entry, you can see if the block pointed to by the address in the i-node is used or not. If it is used, then that will correspond to a 1 in the bitmap and if it is not, that corresponds to a 0. 

#### Question Five: Consider a system with 5 processes ($p_0, p_1, p_2, p_3, p_4$) and 3 resource types ($A, B, C$) with E=($10, 5, 7$) and A=($3, 3, 2$). The resource allocation state at time $T_0$ is show in the table: 

![[Screenshot 2023-04-22 at 18.14.20.png]]

- **TYPE OF QUESTION: Deadlock, (un)safe state, resource allocation** 

1. Will a request of ($1,0,2$) by $p_1$ be granted? 

	- Starting A = First A - Request of resources from A 
	- The amount of resources the requesting process has is its currently allocated + the amount it requested
	- For each resource, assume that it finishes and gives all of its resources. Add that to A. 
	- Check if $A \le E$ at each step and if there exists a row in R such that $row \le A$
		- If this is true, the program remains in a safe state
		- If this is not true, it has entered an unsafe state and the request should not be granted.

#### Question Six: A system uses paging for managing virtual memory. The system has 4 page frames. The time of loading, time of last access, the reference bit R and modified bit M are shown in the table.

![[Screenshot 2023-04-22 at 18.35.05.png]]

- **TYPE OF QUESTION: page replacement algorithm** 

1. Which page will FIFO replace?

	- Check the page that is first loaded

2. Which page will NRU replace? 

	- Check the page that is in the lowest class (not referenced or modified)

3. Which page will LRU replace? 

	- Check the page that was referenced last 

4. Which page will second chancee replace? 

	 - Check the oldest page, if it has been referenced, change the referenced bit to 0 and move to newest. 
	 - Repeat until find a page that was not referenced

#### Question Seven: Page size is one of the most important design issuse in the operating system. We can mathematically analyze page size based on the following assumptions: 

- $S$: average sizee of a process (bytes)
- $P$: the size of a page (bytes)
- $E$: the amount of bytes each page needs
- 50% of memory in the last page of the process is wasted due to internal fragementation 

- TYPE OF PROBLEM: total overhead for page size 

1. Define the total overhead function based on page size P. 

	- Overhead = $(SE)/P + P/2$

2. Find the optimal page size formula based on the total overhead

	- Take the derivative of (1) with respect to P 
	- Solve for P 

		Overhead'(P): $-((SE)/P^2) + 1/2 => -((SE)/P^2) + 1/2 = 0$ 
				= $-2SE + P^2 = 0$
				= $P = sqrt(2SE)$ 

#### Question Eight: Discuss each of the following: 

- **TYPE OF QUESTION: knowledge based**

1. What are the four necessary conditions for a deadlock? 

	- Mutual exclusion
	- No preemption
	- Hold and wait
	- Circular wait 

2. What are the four strategies for dealing with deadlock? 

	- Just ignore 
	- Detection and recovery
	- Dynamic avoidance by careful allocation
	- Negating one of the four necessary conditions

3. Why are segmentation and paging sometimes combined into one scheme for memory management? 

	- Segementation allows for protection and easy sharing while paging allows the system to save memory space by only loading the required segments

#### Question Nine: The Linux system uses i-node to maintain the file system, with attributes and blocks addresses are saved in the i-node. The i-node contains 8 direct addresses and these were 16 bytes each. A block size is 2KB. If a file uses i-node and one extra block to save block information, what would the maximum file size be? 

- **TYPE OF QUESTION: i-node, file size** 

1. What would the maximum file size be? 

	- Number of addresses per block: ($(8*$ block size)/address size in bits)
	- Size of addresses = number of addresses per block $*$ size of blocks
	- Size of direct addresses = number of direct addresses $*$ size of blocks
	- Total maximum file size = size of addresses + size of direct addresses 

#### Question Eleven: A system has four processes and five allocatable resources. The current allocation and request matrix for each process are as follows:


![[Screenshot 2023-04-22 at 19.47.59.png]]

1. What is the minimum value of X and Y for which this is a safe state? 

	- Check values of x and y to find the lowest one such that a process is able to run 
	- Choose the order of processes that have in order to free up resources that may not currently be available 

#### Question Twelve: To solve the deadlock problem, we can attack one of the necessary deadlock conditions. Mr. Computer provided the following solutions. What necessary deadlock conditions does he attack and what are the probleems with his solution?

1. Solution One: Allows a process to request resources only when it has none; to get new resources, first release all of the resources it currently holds and request all at the same time. 

	- Hold and wait; a process that needs several "popular" resources to run may starve

2. Solution Two: a process can only hold one resource; if a process needs a second resource then it must release the first 

	- Circular wait; most modern processes need several resources to run, such as a process attempting to print a file as a series of consecutive pages on a printer. If it must release the printer to access the file, another proceess may print a page in between.

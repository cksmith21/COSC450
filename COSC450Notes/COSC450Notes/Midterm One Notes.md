### Lecture Note One: What is an Operating System

#### Operating System
- **Modern complex computer system:** processor, memory, I/O devices 
- **Operating system:** protected software that provides interface between hardware and software.
- **Hardware:** 
	- Physical devices: wires, power supply, IC chips (CPU, memory, I/O)
	- Micro-architecture: physical devices that are grouped together to form functional units
	- Machine language: executing some set of instructions 
- **Von Neumann** 
	- Von Neumann bottleneck: limitation on throughput caused by the standard personal computer architecture
		- **Throughput:** measure off how many units of information a system can process in a given amount of time
		- Since processor calculation speeds are much faster than data movement between memory and CPU -- causes a bottleneck
	- Programs and data are held in memory; the processor and memory are separate and data moves between the two
- Ways to consider the OS:
	1. **Extended Machine**: provides an interface between user and hahrdware, computer users can use the hardware (CPU, memory, I/O) without knowing the messy details 
	2. **Resource Managers**: computer consists of CPU, RAM, and I/O devices
		1. Process management, memory management, file management, I/O management, deadlock management 

#### A Computer System
- **Instruction cycle:** the CPU's main task is to execute instructions
- **Fetch Cycle:** 
	1. Reading the address of the instruction in PC to be executed from the memory and 
	2. Loading it into the IR
	3. PC is modified to point to the next valid instruction
![[Screenshot 2023-03-31 at 17.44.46.png]]
- **Execute Cycle**:
	1. Contents of the IR are decoded and executed 
	2. The execution may result in a variety of actions (may be self contained, or involve iteraction with the memory and the ALU)

#### History of Operating Systems

1. First generation (1945-1955): vacuum tubes and plugboards 
	- Use vacuum tubes, all programming done in machine language
2. Second generation (1945-1965): transistors and batch system
	- Computers became more reliable
	- Batch system: programmer writes a program on paper, punch program onto cards, bring cards to input room, wait for output
		- Two IBM machines (1401 to read cards onto tape, put tape onto 7094 to do computing)
3. Third generation (1965-1980): IC and multiprogramming
	- Maintaining two computer products was expensive
		- IBM System/360 was made for scientific and commericial purposes 
			- All software, including the OS, had to work on all models 
		- First major computer to use IC 
		- Improve CPU utilization: multiprogramming, spooling, time sharing 
	- **Multiprogramming:** multiple jobs are loaded into RAM and run concurrently 
		- Once CPU became availabale, one job from the ready queue is selected by the short term scheduler
		- OS needs to keep each job's current status in process table
	- **Spooling:** kind of buffering mechanism for a process in which data is temporarily held as a file to be used and executed by a device, program, or system 
	- **Time sharing:** multiple terminals are sharing CPU time 
4. Fourth generation (1980 - present): PC build with LSI, VLSI, and ULSI 
	- Contains thousands of transistors 
	- User types commands from the keyboard
	- Has a GUI
5. Fifth generation (1990 - present): mobile computers 


### Lecture Three: OS as a Resource Manager

- **Process Managment**: The OS is responsible for the following activities for process managment: 
	1. Creating and deleting both user and system processes
	2. Scheduling processes and threads on the CPUs
	3. Suspending and resuming processes and threads
	4. Providing mechanisms for process synchronization (semaphore, mutex, conditional variable)
	5. Providing mechanisms for process communication (PIPE, message queue, shared memory, FIFO, socket)
- **Memory Management:** for a program to be executed, it must be mapped to absolute addresses and loaded into memory
	- As the program executes, it accesses program instructions and data from memory by generating them
	- CPU reads instructions from main memory during the instruction-fetch cycle and both reads/writres data from main memory during the data-fetch cycle
	- The OS is responsible for the following activities for memory managment: 
		1. Keeping trak of what parts of memory are currently being used and which process is using them 
		2. Allocating and deallocating memory space as needed
		3. Deciding which processes (or parts of processes) and data to move in/out of memory
- **FIle Mangement**: OS provides a logical, uniform view of information storage for users to save a file
	- The OS implements the abstract concept of a file by managing mass storage media and the devices that control them 
	- Files are normally organized into directories to make them easier to use
	- The OS is responsible for the following activities for file management: 
		1. Creating and deleting files 
		2. Creating and deleting directories to organize files
		3. Supporting primitives for manipulating files and directories 
		4. Mapping files onto mass sotrage
		5. Backing up files on stable (nonvolatile) storage media 
- **Mass Storage Management**: modern computer system use multiple types of secondary storages 
	- The OS is responsible for the following activities for secondary storage management
		1. Mounting and unmounted
		2. Free space managment 
		3. Storage allocation 
		4. Disk scheduling 
		5. Partitioning 
		6. Protection 
- **Cache Management**
	- Hardware or software component that stores data which might be used against soon
	- Cache hit: the requested data can be found in a cache 
	- Cache management is an important design problem
	- The movement of information between levels of a storage hierachy may be either implicit or explicit; depends on hardware diesgn 
		- Data transfer from cache to CPU and registers is usually a hardware fucntion 
		- Data transfer from disk to memory is usually controlled by the OS
- **Deadlock Management** 
	- Deadlocks between processes happen since limited number of resources must be shared
	- Processes are sharing resources for finishing their job
	- Four strategies for dealing with deadlock
		1. Ignore
		2. Detection and recover 
		3. Dynamic avoidance by careful allocation
		4. Prevention 
	- Four necessary conditions to avoid deadlock:
		1. Mutual exclusion
		2. Circular wait
		3. Hold and wait
		4. No preemptve
- **Input/Output**:
	- OS manages all kinds of I/O devices such as keyboards, monitors, printers
	- I/O subsystems: 
		- A memory mangement components (buffering, spooling, caching)
		- General device drivers
		- Drivers for specific hardware
		- Processor for specific hardware
	- OS controls I/O devices by:
		1. Issue commands to devices 
		2. Catch interrupts from devices 
		3. Handle errors

#### Operating System Structures 
- **Monolithic**: written as a collection of procedures, each of which can call any of the others whenever it needs to
	- Each procedure  in the system as a well-defined interface in tersm of parameters and results, each one is free to call any other one
	- Possible to have some structure for a monolihtic system 
		- Main program, service functions, utility functions
- **Layered Systems:** OS is divided into several layers and each later works on different rules: 
	1. Layer 0: process management 
	2. Layer 1: memory management
	3. Layer 2: inter-process managment
	4. Layer 3: input/output managment 
	5. Layer 4: user program 
	6. Layer 5: systerm operator process
- **Microkernels**: with the layered approacj, the designers have a choice where to draw the kernel-user boundary 
	- Achieve high reliability by splitting the OS up into small well-defined module 
	- Only one module runs in kernel mode and the rest run as users mode
- **Virtual Machine**: runs on bare hardware and does multiprogramming by providing several virtual machines 
	- Each virtual machine copies the bare heardware, including kernel/user mode, I/)
	- Different VMs can run on different OS
![[Screenshot 2023-04-01 at 23.53.41.png]]
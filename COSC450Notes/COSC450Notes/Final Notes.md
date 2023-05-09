### Lecture 23: Input/Output Management 

- **Input/Output**
	- OS controls all I/O devices by:
		1. Issue commands to devices (read/write)
		2. Catch interrupts from devices (when device is ready to read or write)
		3. Handle errors 
	- OS also provides interface betweeen the devices and the rest of the system 
- **Input/Output Devices** 
	- I/O devices can be divided into block devices, character devices, and others
	- **Block devices**: 
		- Stores information in fixed size blocks, each block has its own address
		- Common block size ranges from 512 bytes and 32 KB
		- The essential property of a block device is possible to read or write each block independently; needs a seek operation before read/write operation
		- EX: disk, CD ROM, SSD
	- **Character Devices** 
		- A character device delivers/accepts a stream of characters, without regard to any block structurre
		- It is not addressable and does not have any seek operation
		- EX: printers, network interfaces, mouse
	- **Others**: 
		- Clock: can only cause interrupt at a well-defined interval 
		- Memory mapped screen 
- **Device Controllers**: 
	- Most I/O units consist of a mechanical component, an electrical component, and a device driver 
		- Mechanical component: device itself
		- Electrical component: device controller (a chip on the parent board or a printing circuit card that can be inserted into a PCI expansion slot)
		- Device driver (software): interface between OS 
	- A **device controller** is a part of a computer system that plays an important role in the operation of that device; it functions as a bridge between the device and the OS
		- Any device connected to a computer is connected by a plug an d socket, and the socket is connected to a device controller
		- Each device controller has a local buffer and a command reegister; it communicates with the CPU through interrupts 
	- The device controller receives data from a connected device and stores it in a local buffer inside the controller before sending it through the bus. 
		- It then communicates the data with a **device driver**
		- For each **device controller** there is a **device driver** which is the standard intereface through which the device controller communicates with the OS through interrupts 
	- The interface between the controller and device is very low-level. 
		- EX: Disk controller (512 bytes per block)
			- The disk drive sends a serial bit stream starting with: 
				- Preamble (cylinder and sector number, sector size)
				- 4096 bit ($512*8$)
				- Check sum for error checking 
			- Disk controller's job is to: 
				- Convert the serial bit stream into a block of bytes
				- Error checking and error correction
				- Send to main memory 
	- How does the CPU communicate with controller? 
		- Each controller has a few registers used for communicating with the CPU
		- By writing into these registers, OS can command the device to deliver, accept data and to switch itself ono and off (ready to receive/send)
		- By reading from these registers, the OS an learn: what is the device state --> whether it is ready to accept a new command or not. 
		- Devices have a data buffer in the device controller for the OS to read/write from
			- EX: the common way for computers to display pixels on the screen is to have video RAM, which basically just a data buffer available for programs or OS to write into 
		- The issue is how the CPU communicates with control registers and device buffers: 
			- Non Memory Mapped I/O 
				- Each control register is assigned an I/O port number (8 bit or 16 bits)
				- The set of all I/O ports form the I/O port space, only the OS can access it,
				- By using special I/O instruction, the CPU can read in the control registeres and can write the content of a register
					- EX: IN REG, PORT; the CPU can read in control register PORT and store the result in CPU REG
					- OUT PORT, REG; the CPU writes the contents of REG to a control register
				- In non-mapped I/O scheme, the address space for memory and I/O are different
				- DIfferent instruction sets are used for communicate between CPU and meemory and between the CPU and I/O device controller registers
					- EX: 
						- IN R0, 4; reads the contents of I/O port 4 and puts it in R0
						- MOV R0, 4; reads the contents of memory word 4 and puts it in R0 
			- Memory Mapped I/O
				- Each controler register is assigned a unique memory address to which no memory is assigned (for buffer or register)
				- The assigned addresses are usually at the top of the address space 
			-  Hybrid Scheme
				- The memory-mapped I/O for data buffers and separate ports for the control registers is used in the Pentium 
				- When the CPU wants to read a word, either from memory or from the I/O port: 
					- The CPU puts the address on the bus's address line 
					- Asserts a read signal on the bus's control line 
					- The second signal line is used to tell whether the I/O or memory space is needed
					- Either I/O device or memory responds to the request based on the second signal line
			- Pure memory mapped I/O: 
				- Memory spaces are used for data buffer and registers of controller
				- Every memory module and every I/O device compares the address lines to the range of addresses that its services
				- If the addreses falls in its range, it responds to the request 
				- Advantage with memory mapped I/O: 
					- Since a non-memory mapped I/O system needs a special instruction set, the device driver cannot be written by a high level language such as C or C++
					- WIth memory-mapped I/O, the device driver can be written in a high level language 
					- No special protection mechanism is needed for user processes performning I/O - OS just checks wehtheer the user process tries to use the controller's space or not
					- Every instruction (that can reference memory) can also reference the control register
				- Disadvantage with memory-mapped I/O 
					- The trend in modern PCs is to have a dedicated, high speed memory bus between memory and the CPU
					- I/O devices have no way of seeing memory addresses as they go by on the memory bus, so they have no way of responding (need an extra mechanism to solve the problem)
- **Direct Memory Access**
	- Whether the CPU has memory-mapped I/O or not, it needs to addreess the device controllers to exchange data with them
	- The CPU can request data from an I/O controller one byte at a time
	- If the CPU needs to wait for completion of data transfer, it wastes the CPU time 
	- DMA controller can handle I/O independent from the CPU
	- The DMA is a capability provided by some computer bus architectures that allows data to be sent directly from an attached device (such as a disk drive) to the memory
	- The CPU is freed from invovlement with the data transfer, thus speeding up overall computer operation
	- EX: Disk read froom disk to memory without a DMA controller
		1. Disk controller reads in data bit by bit froom the disk until an entire block is in the controller's buffer
		2. It checks the checksum to verify any error
		3. If there is no error, controller causes an interrupt to get a service from the OS
		4. The service transfers data from the controller's buffer to main memory through the bus line
- **Interrupts Revisited**
	- When an I/O device finishes its job, it causes an interruot
	- The I/O device sends a signal for the interrupt service through the bus line
	- This signal is detected by the interrupt controller chip on the parent board
	- If no other interrupts are pending, the interrupt controller processes the interrupt immediately; if the interrupt controller is busy to handle other interrupt, the device is just ignored for the moment. And continues to assert an interrept signal on the bus until it is serviced by the CPU. 
	- The interrupt controller sends a number to the CPU which is used for the index of the interrupt vector
	- The signal from the interrupt controller causes the CPU to stop what it is doing and start handling the interrupt
	- The interrupt vector (table) has pointers where the interrupt service procedures is located
	- The program counter saves the location where the interrupt service procedure is located
	- Shortly after it starts running, the interrupt service procedure acknowledges the interrupt by writing a certain value to one of the interrupt controller's I/O ports which tells the controller that it is free to issue another interrupt. 

### Principle of I/O Software

- **Goal of I/O Software**: 
	1. Device independent: I/O software must be able to access any I/O devices
		- It is up to the OS to take care of the problems caused by different devices, which require different command sequences to read or write (non-memory I/O needs device dependent isntructure set for device driver)
	2. Uniform naming: name of a filee or device should be named with a string or an integer, not dependent on the type of device 
		- EX: in UNIX or LINUX, all disks can be integrated into the file system hierarchy 
			- A USB stick can be mounted on the top of the directory, so a file can copy from any disk to the USB stick with a path name. 
	3.  Error handling:
		- Error must be handled as close to the hardware as possible
		- I/O errors must be detected by the controller or the device itself
		- OS only takes care of upper level errors for I/O access
		- Only checks error by the RAM (through checksum)
	![[Screenshot 2023-05-04 at 08.12.25.png]]
	4. Synchronous vs. Asynchronous Transfer
		- **Synchronous**: blocks CPU until it finishes transfering data from a device to a different device 
		- **Asynchronous**: interrupt-driven transfer; once a transfer starts between devices, the CPU can take care of other processes until the interrupt arrives (ex. DMA)
	5. Buffering: 
		- For some devices, data cannot be stored directly in its final destination
		- It must be saved in a buffer and check errors or decoded proper form and then send to destination
			- Ex: OS cannot directly take care of data coming in off the network until it is stored in a buffer and examined
	6. Sharable vs. Dedicated Device I/O Software
		- Hard disk is able to share with multiple processes (multiple read/write heads for each disk)
		- Tape drive: have to be dedicated to a single user until that user is finished
- Fundamental ways I/O can be performed: 
	1. **Programmed I/O:** the CPU blocks for a job
		- Ties up the CPU full time until all of the I/O is done
		- Simple but has the disavntage of typing up the CPU full time
	![[Screenshot 2023-05-04 at 08.20.10.png]]
	![[Screenshot 2023-05-04 at 08.20.41.png]]
	2. **Interrupt-driven I/O:** CPU blocks for each step of a job
		- If a printer take 10ms to print one character, the CPU will sit in idle for 10ms wiaitng to be allowed to print the next character
		- Interrupt drive I/O lets the CPU do something else while waiting for the I/O device to become ready
		- Disdavantage is that an interrupt occurs on every character, wasting CPU time
	![[Screenshot 2023-05-04 at 08.23.10.png]]
	3. **I/O using DMA:** the CPU informs to the DMA, CPU works on another job. When the job is done, the DMA informs the CPU. The CPU continues the job. 
		- CPU initiates the I/O, the DMA takes over the I/O job, once the I/O finishes, the DMA issues an acknowledge for the interrupt, and the OS unblocks the process waiting for I/O. 
	![[Screenshot 2023-05-04 at 08.25.41.png]]
- Input/Output Software Layer 
	![[Screenshot 2023-05-04 at 08.26.19.png]]
	- Interrupt Handler: or known as an interrupt service routine, is a subroutine in an OS or device driver whpse execution is triggered by the reception of an interrupt. 
		- Have multitudes of functions, which vary based on the reason the interrupt sas generated and the speed at which the interrupt handler completes its task
		- Pressing a key on a computer keyboard, or moving the mouse, triggers interrupts that call interrupt handlers which read the key (aka mouse position) and copies the associated informatiion into the computer's memory 
		- Steps: 
			1. Save any registers for currently running processes
			2. Set up context switch for ISR
			3. Set up stack for the ISR
			4. Acknowledged interrupt controller
			5. Copy registers
			6. Run the ISR
			7. Select which process will run next
			8. Set up MMU context for the process to run next
			9. Load the new process's register
			10. Start running the new process
	- Device driver:
		- Since each I/O device has different roles, the number of registers and commands used in different I/O devices are different
		- A device driver is a device-specific code for controlling an I/O device 
		- A device driver is written by the device manufacturer
		- A device driver is the interface between OS and I/O controller 
		- In order to access the device's hardware, each of the device drivers has to be part of the OS kernel 
			- It is still possible to contruct a driver which runs in the user's space, with systme calls for reading and writing the device drivers (micro-kernel)
		- Modern OS's expect drivers to run in the kernel 
		- OS designer needs to consider an architecture that will allow driver installation on it
		![[Screenshot 2023-05-04 at 08.33.58.png]]
		- Standard interface: OS defines a standard interface for block and character devices 
			- A number of procedures that the rest of the OS can call to get the driver to do work on it 
			- An OS with a single binary program (UNIX) that contains all of the device driveers needs to be recompileded if a new driver is added 
			- In the PC, OS went over to a model where drivers where dynamically loaded into the system during execution
			- Different system uses different ways to load drivers. 
- **Hard Disk Drive (HDD)**
	- Modern disk drives are addressed as a large one-dimensional array of logical blocks
	- Size of the logical blocks is usually 512 bytes
	- At any given arm position, each of the heads can read (by rotation) an annular region called a track
	- All of the tracks for a given arm position form a cylinder
	- Each track is divded into some number of sectors
	- The one-dimensional array of logical blocks is mapped onto the sector of the disk sequentially 
	- You can convert a logical block number into a disk address that contains the cylinder number, a track number, and a sector number. 
	![[Screenshot 2023-05-04 at 08.39.10.png]]
	![[Screenshot 2023-05-04 at 08.39.24.png]]
	![[Screenshot 2023-05-04 at 08.39.35.png]]
	- Disk Scheduling 
		- One of the responsibilities of the OS is to use hardware efficiently
		- WIth disk drivers, the OS tries to minimize the disk access time
		- The major components of the disk access times: 
			- Seek time: time for the disk arm to move the heads to the cylinder containing the desired sector 
			- Rotation time: the time to rotate to the desired sector to the disk head
		- Disk scheduling algorithms are for scheduling the movement of the disk arm between the cylinders 
		- **First Come, First Serve**:
			- Ex. consider a disk queue with requests for I/O to blocks on cylinder 98, 183, 37, 122, 14, 124, 65, 67. If the disk head is initially at cylinder 53: 
				FCFS = 98, 183, 37, 122, 14, 124, 65, 67
				Total head movement: $(98-53) + (183-98) + (183-37) + (122-37)$
				 $+ (122-14) + (124-14) + (124-65) + (67-65)$
				 
			![[Screenshot 2023-05-04 at 08.45.44.png]]
		- **Shortest Seek Time First**: serves the request closest to the current location
			-  Ex. consider a disk queue with requests for I/O to blocks on cylinder 98, 183, 37, 122, 14, 124, 65, 67. If the disk head is initially at cylinder 53: 
				SSTF: 65, 67, 37, 14, 98, 122, 124, 183
				Total head movement: $(65-53) + (67-65) + (67-37) + (37-14)$
				 $+ (98-14) + (122-98) + (124-122) + (183-124)$
				 
			![[Screenshot 2023-05-04 at 09.20.45.png]]
		- SCAN: elevator scheduling; the disk arm starts at one end of the disk, and moves towards the other end 
			- Ex. consider a disk queue with requests for I/O to blocks on cylinder 98, 183, 37, 122, 14, 124, 65, 67. If the disk head is initially at cylinder 53: 
			Elevator: 37, 14, 0, 65, 67, 98, 122, 124, 183
			![[Screenshot 2023-05-04 at 09.23.44.png]]
		- C-SCAN: similar to elevator algorithm, move the head from one end of the disk to the other, when the head reaches the other end, it immediately retirns to the beginning of the disk without serving any of the requests on the the return trip. 
			- Ex. consider a disk queue with requests for I/O to blocks on cylinder 98, 183, 37, 122, 14, 124, 65, 67. If the disk head is initially at cylinder 53: 
			C-SCAN: 65, 67, 98, 122, 124, 183, 199, 0, 14, 37
			![[Screenshot 2023-05-04 at 09.26.36.png]]
		- LOOK: both elevator and C-SCAN move the disk arm across the full width of the disk. In practice, neither algorithm is implemented in this way. More commonly, the arm goes only as far as the final request in each direction, then it reverses direction immediately  without going all the way to the end of the disk 
			- This version of SCAN and C-SCAN algorithm are called LOOK and C-LOOK 
		![[Screenshot 2023-05-04 at 09.36.24.png]]
		![[Screenshot 2023-05-04 at 09.36.34.png]]
- **DIsk Management**
	- Disk formatting: the format consists of a series of concentric tracks, each track containing sectors, with short gaps between sectors 
		- Preamble: start with bit pattern, the cylinder number, sector number, and other information 
		- Data: size of data portion is determined by formatting program (512 bytes)
		- ECC: error correcting code; information used for error correction 
	- Bad Block Management: bad blocks should be managed by the OS 
		- MS-DOS (windows): *format* command does a logical format, scans the disk to find a bad block, and if one is found, a special value is written into the corresponding file allocation table entry 
		- SCSI (Small Computer System Interface) disk: used in workstations and servers
			- The disk controller maintains a list of bad blocks on the disk
			- The list is initialized during the low-level format at the factory
			- It is updated over the life of of the disk 
	- Swap Space Management: 
		- The swap space is used in various ways by different OSs. Based on the memory management algorithm. 
			- Swapping: need to swap entire process space
			- VIrtual memory: paging, segementation, segementation with paging
		- Swap space location: the location can be sarved out of the normal file system
			- Can be in separate partition 


### Solid State Driver 

- **Structure of an SSD**
	- Solid state drives are a flash-memory based data storage device. Bits are stored into cells, which are made of floating-gate transistors.
	- SSds are made of entirely electronic componnets, there are no mechnical components like in hard disk drivese
	- Voltages are applied to the floating-gate transistors, which is how bits are read, written, and erased; two solutions exist for writing transistors: 
		- NOR flash memory
			- Larger cell size
			- Faster to read
			- Slower to erase and write
			- Much more expensive 
			- Need more power consumption when power is on
			- Used in mobile phones, scientific instructments and medical devices 
		- NAND flash memory 
			- Their cells are wearing off, have limited lifespan
			- Smaller cell size
			- Slow to read
			- Faster to erase and write
			- Less expensive
			- Higher memory capacity 
			- Has lifespan 
			- Used in devices to which large files are frequently uploaded and replaced 
	- Each cell has a maximum number of program/erase cycles
	- NAND flash memory wears off and has a limited life span; the livespan of SSDs can be tremendously increaed 
	- Types of cells: 
		- Single level cell: transistors can only store 1 bit but have a longer lifespan 
		- Multiple level cell: transistors can store 2 bits, at the higher cost of higher latency and reduced lifespan compared to SLC
		- Triple level cell: transistors can store 3 bits, but at an even highere latency and reduced life span.
	- Having more bits for the same amount of transistors reduces the manufacturing costs. SLC based SSDs are known to be more reliable and have a longer life expectancy than MLC-based SSDs, but at a higher manufacturing cost. 
	- **NAND - Flash Pages and Blocks** 
		- Cells are grouped into a grid, called a block, and blocks are grouped into planes
		- The smallest unit through which a block can be read or written is a page
		- Pages cannot be erased individually, only whole places can be erased
		- The size of a NAND-flash page size can vary, most have sizes of 2KB, 4KB, 8KB, or 16KB.
		- Most SSDs have blocks of 128 or 256 pages, which means that the size of a block can vary between 256KB and 4MB. 
	![[Screenshot 2023-05-08 at 15.56.14.png]]
- **Architecture of an SSD**: 
	- Commands (read/write) come from the user through the host interface
	- The processor in the SSD controller takes the commands and passes them to the flash controller. 
	- SSDs have embedded RAM memory, generally for caching purposes and to store mapping information (mapping information)
	- The packages of NAND flash memory organized in gangs, over multiple channels 
- **Basic Operations in SSD (read, write, erase)**: 
	- Due to the organization of NAND-flash cells, it is not possible to read or write single cells individually
	- Memory is grouped and is accessed with very specific properties 
	- Reads are aligned on page size:
		- It is not possible to read less than one page at once
		- One can request just one byte from the OS, but a full page will bee retrived in the SSD, forcing a lot more data to be reead than necessary (read amplification)
	- Writes are aligned on page size: 
		- When writing to an SSD, writes happeen by increments of the page size. So even if a write operation affects only one byte, a whole page will be written anyway
		- Writing more data than necessary is write amplification
	- Pages cannot be overwritten
		- A NAND-flash page can be written to only if it is in the "free" state
		- When data is changed, the content of the page is copied into an internal register, the data is updated, and the new version is stored in a "free" page, an operation called "read-modify-write"
	- Erases are aligned on block size 
		- Pages cannot be overwitten, once they become stale, the only way to make them free again is to erase them
		- It is not possible to erase individual pages, and it is only possible to erase whole blocks at once - one page modification in a block needs the entire block moving to a free block
		- The erase command is triggered automatically by the garbage collection process in the SSD controller when it needs to reclaim stale pages to make free space 
		![[Screenshot 2023-05-08 at 16.54.12.png]]![[Screenshot 2023-05-08 at 16.54.48.png]]
	- **Write Amplification**: 
		- Since writes are aligned on page size, any write operation that is not both aligned on the page size and a multiple of the page size will require more data to be written than necessary, a concept called write amplification
		- Writing one byte will end up writing a page, which can amount up to 16KB from some models of SSD and be extremely inefficient 
		- Writing data in an unaligned way causes the pages to be read into cache before being modified and written back to the device, which is slower than directly writing pages to the disk.
		- This operation is known as read-modify-write
		- Never write less than a page
			- Avoid writing chunks of data that are below the size of a NAND-flash to minimize write amplification and prevrent read-modify-write operations
			- To maximize throughput, whenever possible, keep small writes into a buffer in RAM and when the buffer is full, perform a single large write to batch all of the small writes. 
	- **Wear leveling**:
		- NAND-flash cells have a limited lifespan due to their limited number of process/execute cycles
		- What if data was always read and written from the exact samt block; this block would quickly exceed its P/E cycle limit, wear off, and the SSD controller would mark it as unuseable 
		- One of the main goals of an SSD controller is to implement wear leveling by careful block selection for writing, which distributes P/E cycle as evenly possible among the locks by selecting blocks carefully when writing
		- Ideally, all blocks would reach their P/E cycle limits and wear off at the same time
- **Flask Translation Layer**
	- The main factor that made adoption of SSDs so easy is that they use the same host interface as HDDs
	- Although presenting an array of Logical Block Addresses (LBA) makes sense for HDDs as their sectors can be overwritteen, it is not fully suiteed to the way flash memory works 
	- For this reeason, an additional component is required to hihde the inner characteristics of NAND flash memory and expose only an array of LBAs to the host
	- This component is called the Flash Translation Layer and resides in the SSD controller; the FLT is critical and has two main purposes: logical block mapping and garbage collection
	- **Logical block mapping**:
		- Translates the logical block addresses from the host space into physical block addresses (PBA) in the physical NAND-flash memory space
		- This mapping takes the form of a table, which for any LBA gives the corresponding PBA
		- This mapping table is stored in the RAM of the SSD for speed of access, and is persisted in flash memory in case of power failuree. When the SSD powers up, the table is read from the persisted version and is reconstructured into the RAM of the SSD.
		![[Screenshot 2023-05-08 at 17.34.21.png]]

### Final Exam Review

- Instruction cycle: fetch, decode, execute
- Von neumann bottleneck
- History of OS:
	- First generation (vacuum tubes)
	- Second generation (transistors and batch system)
	- Third generation (IC; time sharing, multiprogramming, spooling)
	- Fourth generation (VLSI)
	- Fifth generation (mobile devices)
- System call
- OS structure
	- Monolithic 
	- Layered machine
	- Virtual machine
- Process management
	- Process models
	- Process termination
	- Process creeation
	- Process state
	- Process implmentation 
	- 
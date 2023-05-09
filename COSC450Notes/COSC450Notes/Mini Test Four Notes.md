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

### Mini Test Four Math Questions

**Question One**: Contiguous allocation and compacting a disk.
- Important items:
	1.  Copying a file takes a seek and rotation delay followed by a transfer at full speed
	2. Writing a file back takes the same thing 
- Assume: 
	1. **Rotation delay**: 4 msec
	2. **Seek delay**: 5 msec
	3. **Transfer rate**: 8MB/sec
	4. **Average file size**: 8KB

- How long does it take to read a single file into memory and copy it back into a new location? 
	- FORMULA = ((seek + rotation) + transfer time per file) * 2
	1. Translate the transfer rate to the correct units (bytes/msec)
		1. Use dimensional analysis 
	2. Find the time it takes for one file to be transfered (file/msec)
		1. Use the answer in the first part (1 move/(amount in bytes/msec))
		2. Convert file size to bytes
		3. Multiply out
	3. Plug the values into the formula 
- How long does it take to compact half of a 16GB disk? 
	1. Find the number of files in the disk 
		1. Size of disk/size of file 
	2. Multiply the number of files by the file transfer rate 

**Question Two**: I-nodes and finding the largest file size. 
- Important items: 
	1. I-nodes use the last block for a block containing disk addresses
- Assume: 
	1. **Number of direct addresses**: 10
	2. **Size of direct addresses** 4 bytes
	3. **Size of blocks**: 2 KB

- What is the largest file size? 
	1. Find the number of addresses per block
		1. Size of address = 4 bytes --> translate into bits 
		2. Translate block size into bits by multiplying by 8 
		3. Divide block size in bits by the size of address in bits, subtract 1
	2. Find the size of addresses per block
		1. Number of blocks $*$ size of block
	3. Find size of address blocks 
		1. Number of direct addresses $*$  size of block
	4. Add $2$ and $3$ to get the total file size 

**Question Three**: Storing/keeping track of free blocks with bitmap and linked lists 
- Assume: 
	1. **Block size:** 8KB 
	2. **Disk block numbers**: 64 bits 

- How many maximum blocks are needed to keep track of a 128GB disk with linked list? 
	1. Find the number of block numbers per block
		1. Size of block in bits/size of block number
		2. Subtract one
	2. Find the number of blocks on the disk 
		1. Size of disk/size of blocks 
	3. Divide the number of blocks by the number of block numbers per block to get the total number of blocks

- How many blocks are needed to keep track of a 128 GB disk with bitmap? 
	1. Find the number of blocks = number of bits in the bitmap 
		1. Size of disk/size of blocks
	2. Translate number 1 into bytes by dividing by $2^3$ 
	3. Divide the number of bytes in the bitmap by the number of bytes in the file to get the number of blocks 

- What is the maximum disk size supported by the OS? 
	1. Calculate the number of blocks multiplied by the block size
		1. Number of blocks is 2 to the power of the number of bits for the disk block number

**Question Four**: Comparing the amount of space free list vs. bitmap takes up. Disk addresses require $D$ bits. For a disk with $B$ blocks, $F$ of which are free, state the condition under which the free list uses less than bitmap. 

1. Find the amount of space required for a free list. 
	1. $F*D$ 
2. Find the amount of space required for a bitmap
	1. $B$ 
3. Find the condition where free list uses less than bitmap with a 16 bit disk address
	1. $F*D < B$
	2. $F*16 < B$ 
	3. $F/B * 16 < 1$ == $F/B < 1/16$ == $F/B < 6.25$ 
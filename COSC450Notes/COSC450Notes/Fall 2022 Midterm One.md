### Short Answer Questions

1. Modern operating systems have three types of schedulers: long term, short term, and memory schedulers. Why did the second generation OS not have short term schedulers? 
2. What are the two main functions of the OS for supporting multiprogramming? 
3. What is the race condition between processes? 
4. What is an effective short term scheduling algorithm essential? 
5. A major program with the priority scheduling algorithm used in a scheduler is starvation. What is the solution? 
6. What is the motivation of virtual memory? 
7. One design issue of the paging system is page size. Discuess advantages for smaller page sizes and bigger page sizes. 
8. Processes can use message queue or shared memory for interprocess communication. What is the main role of the OS for message queue and for shared memory? 
9. When a system uses virtual memoory, the virtual address does not go directly into the memory bus. Instead, it goes to the ___ that maps it to the physical memory address. 
10. The main goal of the batch system for selecting properl scheduling algorithms are maximizing throughput and CPU utilization and minimizing turnaround time. What are throughput and turnaround time? 

### Free Response Questions 

1. A computer system generates a 32-bit virtual address for a process. This system has 8GB of RAM and a page size of 8KB.
	- If each entry needs 64 bits per entry, calculate the possible size of the page table in bytes. 
	- Page frame number information for each page must be saved in the page table. How many bits does it need to save the page frame number information? 


2. Mr. Computer  tries to solve the race condition problem in the producer-consumer problem. He comes up with the following solution. Are there problems with it? If it solves the problem, show it. If it doesn't, write a situation where it would lead to a race condition scenario. ![[Screenshot 2023-04-02 at 19.11.44.png]]

3. A computer can generate a 32-bit virtual address for a process. This sytem has 4GB of RAM and a page size of 4KB. 
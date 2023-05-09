### Deadlock

1. **What is deadlock?** A set of processes competing for a finite set of resources which leads to processes forever sleeping as they enter the block state when there are not enough resources and other processes never release the resources. 
2. **Why do deadlocks occurs?** There are not enough resources for all processes in a system, but they must share the resources.  
3. **What is a resource allocation graph?** A graph containing two sets, one of vertices ($V$) and a set of edges ($E$). $V$ is partitioned into two sets: $P$, a set of processes, and $R$, a set of resources. 
			![[Pasted image 20230423113435.png]]
4. **What is the edge from a process to a resource?** A process P requesting the resource R 
5. **What is the edge from a resource to a process?** The resource R that the process P currently holds. 
6. **Give an example of a deadlock scenario.** There are two processes, $P_1$ and $P_2$, who are working on their jobs and 
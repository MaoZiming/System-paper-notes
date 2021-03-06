# System Paper Notes

<img src="https://pbs.twimg.com/media/Dd7yLMrVwAAdDGT.jpg" alt="drawing" width="250"/>

I will read more papers in this summer!!

## Distributed Systems

I read those paper before but I think I will re-read them again!  
And jot down some notes!

### Raft (ATC'14)

### Chubby (OSDI'06)

### Delos (OSDI'20)

### CopySet (ATC'13)

### Dynamo (SOSP'07)

### Spanner (OSDI'12)

### GFS

### MapReduce

### Spark

### Dryad

### Naiad

### DStreams

### IOFlow

### BigTable

### RAMCloud

### FaRM

### Borg

### Mesos

### DRF

### Carbyne

### CAP

### Impala

### Dremel

### Succinct

### Tensorflow

### Ray

## Resource Disaggregation

cuz I will be working on this...

### MIND [[Link](https://arxiv.org/pdf/2107.00164.pdf)]

* Place memory management logic in the network fabric with programmable switches; desgined in-network cache coherence protocol
* Prior: processes are limited to compute resources on a single compute blade to avoid cache coherence traffic over the network
* Transparent Designs: managing metadata either at compute blade or memory blade, suffer from sequential remote requests. Non-transparent designs: different compute blades do not share memory, limiting transparent compute elasticitiy.
* Challenges: limited memory on switch ASICs to store page tables; Switch ASICs only permit a few cycles of limited computations per packet; Placing logic and metadata for decoupled memory and compute
* Proposed: global virtual address space, domain-based memory protection, adapted directory-based MSI coherence to the in-network setting, novel Bounded Splitting algoirthm that dynamically sizes memory regions.
* Decouple address translation and protection, instead of using fixed sized pages for both in traditional virtual memory
* Range partitions virtual address space across different memory blades, such that the entire virtual address space maps to a contiguous range of physical address space. 
* Virtual memory area (vma), the basic unit of protection, is identified by the base virtual address and length of the area
* Mind supports protection domains, identify the entity that may or may not have permission to access a particular memory region, and permission classes, which identify what the entity can do to the memory region.
* Decouple the granularity of cache (and memory) accesses from the granularity at which cache coherence is performed. This allows memory accesses to be performed at finer granularities, while directory entries are tracked at coarser granularities with the coherence protocol.
* Bounded Splitting Algorithm: Tracks false invalidation count for every region, if any region has a false validation count greater than a threshold in an epoch, it splits the region into two equal halves and creates a new directory entry. 


## Resource sharing / Virtualization

### LightVM [[Link](http://cnp.neclab.eu/projects/lightvm/lightvm.pdf)]

* Trade-off between containers and the security issues surrounding them AND the burden coming from a heavyweight, VM-based platforms. 
* Requirements (fast instantiation, high instance desntiy - thousands of containers on a single host, paused and unpaused quickly)
* Lightweight VMs (Unikernels - tiny virtual machines with minimalistic OS linked directly with target applications; requires porting of applications to a minimalistic OS, Tinyx - automated build system that creates minimalistic Linux VM images along with an optimized Linux kernel.)
* Xen: hypervisor manages basic resources such as CPUs and memory, creates a special virtual machine: Dom0; Dom0 hosts XenStore, a proc-like central registry that keeps track of management information such as which VMs are running and information about devices.
* Main overhead for Xen: the XenStore interaction and the device creation. 
* Forego the use of the XenStore for operations such as creation, pause/unpause and migration. The hypervisor already acts as a centralized store, so we can extend its functionality to implement our (no XenStore) mechanism. Modify hypervisor to create new device memory page for each new VM. 
* A VM create command issued does not actually need to run at VM create time. VMs can be pre-executed and off-loaded from the creation process. The _prepare_ phrase is responsible for functionality common to all VMs. Offload this functionality to the chaos daemon, which generates a number of VM shells and places them in a pull.

### Container-based OS Virtualization [[Link](http://www.cs.toronto.edu/~demke/2227/S.14/Papers/p275-soltesz.pdf)]

* Old 2007 EuroSys Paper
* Hypervisor: creates and manages VMs
* Comparing hypervisor technology that isolate VMs at the hardware abstraction layer with container-based operating-system (COS) technology that isolate VMs at the system level.
* Fault Isolation: for hypervisors there is a narrow interface to events and virtual device, whereas for COSs there is the wide system call ABI. Arguably it is easier to verify the narrow interface, which implies that the interface exposed by hypervisors are more likely to be correct
* COS rely on a single underlying kernel image, they are not able to run multiple kernels like hypervisors can. 
* The COS approach to security isolation directly involves internal OS objects (PIDs, UIDs, etc.) (1) separation of name spaces (2) access controls. Through this _contextualization_ the global identifiers become per-VM global identifiers. 

### LeapIO [[Link](https://ucare.cs.uchicago.edu/pdf/asplos20-LeapIO.pdf)]

* Observation: cloud storage stack is extremely resource hungry. Experiments suggest that the cloud provider may pay a _heavy tax_ for storage: 10-20% of x86 cores may have to be reserved for running storage functions. Storage functions cannot be fully accelerated in hardware.
* LeapIO allows the storage services to portably leverage ARM co-processors. 
* Runtime provides a uniform address space across the x86 and ARM cores. (1) maps NVMe queue pairs across hardware/software boundaries - between guest VMs running on x86 and service code offloaded to the ARM cores. (2) provides an efficient data path that alleviates unnecessary copying across the sfotware components via _transparent address translation_ across multiple address space.
* _Quite difficult to read_

### FairRide [[Link](https://www.usenix.org/system/files/conference/nsdi16/nsdi16-paper-pu.pdf)]

* Fair allocation of memory cache for multiple users with shared files: surprisingly, no memory allocation policy can provide all three desirable properties (isolation-guarantee, strategy-proofness, and Pareto-efficiency) that are typically achievable by other types of resources, e.g. CPU or network
* A cache file can be accessed by multiple users at a time and isolating cache leads to multiple copies of such shared files. We find such _non-exclusive sharing_ to be a defining aspect of cache allocation, while other resources are typically exclusively shared. 
* Global sharing policies lack isolation-guarantee and strategy-proofness, while static isolation (such as through a hypervisor) is not Pareto-efficient and leads to poor resource utilization.
* _Probabilistic blocking_ probabilistically disallowing a user from accessing a cached file if the file is not cached on behalf of the user.
* Redit or Memcached does not provide any guarantee for performance isolation. It is easy for a strategic user to improve her performance and hurt others (by 2.9x performance gap) by making spurious access to files.
* Default answer in cloud cache is to setup a separate caching instance per user or per application.
* "free ride": when files are shared, a user can "free ride?" files that have been already cached by other users.
* Probabilistically blocking accessing files cached by _other users_, with $\frac{1}{n_j + 1}$, where $n_j$ is the number of other users caching the file
* Introduced _expected delaying_ to approximate the expected effect of _probabilistic blocking_. The wait time should be set as the expected delay a user would experience if she is probabilistically blocked by the system.

### FairCloud [[Link](https://dl.acm.org/doi/pdf/10.1145/2342356.2342396)]

* The network allocation of a VM _X_ depends not only on the VMs running on the same machine with _X_, but also on the other VMs that _X_ communicates with and the cross-traffic on each link used by X.
* Hard tradeoff between min-guarantee (minimum network bandwidth the tenants can expect for each VM they buy) and network proportionality (network resources should be divided among tenants in proportion to their payments.) Even without requiring min-guarantees, there is a tradeoff between network proportionality and high utilization.
* Proportional Sharing at Link-Level (PS-L): link proportionality; consider a model in which each switch implements weighted fair queueing and has one queue for each tenant.
* Proportional Sharing at Network-level (PS-N): the communication between the VMs has the same total weight through the network irrespective of the communication pattern between the VMs in Q.

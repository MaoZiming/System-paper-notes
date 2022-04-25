# System Paper Notes

<img src="https://pbs.twimg.com/media/Dd7yLMrVwAAdDGT.jpg" alt="drawing" width="250"/>

I will read more papers in this summer!!

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

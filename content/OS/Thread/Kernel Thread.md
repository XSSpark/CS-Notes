---
Author:
  - Xinyang YU
Author Profile:
  - https://linkedin.com/in/xinyang-yu
tags:
  - OS
Creation Date: 2023-09-03T16:14:00
Last Date: 2024-01-23T17:27:58+08:00
References: 
---
## Abstract
---
![[thread_implementation_in_kernel_space.png|200]]
- Put the [[Thread]] entirely in [[Kernel Space]]
- 1 to 1 thread mapping with program inside the [[User Space]]
- Thread implementation is inside [[Kernel]]





## Pro
---
### Keep All Threads Busy
- If one [[Thread]] in a [[Process (进程)]] causes a [[Page Faults]], the [[Kernel]] can easily check to see if the process has any other runnable threads, and if so, run one of them while waiting for the required page to be brought in from the disk
- Or Kernel can get thread from other process


## Cons
---
### Resource Intense
- All calls that might block a [[Thread]] are implemented as [[System Call (系统调用)]], at considerably greater cost than a call to a [[User Thread#Runtime System]] procedure
</br>

- Relatively greater cost of creating and destroying [[Thread]] in the [[Kernel]]
>[!tip] Recycling threads
>- When a thread is destroyed, it is marked as not runnable, but its kernel data structures are not otherwise affected
>- When a new thread must be created, an old thread is reactivated, saving some overhead
>- Thread recycling is also possible for [[User Thread]], but since the thread-management overhead is much smaller, there is less incentive to do this
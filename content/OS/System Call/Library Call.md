---
Author:
  - Xinyang YU
Author Profile:
  - https://linkedin.com/in/xinyang-yu
tags:
  - OS
Creation Date: 2023-09-11T21:10:44
Last Date: 2024-01-31T09:12:00+08:00
References: 
---
## Abstract
---
- Some written in [[Assembly language]]
- Can be traced by `lstrace`
- Built on top of [[System Call (系统调用)]]

>[!attention] Privilege Mode
>Doesn't switch to [[Privilege Level#Kernel Mode]], always runs in [[Privilege Level#User Mode]]
## Benefits
---
### Abstraction
- Make it possible to make **System Call (系统调用)** from user program written in languages like Java and C without considering the underlying [[Instruction Set Architecture (ISA)]]

## Terminologies
---
### Library Procedure
- The underlying codes that carry out [[Library Call]]
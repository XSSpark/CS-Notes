---
Author:
  - Xinyang YU
Author Profile:
  - https://linkedin.com/in/xinyang-yu
tags:
  - computer_organisation
Creation Date: 2023-12-17, 19:58
Last Date: 2024-01-15T12:39:01+08:00
References: 
---
## Abstract
---
- Provides standardised way to express measurements, allowing for easy comparison of values

## Bit
---
- Holds a value of `0` or `1`
</br>

- Let `n` be the number of bits we have, where `n>=1`
- With `n` bits, we can represent $2^{n}$ values
$$
2^{n}= 2^{n-1} + 2^{n-2} + \cdots + 2^{0}
$$

>[!example] Given 2 bits
>`n` is $2$
>- `2^n = 4`, 4 values: 00, 01, 10, 11 which are corresponding to 0,1,2,3
### Maximum value of n bits
**Approach 1:** $2^{n} -1$

**Approach 2:** $2^{n-1} + \ldots + 2^0$
</br>

>[!example] Given 2 bit
>`n` is $2$
>- **Approach 1:** `2^2 - 1 = 3`
>- **Approach 2:** `2^1 + 2^0 = 3`

### Bit String
- A collection of 0s & 1s
## Byte
---
- A group of 8 [[#Bit]]

## Word
---
- The size of a word can vary depending on the [[Instruction Set Architecture (ISA)]]
- It typically represents the amount of data that the [[CPU]] processes in a single [[Operation]]
- Common word sizes are 16 bits, 32 bits, or 64 bits


## Qubit
---
- Waiting to be explored
---
Author:
  - Xinyang YU
Author Profile:
  - https://linkedin.com/in/xinyang-yu
tags:
  - bash
Creation Date: 2023-08-07T16:32:55+08:00
Last Date: 2024-01-12T16:21:03+08:00
References: 
---
>[!example] Transfer a file to another server with [[SSH Key Pair]]
>```bash
>scp -i <SSH_KEY_PAIR> <FILE>  <SERVER_HOSTNAME>@<SERVER_IP>:<DESTINATION_PATH>
>```
---
layout: post
title: Proving Grounds Box - PC
image: /assets/img/boxes/hacker.jpg
categories: [Pentesting, Proving Grounds]
tags: [Hacking,PenetrationTesting,CTF (Capture The Flag),ProvingGrounds,Walkthrough,PrivilegeEscalation,Vulnerability,Exploit,RPCService]

---

In this writeup i am going to be presenting a walkthrough for  a proving grounds box called `pc`

It has been rated `intermediate` by the community but i think it should be classified as `easy` because the initial foothold is not needed and its all about privilege escalation.

- I begin by running a port scan using rustscan

`rustscan -a <host>`

![Untitled](/assets/img/boxes/Untitled.png)

- We get 2 open ports `8000` and `22`
- `8000` -> Web server http
- `22` -> SSH
- We go to the website hosted on 8000 and see that it already has a `tty webshell` and we can interact directly with the system

![Untitled](/assets/img/boxes/Untitled%201.png)

- Upon further enumeration i come across a weird python file which is named as RPC which is a remote procedure call service
    
    ![Untitled](/assets/img/boxes/Untitled%202.png)
    
- Analysing the code suggests that the RPC service is running on a local port
    
    ![Untitled](/assets/img/boxes/Untitled%203.png)
    
- A quick search for existing exploits shows a vulnerability that can be used to privilege to root

**Exploit used** : [`https://github.com/ehtec/rpcpy-exploit/blob/main/rpcpy-exploit.py`](https://github.com/ehtec/rpcpy-exploit/blob/main/rpcpy-exploit.py)

I made the exploit python file and the following set of commands will give us root.

```bash
chmod +x exploit.py
python3 exploit.py
#We can now use GTFObins to get a payload
#This example creates a local SUID copy of the binary 
#and runs it to maintain elevated privileges. 
#To interact with an existing SUID binary.
 bash -p 
```

![Untitled](/assets/img/boxes/Untitled%204.png)

With this we successfully get root privileges and hence the box is pwned.

This is my first box writeup and I hope i have done a decent job at it, i have tried and kept it as simple and short as possible.

Thank you!
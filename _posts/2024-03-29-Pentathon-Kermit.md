---
layout: post
title: Kermit Box Walkthrough
image: https://live.staticflickr.com/65535/53619367834_cdc6d786b1_m.jpg
comments: True
categories: [Pentesting, CTF]
tags: [pentesting, Red teaming, cybersecurity, offsec] 

---

# Kermit

# Pentathon 2024

This ctf had 2 pentesting challenges, i managed to get first blood for both user flag and root flag out of a total 5k+ teams.

**Initial Enumeration**

An Nmap scan was was conducted against the machine in question. Through this, it was revealed that 2 ports were open.

- Port 22: SSH
- Port 80: HTTP

→ On visiting the webpage, this page was found.

![Untitled](/assets/img/Kermit%20dcd423d39b634bbaa8368d7f4126fd32/Untitled.png)

Submitting anything on this page, we get redirected to a `complaints.php page`. 

Upon directory fuzzing, 2 more endpoints were found.

- `/resources/app`
- `/actual`

On visiting the first path, we are given a binary file named ‘app’. On reversing this file, we find that it requires a key to work and can read contents of files present on the machine. But the key input is vulnerable to buffer overflow

The original intent of the actual page was to give show Exif data for images but it was found that the `file uploading is not filtered` and any file can be uploaded to the server.

![Untitled](/assets/img/Kermit%20dcd423d39b634bbaa8368d7f4126fd32/Untitled%201.png)

`The uploaded files were also accessible at /uploads/filename`

So, we created a PHP web shell using pentest-monkey and uploaded the following to the server.

![Untitled](/assets/img/Kermit%20dcd423d39b634bbaa8368d7f4126fd32/Untitled%202.png)

Through this we were able to get the shell.

![Untitled](/assets/img/Kermit%20dcd423d39b634bbaa8368d7f4126fd32/Untitled%203.png)

We then locate the ‘app’ binary on the server.

Through our previous findings, we exploit the binary using a simple bufferoverflow and read the contents of the id_rsa through which we can ssh into the server.

![Untitled](/assets/img/Kermit%20dcd423d39b634bbaa8368d7f4126fd32/Untitled%204.png)

`Chmod 600`  on the file and then run the command.

![Untitled](/assets/img/Kermit%20dcd423d39b634bbaa8368d7f4126fd32/Untitled%205.png)

We are then able to access the user flag.

## Root

**Now we have to escalate to root, I found that another webpage has been hosted on
localhost:8000,**

![Untitled](/assets/img/Kermit%20dcd423d39b634bbaa8368d7f4126fd32/Untitled%206.png)

but since it is hosted on localhost, we have to perform port forwarding to our machine.

![Untitled](/assets/img/Kermit%20dcd423d39b634bbaa8368d7f4126fd32/Untitled%207.png)

This means that any `request made on our local kali on port 8000` of localhost will be forwarded to our target machine

- Now I identified `cacti server` on the webpage and set it up via the web browser.

![Untitled](/assets/img/Kermit%20dcd423d39b634bbaa8368d7f4126fd32/Untitled%208.png)

A simple search revealed that it is vulnerable to several rce based on sqli, I used metasploit to exploit it further

![Untitled](/assets/img/Kermit%20dcd423d39b634bbaa8368d7f4126fd32/Untitled%209.png)

I have set the rhosts and rport as 127.0.0.1:8000 because it will get forwarded because of our ssh port forward

We also change the targeturi to / because that is where cacti is hosted.

Alll that was left is to run the exploit and `boom we got root shell.`

![Untitled](/assets/img/Kermit%20dcd423d39b634bbaa8368d7f4126fd32/Untitled%2010.png)
---
layout: post
title: Bsides Mumbai CTF
image: /assets/img/Chandigarh/mum.png
comments: True
description: BSides Mumbai CTF 2024
categories: [CTF]
tags: [CTF, cybersecurity] 

---
# BSides Mumbai CTF

## Challenge 1: Feedback Bonus

Just a feedback  Form

## Challenge 2: Welcome

![Untitled](/assets/img/BSides%20Mumbai%20CTF%205b2b9b2ec9614c368ef8a75ee3973733/Untitled.png)

Direct flag given in source code.

The left png file was corrupted so we fix the header as given:

`89 50 4E 47` → Magic Bytes for PNG

![Untitled](/assets/img/BSides%20Mumbai%20CTF%205b2b9b2ec9614c368ef8a75ee3973733/Untitled%201.png)

![Untitled](/assets/img/BSides%20Mumbai%20CTF%205b2b9b2ec9614c368ef8a75ee3973733/Untitled%202.png)

![Untitled](/assets/img/BSides%20Mumbai%20CTF%205b2b9b2ec9614c368ef8a75ee3973733/Untitled%203.png)

## Challenge 3: Vadapaooo

In this challenge we get a corrupted jpg file, lets inspect it in a hexeditor and try to fix the image.

We have to insert 3 bytes ahead of the file and add the magic bytes `FF D8 F8`

![Untitled](/assets/img/BSides%20Mumbai%20CTF%205b2b9b2ec9614c368ef8a75ee3973733/Untitled%204.png)

Now using stegseek we can extract the hidden password and exe file

![Untitled](/assets/img/BSides%20Mumbai%20CTF%205b2b9b2ec9614c368ef8a75ee3973733/Untitled%205.png)

![Untitled](/assets/img/BSides%20Mumbai%20CTF%205b2b9b2ec9614c368ef8a75ee3973733/Untitled%206.png)

## Challenge 4 : Sanity Check

Simple flag, it was there in the discord browse channels

![Untitled](/assets/img/BSides%20Mumbai%20CTF%205b2b9b2ec9614c368ef8a75ee3973733/Untitled%207.png)

![Untitled](/assets/img/BSides%20Mumbai%20CTF%205b2b9b2ec9614c368ef8a75ee3973733/Untitled%208.png)

## Challenge 5 : Shadow Realm

![Untitled](/assets/img/BSides%20Mumbai%20CTF%205b2b9b2ec9614c368ef8a75ee3973733/Untitled%209.png)

In this challenge we were given 2 files i.e passwd and shadow files. These are linux password files where every username and password is stored.

![Untitled](/assets/img/BSides%20Mumbai%20CTF%205b2b9b2ec9614c368ef8a75ee3973733/Untitled%2010.png)

In this shadow file we can see the pattern `$y$` this indicates it is a yescrypt cipher. We can now use `unshadow` and then run `johntheripper` to crack the password

![Untitled](/assets/img/BSides%20Mumbai%20CTF%205b2b9b2ec9614c368ef8a75ee3973733/Untitled%2011.png)

`flag{spongebob}` → Has been found

## Challenge 6: Data Breach

On initial analysis of the given files, we can see that the zip file is password protected and we have been given a bunch of other files.

![Untitled](/assets/img/BSides%20Mumbai%20CTF%205b2b9b2ec9614c368ef8a75ee3973733/Untitled%2012.png)

I now checked the authors of the docs using exiftool

![Untitled](/assets/img/BSides%20Mumbai%20CTF%205b2b9b2ec9614c368ef8a75ee3973733/Untitled%2013.png)

The usernames were weird so i decided to extract all the usernames and use it as a wordlist to crack the zip file.

```bash
exiftool -a * | grep "Creator" | awk '{print $3}'
```

![Untitled](/assets/img/BSides%20Mumbai%20CTF%205b2b9b2ec9614c368ef8a75ee3973733/Untitled%2014.png)

We then used zip2john to convert it to a john hash format

![Untitled](/assets/img/BSides%20Mumbai%20CTF%205b2b9b2ec9614c368ef8a75ee3973733/Untitled%2015.png)

`flag{Ch@rlotte}`

## Challenge 7: Common

Login, as admin:admin in ctfd
Flag would be found in access tokens, its deleted now

![Untitled](/assets/img/BSides%20Mumbai%20CTF%205b2b9b2ec9614c368ef8a75ee3973733/Untitled%2016.png)

## Challenge 8: Potential APT

We got 2 files, `SAM` and `System` files, these pertain to windows credential storage, with 2 files we can extract the `NTLM` hashes of the users on the machine

![Untitled](/assets/img/BSides%20Mumbai%20CTF%205b2b9b2ec9614c368ef8a75ee3973733/Untitled%2017.png)

We save these to a file and use crackstation to check for cracked hashes of littlemermaid.

![Untitled](/assets/img/BSides%20Mumbai%20CTF%205b2b9b2ec9614c368ef8a75ee3973733/Untitled%2018.png)

## Challenge 9: Many Doc

![Untitled](/assets/img/BSides%20Mumbai%20CTF%205b2b9b2ec9614c368ef8a75ee3973733/Untitled%2019.png)

Out of these files, [message.doc](http://message.docs)x was not opening for me normally so i decided to inspect it with binwalk

![Untitled](/assets/img/BSides%20Mumbai%20CTF%205b2b9b2ec9614c368ef8a75ee3973733/Untitled%2020.png)

I can see vba script lets inspect it further for macros attacks.

VBAproject.bin was an interesting binary file and i found a base64 string in it

![Untitled](/assets/img/BSides%20Mumbai%20CTF%205b2b9b2ec9614c368ef8a75ee3973733/Untitled%2021.png)

![Untitled](/assets/img/BSides%20Mumbai%20CTF%205b2b9b2ec9614c368ef8a75ee3973733/Untitled%2022.png)

## Challenge 10: Rabbit Holes

Unfortunately i had deleted the file but doing strings and grepping will give you the flag

## Challenge 11: FindMe

Packetwhisperer from defcon was used to encrypt data in DNS traffic

![Untitled](/assets/img/BSides%20Mumbai%20CTF%205b2b9b2ec9614c368ef8a75ee3973733/Untitled%2023.png)

Using mode 3 cloudfront prefixes in random subdomains, we can get the flag extracted.

![Untitled](/assets/img/BSides%20Mumbai%20CTF%205b2b9b2ec9614c368ef8a75ee3973733/Untitled%2024.png)

[https://github.com/TryCatchHCF/PacketWhisper](https://github.com/TryCatchHCF/PacketWhisper)

## Challenge 12: BabyPass

/FLAG endpoint bypasses /flag filter

![Untitled](/assets/img/BSides%20Mumbai%20CTF%205b2b9b2ec9614c368ef8a75ee3973733/Untitled%2025.png)

## Challenge 13: A Bucket load of samosa

```powershell
import requests

# Load the custom dictionary
url = "https://naughtydev-company-files.s3.eu-central-1.amazonaws.com/naughtydev-bucketnames.txt"
response = requests.get(url)
dictionary = response.text.splitlines()

# Check the buckets
for word in dictionary:
    bucket_name = f"{word}.naughtyide.s3.amazonaws.com"
    print(bucket_name)
    bucket_url = f"http://{bucket_name}"
    response = requests.get(bucket_url)
    if response.status_code == 200:
        print(f"Bucket found: {bucket_url}")
        print(response.text)
```

This code, enumerates all the buckets based on the pattern.

![Untitled](/assets/img/BSides%20Mumbai%20CTF%205b2b9b2ec9614c368ef8a75ee3973733/Untitled%2026.png)

[`http://social.naughtyide.s3.amazonaws.com`](http://social.naughtyide.s3.amazonaws.com/)

![Untitled](/assets/img/BSides%20Mumbai%20CTF%205b2b9b2ec9614c368ef8a75ee3973733/Untitled%2027.png)

## Challenge 14: Lottery

```python
import concurrent.futures
from pwn import *

context.log_level = 'info'

def find_number_combination(io):
    io.sendlineafter(b"lottery?", b"-1")

    for a in range(1, 10):
        for b in range(1, 10):
            for c in range(1, 10):
                for d in range(1, 10):
                    for e in range(1, 10):
                        res = io.recvuntil("spaces:")
                        if not b"Try again" in res and not b"number 1" in res:
                            print(res)
                            return

                        io.sendline(f"{a} {b} {c} {d} {e}")

def runit():
    io = remote("143.110.176.49", 1024)
    find_number_combination(io)

# Number of workers you want to use
num_workers = 100

# Using ThreadPoolExecutor to parallelize the function
with concurrent.futures.ThreadPoolExecutor(max_workers=num_workers) as executor:
    # List of your functions to be executed in parallel
    futures = [executor.submit(runit) for _ in range(num_workers)]

    # Getting results if your functions return something
    results = [future.result() for future in futures]
    
```

Made this code to bruteforce the code using concurrent threads.

![Untitled](/assets/img/BSides%20Mumbai%20CTF%205b2b9b2ec9614c368ef8a75ee3973733/Untitled%2028.png)

## Challenge 15: Jail Shell

I wont lie but i took reference from this writeup as the chall was similar

[https://github.com/nikosChalk/ctf-writeups/blob/master/uiuctf22/jail/a-horse-with-no-names/README.md](https://github.com/nikosChalk/ctf-writeups/blob/master/uiuctf22/jail/a-horse-with-no-names/README.md)

```python
(lambda:__builtins__.__import__(open.__name__.__getitem__(0).__add__(set.__name__.__getitem__(0))).system(chr.__name__.__getitem__(0).__add__(abs.__name__.__getitem__(0)).__add__(type.__name__.__getitem__(0)).__add__(str(hash).__getitem__(9)).__add__(float.__name__.__getitem__(0)).__add__(list.__name__.__getitem__(0)).__add__(abs.__name__.__getitem__(0)).__add__(globals.__name__.__getitem__(0)).__add__(hash.__doc__.__getitem__(151)).__add__(type.__name__.__getitem__(0)).__add__(hex.__name__.__getitem__(2)).__add__(type.__name__.__getitem__(0))))()

```
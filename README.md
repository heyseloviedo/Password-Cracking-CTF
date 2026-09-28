# Password Cracking CTF

## Objective

The objective of this project was to recover plaintext passwords from provided password hashes using Kali Linux, Hashcat, and the RockYou wordlist. The challenge required identifying the likely hash format, preparing the provided hashes, and performing a dictionary attack to match the hashes against known passwords from the RockYou breach.

### Skills Learned

- Identified password hashes based on their format and length.
- Used Kali Linux command-line tools to prepare files for password cracking.
- Worked with compressed wordlists and extracted `rockyou.txt`.
- Used Hashcat to perform a dictionary attack against MD5 hashes.
- Interpreted Hashcat output to confirm successful password recovery.
- Improved understanding of password hashing, wordlist attacks, and basic password-cracking workflows.

### Tools Used

- Kali Linux for the password-cracking environment.
- Hashcat for testing candidate passwords against the provided hashes.
- RockYou wordlist as the source of candidate plaintext passwords.
- Nano for creating the hash input file.
- Linux terminal commands such as `ls`, `cat`, and `gzip`.

## Steps

### Step 1: Review the Provided Hashes

I first reviewed the provided password ciphertexts. Each value contained 32 hexadecimal characters, which is a format commonly associated with MD5 hashes. Based on that pattern, I used MD5 as my initial hash-type hypothesis.

I saved the hashes into a text file named `hashes.txt`, with one hash per line.

```bash
nano hashes.txt
```

After saving the file, I verified the contents with:

```bash
cat hashes.txt
```

*Ref 1: The provided hashes were stored in `hashes.txt` so Hashcat could process them as a group.*

---

### Step 2: Prepare the RockYou Wordlist

The challenge description mentioned that the recovered plaintext passwords appeared to overlap with passwords from the RockYou breach. Because of that clue, I checked Kali Linux for the RockYou wordlist.

```bash
ls /usr/share/wordlists/
```

The wordlist was available as:

```text
rockyou.txt.gz
```

The `.gz` extension meant that the file was compressed, so I decompressed it with:

```bash
sudo gzip -d /usr/share/wordlists/rockyou.txt.gz
```

After decompression, the file was available as:

```text
/usr/share/wordlists/rockyou.txt
```

*Ref 2: RockYou was located in Kali Linux and decompressed so Hashcat could use the plaintext password list.*

---

### Step 3: Run Hashcat

I used Hashcat to test the hashes against passwords contained in the RockYou wordlist.

```bash
hashcat -m 0 -a 0 hashes.txt /usr/share/wordlists/rockyou.txt
```

In this command:

- `hashcat` starts the password-cracking tool.
- `-m 0` tells Hashcat to treat the hashes as MD5.
- `-a 0` selects a straight dictionary attack.
- `hashes.txt` contains the target hashes.
- `/usr/share/wordlists/rockyou.txt` provides the candidate passwords.

*Ref 3: Hashcat was configured to test MD5 hashes using passwords from the RockYou wordlist.*

---

### Step 4: Review the Results

Hashcat successfully recovered all of the hashes in the challenge. The final output showed:

```text
Status...........: Cracked
Hash.Mode........: 0 (MD5)
Recovered........: 5/5 (100.00%) Digests
```

The recovered plaintext passwords appeared next to their matching hashes in the terminal output.

*Ref 4: Hashcat completed the dictionary attack successfully and recovered all 5 passwords.*

---

## Result

The challenge was completed successfully by identifying the hashes as compatible with MD5 mode in Hashcat and using the RockYou wordlist to recover all five plaintext passwords.

## Screenshots

Screenshots from the Kali Linux terminal can be added here to document each stage of the process:

- Creating and verifying `hashes.txt`
- Locating and decompressing `rockyou.txt.gz`
- Running Hashcat
- Final `Recovered: 5/5` results

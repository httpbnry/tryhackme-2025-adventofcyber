# ROOM 9 - Passwords - A Cracking Christmas (httpbnry)

The room context is the following:

As the festive balance destabilizes between Easter and Christmas, Sir Carrotbane uncovers encrypted PDF and ZIP files labeled **“North Pole Asset List”** within The Best Festival Company’s servers. Rumored to contain fragments of Santa’s master gift registry, these files could be exploited by Malhare if their encryption is broken. The challenge highlights how weak passwords can expose even protected data—and whether the Elves can adapt in time.

## Learning Objectives
- Understand how **password-based encryption** protects PDFs and ZIP archives.
- Learn **why weak passwords undermine encryption**.
- Explore **dictionary and brute-force attacks** used to recover passwords.
- Gain **hands-on experience** cracking an encrypted file.
- Appreciate the **importance of strong, complex passwords** for defense.

## Key Takeaways
- Encryption strength depends largely on the **password quality**.
- **Short or common passwords** are easy to guess; **long, random passwords** are far harder to crack.
- **PDF and ZIP encryption differ** in algorithms, salts, and key derivation—affecting attack difficulty.
- **Legacy or weak encryption modes** (common in older ZIP files) are especially vulnerable.
- Encryption ensures **confidentiality only**; attackers with file access can attempt **offline password guessing**.

**Bottom line:** Encryption protects data only as well as the password behind it. Weak passwords invite compromise.

## LOGIN INFO

**username:** `ubuntu`
**password:** `AOC2025Ubuntu!`

We need to connect via ssh to the `MACHINE_IP`

# HOW ATTACKERS RECOVER WEAK PASSWORDS

Rather than breaking modern encryption algorithms, attackers focus on **guessing the password** protecting the file. The two main methods are **dictionary attacks** and **brute-force/mask attacks**.

### Dictionary Attacks
- Use predefined **wordlists** of common or leaked passwords.
- Include predictable patterns (e.g. `password123`, names, dates).
- **Fast and effective** because many users choose weak passwords.

### Brute-Force & Mask Attacks
- **Brute-force** tries every possible character combination; success is guaranteed but time grows rapidly with password length and complexity.
- **Mask attacks** narrow guesses to a known format (e.g. three letters + two digits), making attacks faster and more practical.

### Common Attack Strategy
- Start with general wordlists (e.g. `rockyou.txt`).
- Move to **targeted wordlists** using contextual information.
- Try **mask or incremental attacks** for short or patterned passwords.
- Use **GPU acceleration** to significantly increase cracking speed.
- Cracking is resource-intensive and may be **detectable** through high CPU/GPU usage.

**Key takeaway:** Weak, short, or predictable passwords are highly vulnerable to these attacks.

# EXCERSIZE

After logging in into the user `ubuntu` via **ssh** we are going to start the exercise in the **desktop**.

`cd Desktop`

Here we see 2 files that catch our eye: `flag.pdf` and  `flag.zip`.

lets use the `file command` to see what type of file they are.

- `flag.pdf` is a PDF Document v1.7
- `flag.zip` is a ZIP archive data v2.0 (**IMPORTANT** its encrypted)

For the **pdf** we are going to use `pdfcrack` or `john` and for the **ZIP** we can use `fcrackzip` or `john`.

Lets start with the PDF

## DICTIONARY ATTACK

Lets do a dictionary attack on the pdf using the wordlist `rockyou.txt`.

```
ubuntu@tryhackme:~/Desktop$ pdfcrack -f flag.pdf -w /usr/share/wordlists/rockyou.txt 
PDF version 1.7
Security Handler: Standard
V: 2
R: 3
P: -1060
Length: 128
Encrypted Metadata: True
FileID: 3792b9a3671ef54bbfef57c6fe61ce5d
U: c46529c06b0ee2bab7338e9448d37c3200000000000000000000000000000000
O: 95d0ad7c11b1e7b3804b18a082dda96b4670584d0044ded849950243a8a367ff
found user-password: flag1 #This is not the real password
```

Lets break down the command:

`pdfcrack -f flag.pdf -w /usr/share/wordlists/rockyou.txt`

- `-f` is the parameter to specify the file we want to crack.
- `-w` is the parameter to specify the wordlist we want to use (in our case the famous **rockyou.txt**)

> The room also teaches you to extract a hash using `pdf2john` and the cracking the hash to find the password.

now lets use `pdftotxt` to extract the decrypted message to a text format.

`pdftotext -upw naughtylist flag.pdf decryptedpdf.txt`

- `-upw` lets us specify the password.
- Then we specify the pdf that needs to be decrypted and the output `file` ( customname + .txt)

Then to get the flag we just cat it out (`cat`).

## USING JOHN

For the **ZIP archive** lets use the method with the **john** tool.

Lets start by extracting a hash thats **john-readable**.

`zip2john flag.zip > hashzip.txt`

This extracts the hast to a file called `hashzip.txt`.

Now with **john** lets crack the hash using the `rockyou.txt` wordlist.

`john --wordlist=/usr/share/wordlists/rockyou.txt ziphash.txt`

This will crack the hash and give us the password.

Then with the help of `7z` we will extract the files from the encrypted **ZIP** using the password gained.

`7z x flag.zip -pPASSWORD`

Then we cat out the `flag.txt` extracted to get the 2nd and last flag.

# SIDE QUEST NUM 2

This room also gives us a hint to get the key for **Side Quest 2**. Lets try it!!

> Ive not figured out the answer for now, but ill get to this after.

## SUMMARY / FEEDBACK

- I really liked this room, i love this part of cybersec.

- I love that its difficult to do the sidequest, ill get to it.

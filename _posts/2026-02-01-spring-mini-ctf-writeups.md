---
layout: post
title: Cornell Spring Mini CTF Writeups
tags: Comp featured
author: toasty / Bill Nguyen
pretty-date: 1 Feb. 2026
excerpt: Spring 26 Cornell CTF Writeup
---

The Spring CTF concluded on January 31st, 2026 with 48 users and 1 unblooded challenge. Unlike the Fall mini CTF, this competiion included challenges made by members of the CTF competition team. With NECCDC in the same month, I only managed to make 1 blind web challenge, and also a collection of OSINT challenges, which were mostly from my photo dump in Vietnam. This blog post will only contain the writeups for my challenges, but will also contain links to other writeups as I receive them from the team.

## Web
### Under Construction
#### 2 solves

I thought of this challenge when I was in Vietnam, which was also when I had to do scripting and infra for the CCDC team, so I made this (what I thought) simple blind-web challenge.

The challenge started with this static web page and no given src
![Under Construction](/assets/images/ctf-writeups/cornellsp26/Web1.png)

Looks boring, let's see what's underneath by viewing the page source
![View Page Source](/assets/images/ctf-writeups/cornellsp26/Web2.png)

We can immediately tell that this is running on React and Next.js. In a pentester standpoint, it is almost second nature to check for the version of the webpage's server.
This can be done by adding the -I flag to curl, and this is the result of doing so.
![curl -I](/assets/images/ctf-writeups/cornellsp26/Web3.png)

Even if you didn't know this existed, an nmap service scan with -sV will also reveal it. It's even in one of the chunks I think.

From there, it simply became another OSINT challenge. If you've been listening to security news for the past 2 months or have built web apps yourself, you have definitely heard of the critical CVE-2025-55182 / CVE-2025-66478, more commonly known as [React2Shell](https://react2shell.com/). This 10.0 CVSS-scored vulnerability have affected thousands of websites as React.js with its server components have been a stable for many developers to use.

If you have figured it out this far, all that's left is to find a Proof-of-Concept (PoC) that can exploit this vulnerability. I found [this](https://github.com/surajhacx/react2shellpoc.git) one to be effective.

Then reading the flag is trivial after getting shell.
![Flag](/assets/images/ctf-writeups/cornellsp26/Web4.png)

```
ccy{Y0u_g0tta_r3ad_th3_CVEs_bruh_:wilted_rose:}
```

To those who asked why this challenge didn't have an instancer as someone could've just rm -rf the whole container, I have already made it so you're an unprivileged user in a read-only tmpfs root filesystem.

Honestly I wanted to write an actual challenge instead of this, but we'll just have to wait for the next CTF.

## OSINT

Now for everyone's favorite part, OSINT!
This semester, I spent a big chunk of my winter break in Vietnam. Had lots of food and drinks (definitely needed that) and went to a lot of cool places.

### Drinks
#### 18 solves
```
I had drinks here. Drinks were mid, view is ok. Find the google maps location of the coffee shop

e.g. ccy{343 Campus Rd, Ithaca, NY 14853}
```
![Drinks](/assets/images/ctf-writeups/cornellsp26/OSINT1.jpg)

If you look at the image as a whole, you wouldn't be able to get much. Just a view of the beach and some people chilling. But if you zoom in on the right, you'll see a weird sculpture on a piece of rock. 

If you crop the photo onto the sculpture, you'll see it's the "Hòn Rù Rì" in Vũng Tàu, Vietnam. I've seen it falsely detect to stuff in Thailand or other places, but if you got lucky and did not fall into that rabbit hole, then you can easily identify where this was on Google Maps.

The coffee shop location added a little caveat to the problem. We can see that there is a bridge directly connecting the shop to the rock. This means that there is a direct path to the monument.

The flag then is:
```
ccy{1 Trần Phú, Phường 1, Vũng Tàu, Bà Rịa - Vũng Tàu 78000, Vietnam}
```

### Beautiful
#### 15 solves
```
Isn't the beach so beautiful. Find the google maps location

e.g. ccy{343 Campus Rd, Ithaca, NY 14853}
```
![Beautiful1](/assets/images/ctf-writeups/cornellsp26/OSINT2.jpg)
![Beautiful2](/assets/images/ctf-writeups/cornellsp26/OSINT3.jpg)

Honestly I expected much fewer solves for this challenge, as I thought it was just a random tunnel I found that led to a beautiful scene.

Turns out if you just put the second image into google images. It will spit out the correct address. Idk why I didn't check for this when making this chall XD.  The intended solve was to go around the coast of Vũng Tàu to find this tunnel. Oh well.

```
ccy{107 Trần Phú, Phường 5, Vũng Tàu, Bà Rịa - Vũng Tàu, Vietnam}
```

### 🥥🍦
#### 14 solves
```
I love coconut ice cream. Find me the google maps address e.g. ccy{343 Campus Rd, Ithaca, NY 14853}
```
![CoconutIC](/assets/images/ctf-writeups/cornellsp26/OSINT4.jpg)

This challenge bumped up a tiny bit in difficulty. When directly putting this image into Google Images, nothing of interest really pops up. Just coconut ice cream near the coastline. You'd probably guess it's Vietnam again and you'd be correct.

From there, you could just look around the coastline for the place, perfectly valid. 

Another solution path is to realize the [facebook post](https://www.facebook.com/cocodelivungtau/posts/n%E1%BA%BFu-m%C3%A0-ai-%C4%91ang-%E1%BA%BF-th%C3%AC-n%C3%AAn-%C4%91i-du-l%E1%BB%8Bch-nhi%E1%BB%81u-v%C3%A0o-%C4%91%E1%BA%A3m-b%E1%BA%A3o-kh%C3%B4ng-h%E1%BA%BFt-%E1%BA%BF-%C4%91%C3%A2u-nh%C6%B0ng-m%C3%A0-h/1441955071266694/) that pops up when looking up the image. The view does look the same as the challenge image, except at a slight different angle. This turns out to be an advertisement post for the store, CocoDeli.

The flag is then obvious from there

```
ccy{154A Hạ Long, Phường 2, Vũng Tàu, Bà Rịa - Vũng Tàu 78000, Vietnam}
```

### Food
#### 5 solves
```
O NOM NOM NOM NOM. Find the google maps location
e.g. ccy{343 Campus Rd, Ithaca, NY 14853}
```
![Food1](/assets/images/ctf-writeups/cornellsp26/OSINT5.jpg)
![Food2](/assets/images/ctf-writeups/cornellsp26/OSINT6.jpg)

This is where the challenges got difficult. A google image search on the second photo would reveal that the food I was eating was called ``bánh khọt``. If you noticed there were either shrimps or squids there also, which in Vietnamese would be ``tôm`` or ``mực``,repsectively. Creatively, the full name of that dish is ``bánh khọt tôm`` or ``bánh khọt mực``. 

However a problem quickly arises, there are too many places that sell that food in the area.
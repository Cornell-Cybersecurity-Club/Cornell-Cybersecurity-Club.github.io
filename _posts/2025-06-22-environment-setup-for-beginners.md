---
layout: post
title: Environment Setup for Beginners
tags: nme setup
author: Jakob Nacanaynay
pretty-date: 22 June, 2025
excerpt: Just run Linux!
---

The most important requirement for getting an environment setup for competing in CTFs is finding a way to run Linux. Running a Linux distribution like Kali will give you access to the tools most often used. Most people don’t use Linux as their main OS, but there are many ways to get access.

- Running a virtual machine (VM)
- Enabling WSL2 on Windows
- Using a cloud service (generally will cost money)
- Installing Linux to your computer or dual booting (not necessary nor recommended for beginners)

The fastest way to get things running is simply using WSL2 (if you have Windows), but in the long run, it is safer and better practice to run virtual machines.

## VirtualBox and Kali

VirtualBox is an open source program to manage virtual machines. There are other programs like VMware and QEMU which you may use if you prefer. The instructions for installing Kali Linux in VirtualBox is described here: [Setting up Kali in VirtualBox](https://docs.google.com/document/d/1uh1AxYd8tA30vbGhYDxMvpuHqMIQIz_O4XGg-GuJLOE/edit?usp=sharing) - I plan on switching this to markdown eventually.

## WSL2 Kali

1. Open Powershell as administrator
2. Run the command `wsl --install`
3. Go the the Microsoft store and install Kali Linux
4. You may need to reboot your computer
5. You should now be able to run a shell in Kali Linux

Your main drive in WSL should be accessible in the directory `/mnt/c` (or whatever your main drive letter is).

## Post-Installation

It’s a good idea to update your system once installed and update your system periodically. The first update will usually take a while, but it will be faster subsequent times if you update frequently. In Kali, the commands to update are as follows:

`sudo apt update` — it will ask you to authenticate
`sudo apt upgrade` — type ‘y’ to approve then hit enter

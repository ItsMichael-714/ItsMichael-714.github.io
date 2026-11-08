---
title: "Building My First Cybersecurity Home Lab (and Breaking It a Few Times)"
date: 2026-11-08
draft: false
tags: ["homelab", "cybersecurity", "virtualbox", "kali-linux", "ubuntu"]
---

I had an old PC running Ubuntu and doing nothing. This is the story of turning it into my first cybersecurity home lab, including everything that went wrong.

## The hardware

- **CPU:** Intel Core i5-3570 (4 cores, VT-x supported)
- **RAM:** 16 GB
- **Boot drive:** 2 TB hard drive running Ubuntu
- **Lab storage:** 240 GB Samsung 840 Pro SSD, previously an old Windows 10 install

The plan: run VirtualBox on Ubuntu, with Kali Linux as the attacker and Metasploitable 2 as an intentionally vulnerable target, all on an isolated network.

## Step 1: Giving the VMs a fast home

The SSD still had three old Windows partitions. After checking that everything on it was already backed up, I wiped it, created a GPT partition table, formatted it ext4, and mounted it at `/mnt/vmstore` through `/etc/fstab`.

Two lessons here. First, **drive letters are not permanent**. My SSD was `sda` and my hard drive was `sdb`, the reverse of what I first assumed, so I identified disks by size and model before touching anything. Second, a one-character typo in `fstab` (`/ext4` instead of `ext4`) produced an "unknown filesystem type" error. Running `mount -a` before rebooting caught it safely.

## Step 2: VirtualBox fights back

Installing Ubuntu's packaged VirtualBox (7.0.16) went wrong in several ways:

1. The kernel module wouldn't load, with a `Key was rejected by service` error.
2. A background update installed a brand-new 7.0 kernel while I was installing, and VirtualBox's driver failed to compile against it, leaving the update half-finished.
3. I removed VirtualBox, let the kernel finish installing, and reinstalled from **Oracle's own repository** (version 7.2.22), which supports newer kernels.

Then came the confusing part. `mokutil` claimed my system didn't support Secure Boot, but the kernel was clearly enforcing module signatures. Checking `/sys/kernel/security/lockdown` showed `integrity`, which gave it away. A second problem was hiding behind the first: `dmesg` showed **VT-x was disabled in the BIOS**. After enabling virtualization and changing the Secure Boot setting in my ASUS BIOS, lockdown showed `none`, `vboxconfig` built the modules, and `vboxdrv` finally loaded.q

## Step 3: Building the lab

- **Kali Linux** with two adapters: NAT for internet and updates, Host-only for the lab
- **Metasploitable 2** with a single Host-only adapter, so it can never touch my home network or the internet. This is necessary because Metasploitable 2 is vulnerable on purpose for learning purposes. I don't want that back door open in my network.
- I confirmed both machines worked and kali could ping MS2 machine. I then took a snapshot of each VM right after setup to have a default I could restore if anything ever broke. 

Metasploitable first showed up with an address in the NAT range, a sign it was attached to the wrong network. I fixed the adapter settings, and both machines landed on the `192.168.56.0/24` host-only network. I accidently put in the wrong Network settings. I got them backwords. BIG whoops. 

## First scan

From Kali:

```
nmap -sV 192.168.56.101
```

Twenty-three open ports in about 15 seconds, with FTP, Telnet, Samba, MySQL, Tomcat, and more, many running old versions with well-known flaws. The `-sV` flag matters because the *version* tells you which vulnerabilities to research.

## What I broke (twice)

I managed to break my Kali VM, then break the replacement too. I'm not sure how I broke it but I'm thinking when I performed the the full update on the kali machine, something must have gone wrong. I did the update overnight because it was taking way too long. Snapshots exist for this reason. Unfortunately, i broke the snapshot too. I truely don't know how but it would stay stuck in a black screen when I would start the VM. I had to remove that machine, delete the old files, unzip the .7z file again and start up a new VM and make sure the settings were correct all over again. Good practice I suppose. The habit I'm taking from it is to snapshot before every change and name the snapshot after what I'm about to do.

## Lessons learned

1. **Verify before you format.** Check disk names, sizes, and models first.
2. **Typos cause most errors.** `donload`, a missing `www`, and `intall` each cost me a round of troubleshooting. Copy and paste long commands.
3. **Don't install packages while the system updater is running.**
4. **Read the actual error.** `Key was rejected by service` led straight to the real cause.
5. **Isolate vulnerable machines.** Host-only networking for the target, always.
6. **Snapshot constantly.**

## What's next

- Research one service from the scan (starting with vsftpd 2.3.4) and understand *why* it's vulnerable
- Work through a structured path on TryHackMe
- Add a defensive VM, such as Wazuh or Security Onion, to learn the detection side

*All testing in this lab is done on machines I own, on an isolated network.*
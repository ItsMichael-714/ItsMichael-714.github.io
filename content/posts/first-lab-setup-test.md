+++
date = '2026-11-06T18:09:09-06:00'
draft = false
title = 'Setting Up My Homelab: VirtualBox, Kali, and Metasploitable'
theme = 'PaperMod'
paginate = 10

[params]
  description = "A diary of my homelab and cybersecurity learning"
  defaultTheme = "auto"
  ShowReadingTime = true
  ShowPostNavLinks = true
  ShowBreadCrumbs = true
  ShowCodeCopyButton = true

  [params.homeInfoParams]
    Title = "Welcome to my Homelab Journey"
    Content = "I'm documenting my start in homelabbing: VirtualBox, Kali, Metasploitable, and everything I break along the way."

[[menu.main]]
  identifier = "posts"
  name = "Posts"
  url = "/posts/"
  weight = 10

[[menu.main]]
  identifier = "tags"
  name = "Tags"
  url = "/tags/"
  weight = 20
+++


## Why I am starting a homelab
I've worked in the technical (not technology) field my whole professional life. I've fixed everything from cars to trains to now fixing Tesla Supercharging stations. I just don't feel challenged by this field any more. I actually started my interest in computers back in highschool when I built my first gaming PC. I had to learn about compatability bewtween specs on parts and software installation. Then a friend asked me to build them one, then another. Before I knew it, they would call me asking for tech support and I'd come over to troubleshoot why their PC build wouldn't boot or power on. 

Anyway, I'm going off on a tangent. I lost my way in the tech field and 2 decades later am finding my way back. I got into cybersecurity, got an associates in CyberSecurity this year and am now looking to change careers into that field. I started this homelab to learn how to fix all of the things I will surely break when learning how to spin up virtual machines and make remote connections. This is my first project and I started it on one of those old gaming computers I built back circa 2012. I intend on this really just being a way for me to document all my failures, attempts, and successes. Something for me to be able to look back at in the future if I get stuck and think "I feel like I've seen this problem before".

## What I've installed so far

- VirtualBox 
- Kali Linux
- Metasploitable 2
- Chocolatey
- Visual Studio
- Go
- sass
- git

## What I've broken so far

Aside from many spelling errors causing installation errors:
- Ubuntu kernel
    - I tried running a kernel update while installing virtualbox at the same time. It did not like that. 
    -The fix: boot into old kernel version and select the kernel to run(spacebar or esc while booting up) uninstall vbox, reintstall/reinitialize kernel update, reinstall vbox. All separately...hours of fun....
- Chocolatey
    -For some reason that I still don't understand, chocolatey installed itself but an error didn't add the executable file in the folder path. I would run choco --version in powershell and it would not understand choco. 
    -The fix: delete the file path and reinstalled chocolatey. It liked the second install and properly showed it's version when requested like a good boy. 
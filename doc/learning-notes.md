# Homelab Learning Notes

Everything I've learned building my homelab, in plain English. Newest topics are added at the bottom of each section.

## Servers and Linux

**Server:** a computer that stays on all the time and provides services to other devices. Mine is an old Lenovo IdeaPad 320.

**Linux:** a free operating system, like Windows. Most of the world's servers run Linux, so it's a key skill for cyber and software jobs.

**Ubuntu Server:** a version of Linux made for servers. It has no desktop, just a command line, so it uses fewer resources and has less that can be attacked.

**ISO file:** a single file that holds a whole operating system installer.

**Bootable USB:** a USB stick set up so a computer can start from it. I used **Rufus** to write the Ubuntu ISO to it, because just copying the file wouldn't work.

**Boot menu:** a menu at startup that lets you pick which device the computer starts from.

**cloud-init ("cloud-final" message):** a tool that finishes setting up Ubuntu the first time it starts. The text on screen during first boot comes from this. It's normal.

**Firmware:** low-level software built into hardware, like the BIOS. Updates are optional and slightly risky if the power cuts out halfway.

## Commands

**sudo:** runs a command as the administrator (called "root"). It asks for your password. Use it only when needed.

**apt:** Ubuntu's app store for the command line.

- `sudo apt update` checks for new versions.
- `sudo apt full-upgrade` installs them.

**sudo reboot:** restarts the server.

**ip a:** shows the server's network connections and IP addresses.

**exit:** leaves the server and returns to your own PC.

## Networking

**IP address:** a device's address on a network.

**Private IP** (like `192.168.1.206`): only works inside my home. It's safe to show people.

**Public IP / IPv6 starting 2a00:** points at my home connection on the internet. Never share it, and blur it in screenshots.

**Router:** the box that connects my home to the internet. By default it blocks incoming connections from the internet, which is why my server can't be seen from outside.

**Port:** a numbered door on a computer for a specific service. SSH uses port 22.

**Port forwarding:** a router setting that opens a door from the internet to a device. I'm **never** using it in this project, because it would expose the server.

**Ethernet (enp1s0):** the wired network connection. It's more reliable than Wi-Fi for a server.

**Fixed IP / DHCP reservation:** telling the router to always give the server the same address, so my SSH command keeps working.

## SSH (Secure Shell)

**SSH:** a way to control another computer by typing commands from your own, over an encrypted connection. Everything I type runs on the server, not my PC. My PC just acts like a remote control.

**OpenSSH server:** the program on the server that listens for SSH connections.

**Host key fingerprint:** a unique ID for the server. The first time I connect, my PC asks if I trust it. Checking it matches (with `ssh-keygen -lf` on the server) proves I'm talking to the real server and not an attacker pretending to be it. This kind of attack is called a **man-in-the-middle attack**.

**known_hosts:** a file on my PC listing servers I've trusted. If a fingerprint ever changes unexpectedly, SSH warns me.

**Login timeout:** the server closes the connection if you don't log in within about 2 minutes.

**Telling where I am:** `PS C:\Users\...>` means my PC. `username@homelab:~$` means the server.

## SSH keys

**SSH key pair:** two matching files that replace passwords.

- **Private key** (`id_ed25519`): stays on my PC. Never share it.
- **Public key** (`id_ed25519.pub`): goes on the server. It's safe to share.

It works like a padlock and key. The server holds the padlock (the public key) and only my private key opens it.

**Why keys beat passwords:** passwords can be guessed by bots. A key is far too long to guess.

**ed25519:** a modern, fast and secure type of key.

**Passphrase:** a password protecting the private key, in case someone steals my PC.

**authorized_keys:** the file on the server listing which public keys are allowed to log in.

## Security ideas

**Attack surface:** everything an attacker could try to get in through. Less is better.

**Bots:** automated programs that scan the whole internet nonstop, trying passwords on anything public. They don't target anyone in particular.

**Hardening:** locking a system down by removing what isn't needed and strengthening what is.

**Defence in depth:** using several layers of protection, so if one fails another still stops the attacker.

## GitHub

**Repository (repo):** a project folder on GitHub that keeps every version of every file. Mine is HomeLabDarcy.

**Commit:** saving a change to the repo, with a short message saying what changed. GitHub keeps every commit, so nothing is ever truly lost, including secrets. That's why passwords and keys never go in.

**Branch:** a separate copy of the project where changes can be made without touching the main version (called `main`).

**Pull request (PR):** a proposed change. Someone makes changes on a branch, then opens a PR asking to add them to `main`. Others review it, comment, and only merge it once they're happy. This is how software teams work, and it's how Claude updates my repo: Claude proposes, I review and click **Merge**.

**Merge:** accepting a pull request, so its changes join the main version.

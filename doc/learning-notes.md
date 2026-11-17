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

**What "Secure Shell" means:** a **shell** is the program that reads the commands I type and runs them (on Ubuntu it's called **bash**). **Secure** means the connection is encrypted, so anyone listening on the network only sees scrambled data, not my commands or passwords. SSH replaced an older tool called **Telnet**, which sent everything, passwords included, as plain readable text.

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

**Private key, in more detail:** the secret half of the key pair, saved on my PC at `C:\Users\<me>\.ssh\id_ed25519`. Whoever has this file can log in to my server, so it's as valuable as a password. It never leaves my PC: when I log in, my PC uses it to prove who I am, but the key itself is never sent over the network.

**How the proof works (the cyber security behind it):** this is called **public-key cryptography** (or asymmetric cryptography). The server sends my PC a random challenge. My PC signs it with the private key, and the server checks the signature using my public key. Only the matching private key could have made that signature, so the server knows it's me, without the secret ever travelling where it could be intercepted. With a password, the secret itself is typed and sent every time.

**Passphrase:** a password that encrypts (scrambles) my private key file on my PC. If someone stole my PC or copied the file, they'd only get a scrambled file they can't use without the passphrase. This is **defence in depth**: two things are needed to log in, the key file (something I have) and the passphrase (something I know). That's the same idea as **two-factor authentication (2FA)**. I keep the passphrase in my password manager.

**Key fingerprint:** a short code (starting `SHA256:`) that identifies a key, like a summary of it. It's much easier to compare than the whole key. It's safe to share.

**Randomart image:** the box of random characters shown when a key is made. It's the fingerprint drawn as a picture, because people spot a changed picture faster than a changed string of letters. It's just for my eyes, and it's safe to see.

**authorized_keys:** the file on the server listing which public keys are allowed to log in.

**Public key, in more detail:** the half of the key pair that's safe to share. On its own it can't log in to anything. It can only *check* signatures made by my private key. Putting it on the server means "trust whoever can prove they hold the matching private key". That lets me log in with my key instead of a password, and later lets me turn password login off completely, so bots can't guess their way in. I can put the same public key on many servers (and on GitHub) and use one private key for all of them.

**What the copy command did:** `type ...id_ed25519.pub` read my public key on my PC, and `|` (a "pipe") sent it over SSH to the server. There, `mkdir -p ~/.ssh` made the `.ssh` folder, `cat >> ~/.ssh/authorized_keys` added my key to the end of the allowed list, and `chmod 700` / `chmod 600` set the **permissions** so only my user can read or change them. SSH refuses keys in files other people could edit, because an attacker could add their own key.

## Hardening SSH

**nano:** a simple text editor that runs inside the terminal. **Ctrl+O** then **Enter** saves, **Ctrl+X** exits.

**sshd:** the SSH server program (the "d" stands for **daemon**, a program that runs in the background all the time). Its settings live in `/etc/ssh/sshd_config`, plus extra files in the folder `/etc/ssh/sshd_config.d/`.

**My hardening file** (`/etc/ssh/sshd_config.d/00-hardening.conf`):

- `PasswordAuthentication no` turns off password login, so only keys work and bots have nothing to guess.
- `KbdInteractiveAuthentication no` turns off a second, older way of typing a password in.
- `PermitRootLogin no` blocks logging in directly as **root**, the all-powerful admin account. Attackers always try `root` first because every Linux machine has it. I log in as my own user and use `sudo` when I need admin powers.

**Why the file name starts with 00:** SSH reads the files in that folder in alphabetical order, and for each setting **the first value it reads wins**. Ubuntu adds its own file (`50-cloud-init.conf`) that can turn password login back on. Starting my file with `00` means mine is read first, so my settings win.

**Lock-out safety:** before changing SSH settings, keep one SSH window logged in, test the change in a second window, and only close the first once the test works. The laptop's own keyboard is a last-resort way in, because SSH settings don't affect it.

**sudo sshd -t:** tests the SSH settings for mistakes without changing anything (`-t` = test). No output means no mistakes. Always run it before restarting SSH: a broken settings file could stop SSH from starting, which could lock me out.

**Kbd = keyboard:** `KbdInteractiveAuthentication` means "keyboard-interactive login", i.e. typing a password in. Settings names must be spelled exactly, capital letters included. I first typed `Kdb` by mistake, and `sshd -t` caught it as a "Bad configuration option" before it did any harm.

**sudo sshd -T** (capital T): prints the settings SSH will *actually* use after reading every file. Piping it into `grep -Ei` filters it down to just the lines I want to check. It's how I prove my settings won, rather than assuming.

**systemctl:** the tool for controlling services (background programs). `sudo systemctl restart ssh` restarts the SSH service so new settings take effect. My existing SSH connection stays open; only new connections use the new settings.

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

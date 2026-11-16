# Build Log

Notes on how the build is going, including anything that goes wrong and how I fix it. Newest at the top.

## 16 November 2026

- Downloaded [Ubuntu Server 26.04.1 LTS](https://ubuntu.com/download/server).
- Made a bootable USB stick with [Rufus](https://rufus.ie). You can't just copy the file onto the stick, the laptop wouldn't know how to start from it. Rufus writes it in a special way so the laptop can boot from it. Used [Create a bootable USB stick with Rufus on Windows](https://ubuntu.com/tutorials/create-a-usb-stick-on-windows#1-overview).
- Used Claude to guide through setup
- Made this GitHub repo to keep a record of the build.
- First boot showed lots of text ending in "cloud-final". This is cloud-init finishing Ubuntu's setup, and it's normal. Pressing Enter brought up the login prompt.
- Connected to the server over SSH from PowerShell on my Windows desktop. Before trusting the server, I checked its host key fingerprint matched the one on the server (`ssh-keygen -lf /etc/ssh/ssh_host_ed25519_key.pub`), to make sure I wasn't connecting to an attacker pretending to be it (a man-in-the-middle attack).
- **Problem:** the connection closed straight after I accepted the fingerprint. **Cause:** checking the fingerprint took a couple of minutes, and the server only waits about 2 minutes for a login. **Fix:** connected again, and it worked straight away because my PC had saved the fingerprint.
- The login message said firmware updates were available. I skipped them for now, because they're optional and slightly risky if the power cuts out halfway.
- Ran `sudo apt update && sudo apt full-upgrade` to install the latest security fixes, then rebooted.
- Set up a GitHub workflow with Claude: it proposes changes as pull requests, and I review and merge them myself, so nothing changes without my approval. I also scheduled a daily automated task that checks my learning notes and project instructions for changes and keeps this repo in sync. It batches each day's changes into one pull request to keep AI usage down, and stops after two weeks.

**Next:** set up an SSH key, check key login works, turn off password login, then turn on the UFW firewall.

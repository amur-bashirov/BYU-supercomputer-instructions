# SSH Client Instructions
explanation how to use SSh client and successfuly use it before creating an acount with BYU supercomputer department

### What is SSH
SSH (Secure Shell) lets you log into a remote computer's command line securely over a network. Instead of typing a password every time (which can be intercepted or brute-forced), you can use a key pair — two mathematically linked files:

- Private key — stays on your laptop, forever, never shared. Protected by the passphrase you set.
- Public key — you give this to any server you want to log into. It's safe to share; it can't be used to derive your private key.



## The concept: what `ssh-agent` actually is and why it exists

Think of your private key like a house key protected by a passphrase-locked safe. Every time you want to use it (connect to a server), you'd normally have to unlock the safe (type your passphrase) first. That's annoying if you connect to the same server 10 times a day.

`ssh-agent` is a small background program that:

1. Unlocks your private key once (you type the passphrase one time).
2. Holds the unlocked key in memory (RAM only — never written to disk).
3. Hands it out automatically to `ssh` whenever you connect somewhere, for as long as the agent is running (usually until you log out or reboot).



## How to install and use it (teachable steps)
### For Linux (Ubuntu / Debian-based)
1. Confirm OpenSSH client is installed (includes s`sh-agent`):
  ```
  sudo apt update
  sudo apt install openssh-client
  ```
2. Generate a key pair, if you don't have one:
  ```
  ssh-keygen -t ed25519 -C "your_email@example.com"
  ```
  Press Enter at the file-location prompt to accept the default, and set a passphrase.

3. Start the agent for your terminal session:
  ```
  eval "$(ssh-agent -s)"
  ```
  This starts the agent process and sets environment variables so `ssh` knows how to talk to it. You'll see it print something like Agent `pid 12345`.

  4. Add your private key to the running agent:
  ```
  ssh-add ~/.ssh/id_ed25519
  ```
  This is the one moment you type your passphrase. The agent now holds the unlocked key.

  5. Confirm it's loaded:
     ```
     ssh-add -l
     ```
  Lists the fingerprints of keys currently held by the agent.
  
  Note on persistance: 
  > on most desktop Ubuntu setups, GNOME Keyring auto-starts an agent at login and can auto-unlock it when you log into your desktop, so you may not need step 3–4 manually every session. But knowing how to do it by hand (like above) is what matters for anyone using a minimal/server install with no desktop environment.


### For Linux (Fedora / RHEL-based)
 
Package name and manager differ, everything else is identical to Ubuntu:
 
1. Install the client:
```
   sudo dnf install openssh-clients
```
2. Generate a key pair:
```
   ssh-keygen -t ed25519 -C "your_email@example.com"
```
3. Start the agent and add your key:
```
   eval "$(ssh-agent -s)"
   ssh-add ~/.ssh/id_ed25519
```
4. Confirm:
```
   ssh-add -l
```
 
Note: GNOME-based Fedora desktops auto-manage the agent the same way Ubuntu's GNOME does.
 

### For Linux (Arch-based)
 
1. Install the client:
```
   sudo pacman -S openssh
```
2. Generate, start agent, add key — identical commands to Ubuntu:
```
   ssh-keygen -t ed25519 -C "your_email@example.com"
   eval "$(ssh-agent -s)"
   ssh-add ~/.ssh/id_ed25519
   ssh-add -l
```
 
Arch installs are often minimal (no desktop keyring), so expect to run the `eval`/`ssh-add` steps manually every new terminal session unless you set up autostart yourself (e.g. in `~/.bash_profile` or `~/.zprofile`).
 
### For macOS
 
macOS ships OpenSSH by default — no install step needed.
 
1. Generate a key pair:
```
   ssh-keygen -t ed25519 -C "your_email@example.com"
```
2. Start the agent (usually already running, but this is safe to run anyway):
```
   eval "$(ssh-agent -s)"
```
3. Add your key, storing the passphrase in macOS's Keychain so you're not asked again after reboots:
```
   ssh-add --apple-use-keychain ~/.ssh/id_ed25519
```
4. Confirm:
```
   ssh-add -l
```
 
Note: on older macOS/OpenSSH versions the flag is `-K` instead of `--apple-use-keychain`. For it to persist automatically across every new Terminal tab, add a small `~/.ssh/config` entry:
```
Host *
  AddKeysToAgent yes
  UseKeychain yes
  IdentityFile ~/.ssh/id_ed25519
```
 
### For Windows
 
Windows 10/11 include an OpenSSH client built in, run through PowerShell — no separate download required for the basics.
 
1. Check it's available (from PowerShell):
```
   ssh -V
```
   If missing, install it via Settings → Optional Features → "OpenSSH Client", or:
```
   Add-WindowsCapability -Online -Name OpenSSH.Client~~~~0.0.1.0
```
2. Generate a key pair (same command as Linux/macOS):
```
   ssh-keygen -t ed25519 -C "your_email@example.com"
```
   This saves to `C:\Users\<you>\.ssh\id_ed25519` by default.
3. Start the agent service. On Windows the agent runs as a background Windows service rather than something you launch per terminal:
```
   Get-Service ssh-agent | Set-Service -StartupType Automatic
   Start-Service ssh-agent
```
   (First command needs to be run once, as Administrator.)
4. Add your key:
```
   ssh-add ~\.ssh\id_ed25519
```
5. Confirm:
```
   ssh-add -l
```
 
Alternative: many Windows users prefer **PuTTY** (a separate GUI SSH client with its own key format, `.ppk`) or **WSL** (Windows Subsystem for Linux, which lets you just follow the Ubuntu instructions above inside a real Linux environment). If BYU's documentation assumes a Linux-style client, using WSL is often the smoothest path on Windows.
 
## Summary table
 
| OS | Package manager / source | Agent start command | Notes |
|---|---|---|---|
| Ubuntu/Debian | `apt install openssh-client` | `eval "$(ssh-agent -s)"` | GNOME Keyring may auto-manage it |
| Fedora/RHEL | `dnf install openssh-clients` | `eval "$(ssh-agent -s)"` | Same GNOME auto-handling |
| Arch | `pacman -S openssh` | `eval "$(ssh-agent -s)"` | Often minimal — no auto-start |
| macOS | built in | `eval "$(ssh-agent -s)"` | Use `--apple-use-keychain` to persist |
| Windows | built in (Win10+) | `Start-Service ssh-agent` | Or use WSL / PuTTY as alternatives |

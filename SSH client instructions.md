# SSH Client Instructions
explanation how to use SSh client and successfuly use it before creating an acount with BYU supercomputer department

### What is SSH?
SSH (Secure Shell) lets you log into a remote computer's command line securely over a network. Instead of typing a password every time (which can be intercepted or brute-forced), you can use a key pair — two mathematically linked files:

- Private key — stays on your laptop, forever, never shared. Protected by the passphrase you set.
- Public key — you give this to any server you want to log into. It's safe to share; it can't be used to derive your private key.



## The concept: what `ssh-agent` actually is and why it exists

Think of your private key like a house key protected by a passphrase-locked safe. Every time you want to use it (connect to a server), you'd normally have to unlock the safe (type your passphrase) first. That's annoying if you connect to the same server 10 times a day.

`ssh-agent` is a small background program that:

1. Unlocks your private key once (you type the passphrase one time).
2. Holds the unlocked key in memory (RAM only — never written to disk).
3. Hands it out automatically to `ssh` whenever you connect somewhere, for as long as the agent is running (usually until you log out or reboot).

It's not a separate piece of software you download — it ships with OpenSSH, which you already installed. You just have to start it, because it doesn't run by default in most terminal sessions.

## How to install and use it (teachable steps)
### For Linux Ubuntu
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

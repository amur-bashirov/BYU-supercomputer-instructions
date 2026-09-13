# Linux & SSH Command Cheat Sheet

## General Linux Commands

### Navigation & Files

| Command | Description |
|---|---|
| `pwd` | Prints the current working directory. |
| `ls` | Lists files/folders in the current directory. |
| `ls -la` | Lists all files (including hidden ones starting with `.`) with detailed info (permissions, size, owner). |
| `cd path/to/dir` | Changes the current directory. |
| `cd ..` | Moves up one directory level. |
| `cd ~` | Goes to your home directory. |
| `mkdir dirname` | Creates a new directory. |
| `mkdir -p a/b/c` | Creates nested directories in one step, even if parents don't exist yet. |
| `rm filename` | Deletes a file. |
| `rm -r dirname` | Deletes a directory and everything inside it. |
| `cp source dest` | Copies a file. |
| `cp -r sourcedir destdir` | Copies a directory and its contents. |
| `mv source dest` | Moves or renames a file/directory. |
| `touch filename` | Creates an empty file (or updates its timestamp if it exists). |

### Viewing & Editing Files

| Command | Description |
|---|---|
| `cat filename` | Prints the entire file contents to the terminal. |
| `less filename` | Opens the file in a scrollable pager (press `q` to quit). |
| `head filename` | Shows the first 10 lines of a file. |
| `tail filename` | Shows the last 10 lines of a file. |
| `tail -f filename` | Continuously shows new lines as they're added (useful for log files). |
| `nano filename` | Opens the file in the Nano text editor (simple, beginner-friendly). |
| `vim filename` | Opens the file in Vim (more powerful, steeper learning curve). |
| `echo "text" >> filename` | Appends a line of text to the end of a file. |
| `grep "pattern" filename` | Searches for lines matching a pattern inside a file. |

### Permissions & Ownership

| Command | Description |
|---|---|
| `chmod 700 path` | Sets permissions so only the owner can read/write/execute. |
| `chmod 600 path` | Owner can read/write only, no execute, nobody else has access. |
| `chmod +x filename` | Makes a file executable. |
| `chown user:group path` | Changes the owner and group of a file/directory. |
| `sudo command` | Runs a command with administrator (root) privileges. |

### System & Processes

| Command | Description |
|---|---|
| `whoami` | Prints your current username. |
| `hostname` | Prints the machine's network name. |
| `sudo apt update` | Refreshes the list of available package versions (Debian/Ubuntu). |
| `sudo apt install packagename` | Installs a package (Debian/Ubuntu). |
| `systemctl status servicename` | Checks whether a background service is running. |
| `ps aux` | Lists all currently running processes. |
| `df -h` | Shows disk space usage in human-readable form. |
| `top` | Live view of running processes and resource usage (press `q` to quit). |

---

## SSH-Specific Commands

### Key Generation

| Command | Description |
|---|---|
| `ssh-keygen -t ed25519 -C "your_email@example.com"` | Generates a new Ed25519 key pair, saved to `~/.ssh/id_ed25519` (private) and `~/.ssh/id_ed25519.pub` (public) by default. |
| `ssh-keygen -l -f ~/.ssh/id_ed25519.pub` | Shows the fingerprint of a given public key. |

### ssh-agent

| Command | Description |
|---|---|
| `eval "$(ssh-agent -s)"` | Starts `ssh-agent` for the current terminal session. |
| `ssh-add ~/.ssh/id_ed25519` | Unlocks the private key (prompts for passphrase) and loads it into the agent's memory. |
| `ssh-add -l` | Lists fingerprints of keys currently loaded into the agent. |
| `ssh-add -D` | Unloads all keys from the agent (doesn't delete the key files). |

### Connecting

| Command | Description |
|---|---|
| `ssh username@remote-host` | Connects to `remote-host`, logging in as `username`. |
| `ssh -i ~/.ssh/custom_key username@remote-host` | Connects using a specific private key file. |
| `ssh -p 2222 username@remote-host` | Connects on a non-default port. |
| `ssh -v username@remote-host` | Connects with verbose output, useful for debugging connection issues. |
| `ssh-copy-id username@remote-host` | Copies your public key to the remote server's `authorized_keys`, enabling passwordless login. Requires existing password access. |

### Copying Files (scp)

| Command | Description |
|---|---|
| `scp localfile.txt username@remote-host:/path/` | Copies a local file to the remote server. |
| `scp username@remote-host:/path/file.txt ./` | Copies a file from the remote server to your current local directory. |
| `scp -r localfolder/ username@remote-host:/path/` | Recursively copies a folder and its contents to the remote server. |

### SSH File Permissions

| Command | Description |
|---|---|
| `chmod 700 ~/.ssh` | Restricts the `.ssh` folder to owner-only access. |
| `chmod 600 ~/.ssh/id_ed25519` | Restricts the private key to owner read/write only. |
| `chmod 644 ~/.ssh/id_ed25519.pub` | Public key can be world-readable. |
| `chmod 600 ~/.ssh/authorized_keys` | Restricts the server-side authorized keys file. |

### Diagnostics

| Command | Description |
|---|---|
| `ssh -V` | Prints the installed OpenSSH client version. |
| `sudo systemctl status ssh` | (server-side) Checks whether the SSH server daemon is running. |

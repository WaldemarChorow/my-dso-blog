# V-Server Setup

Dokumentation zur Konfiguration meines ersten Cloud-Servers während des Developer Akademie DevSecOps-Kurses. Schritt-für-Schritt-Anleitung zur Installation und Sicherung eines Webservers mit Nginx und SSH-Authentifizierung.

## Table of Contents

1. [Quickstart](#quickstart)
2. [Server Update](#server-update)
3. [Nginx Installation](#nginx-installation)
4. [SSH Keys and Initial Login](#ssh-keys-and-initial-login)
5. [SSH Configuration](#ssh-configuration)
6. [Nginx Configuration](#nginx-configuration)
7. [SSH Config for Multiple Identities](#ssh-config-for-multiple-identities)

import GithubLinkAdmonition from '@site/src/components/GithubLinkAdmonition';

<GithubLinkAdmonition 
    link="https://github.com/WaldemarChorow/my-dso-blog"
    title="Github Repository" 
    type="tip"
/>

## Quickstart

1. Update server: `sudo apt update && sudo apt upgrade`
2. Install Nginx: `sudo apt install nginx -y`
3. Generate SSH keys: `ssh-keygen -t ed25519`
4. Connect to server: `ssh -i ~/.ssh/keyname user@000.000.000.000`
5. Copy SSH key to server: `ssh-copy-id -i ~/.ssh/keyname user@000.000.000.000`
6. Adjust SSH config and disable password login

## Description

### Server Update

First, update the server:

```bash
sudo apt update
```

If new packages are available:

```bash
sudo apt upgrade
```

### Nginx Installation

Install Nginx web server:

```bash
sudo apt install nginx -y
```

Check service status:

```bash
systemctl status nginx.service
```

The server is now accessible via its IP address.

### SSH Keys and Initial Login

#### Generate SSH Keys

```bash
ssh-keygen -t ed25519
```

Enter an optional passphrase. This creates a public and private key pair.

Display the key path:

```bash
ls ~/.ssh/example
```

#### Connect to the Server

```bash
ssh user@000.000.000.000
```

- Confirm the fingerprint
- Enter your password
- Connection to server established

#### Store SSH Key on the Server

```bash
ssh-copy-id -i ~/.ssh/example user@000.000.00.00
```

The private key remains on your local machine. Enter the server password.

After that, connect using the SSH key:

```bash
ssh -i ~/.ssh/example_sshkey_name user@000.000.00.00
```

**Result:** Connected to server without password prompt.

#### Check Permissions

List server directory:

```bash
ls -al ~/
```

Change to SSH directory:

```bash
cd ~/.ssh
```

Display authorized keys:

```bash
cat ~/.ssh/authorized_keys
```

On Windows PowerShell:

```bash
type pfad_ssh_key/ssh_key_name.pub
```

### SSH Configuration

#### Disable Password Login (SSH Keys Only)

Open configuration file:

```bash
sudo nano /etc/ssh/sshd_config
```

Change the following entry:

```
PasswordAuthentication no
```

Save and exit (Ctrl+X, Y, Enter).

Restart SSH service:

```bash
sudo systemctl restart ssh.service
```

If the following warning appears:

```
Warning: The unit file, source configuration file or drop-ins of ssh.service changed on disk. Run 'systemctl daemon-reload' to reload units.
```

Then run:

```bash
sudo systemctl daemon-reload
```

Afterwards, repeat step 1.

Logout and verify functionality:

```bash
logout
```

Reconnect:

```bash
ssh -i ~/.ssh/example_sshkey_name user@000.000.00.00
```

Logout again and verify with public key only:

```bash
ssh -o PubkeyAuthentication=no user@000.000.00.00
```

**Expected result:** `user@000.000.00.00: Permission denied (publickey)`

#### Alias for SSH Connection

```bash
alias name_alias="ssh -i ~/.ssh/example_sshkey_name user@000.000.00.00"
```

The alias only works in the **current terminal session**. After restarting, it will be gone.

To save it **permanently**, add it to your shell configuration:

**For Zsh:**

```bash
nano ~/.zshrc
```

Add the following line at the end:

```bash
alias name_alias="ssh -i ~/.ssh/sshkey_name user@000.000.000.000"
```

Save (Ctrl+X, Y, Enter), then:

```bash
source ~/.zshrc
```

Now you can simply type:

```bash
daserver
```

And you're connected! ✓

### Nginx Configuration

#### Create Directory and HTML File

Create protected directory:

```bash
sudo mkdir /var/www/alternatives
```

Create HTML file:

```bash
sudo touch /var/www/alternatives/alternate-index.html
```

Verify directory:

```bash
ls /var/www/alternatives
```

#### Add Nginx Configuration

Create new configuration under `sites-enabled`:

```bash
sudo nano /etc/nginx/sites-enabled/alternatives
```

Example configuration:

```nginx
server {
    listen 80;
    server_name _;
    
    location / {
        root /var/www/alternatives;
        index alternate-index.html;
    }
}
```

Save and exit.

#### Customize HTML File

```bash
sudo nano /var/www/alternatives/alternate-index.html
```

Insert desired HTML code and save.

#### Restart Nginx and Check Status

```bash
sudo service nginx restart
```

Check status:

```bash
systemctl status nginx.service
```

### SSH Config for Multiple Identities

Find SSH config directory:

```bash
ls ~/.ssh
```

Display config file contents:

```bash
cat ~/.ssh/config
```

Edit config file:

```bash
nano ~/.ssh/config
```

Specify host, user, authentication method, and path to public key:

```
Host vserver
    HostName 000.000.000.000
    User username
    IdentityFile ~/.ssh/example_sshkey_name
    IdentitiesOnly yes
    
Host github
    HostName github.com
    User git
    IdentityFile ~/.ssh/github_key
    IdentitiesOnly yes
```

Save and exit.

Now you can simply connect with `ssh vserver`.

## Further References

- [OpenSSH Documentation](https://man.openbsd.org/ssh)
- [Nginx Official Documentation](https://nginx.org/en/docs/)
- [Ed25519 SSH Keys](https://wiki.archlinux.org/title/SSH_keys#Ed25519)
- [Debian/Ubuntu SSH Security](https://wiki.debian.org/SSH)
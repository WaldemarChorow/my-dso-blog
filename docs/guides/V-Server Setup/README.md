# V-Server Setup

Documentation for configuring my first cloud server during the Developer Akademie DevSecOps course. Step-by-step guide for installing and securing a web server with Nginx and SSH authentication.

## Table of Contents

1. [Quickstart](#quickstart)
2. [Server Update](#server-update)
3. [SSH Keys and Initial Login](#ssh-keys-and-initial-login)
4. [SSH Configuration](#ssh-configuration)
5. [SSH Config for Multiple Identities](#ssh-config-for-multiple-identities)
6. [Nginx Installation](#nginx-installation)
7. [Nginx Configuration](#nginx-configuration)
8. [Connect Server with GitHub](#connect-server-with-github)
   - [Set Git User](#set-git-user)
   - [Set Git Email](#set-git-email)
   - [Verify Git Configuration](#verify-git-configuration)
   - [Generate SSH Key Pair](#generate-ssh-key-pair)
   - [Add Public Key to GitHub](#add-public-key-to-github)
   - [Test Connection](#test-connection)
   - [Troubleshooting: Connection Fails](#troubleshooting-connection-fails)
9. [Further References](#further-references)

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
4. Copy SSH key to server: `ssh-copy-id -i ~/.ssh/keyname user@000.000.000.000`
5. Connect to server: `ssh -i ~/.ssh/keyname user@000.000.000.000`
6. Disable password authentication in SSH config
7. Add public key to GitHub and verify connection

## Server Update

First, update the server's package list:

```bash
sudo apt update
```

Install available package upgrades:

```bash
sudo apt upgrade
```

Your server is now up-to-date and accessible via its IP address.

## SSH Keys and Initial Login

### Generate SSH Keys

Generate a new SSH key pair using Ed25519:

```bash
ssh-keygen -t ed25519
```

Enter an optional passphrase to secure your private key. This creates a public and private key pair.

Verify the key was created:

```bash
ls ~/.ssh/
```

### Connect to the Server

Connect to your server for the first time with your password:

```bash
ssh user@000.000.000.000
```

- Confirm the host fingerprint by typing `yes`
- Enter your server password
- Connection to server established

### Copy SSH Key to Server

Copy your public key to the server (you will need to enter your password one last time):

```bash
ssh-copy-id -i ~/.ssh/id_ed25519 user@000.000.000.000
```

Your private key remains securely on your local machine.

### Connect Using SSH Key

After copying the key, connect to your server using only the SSH key:

```bash
ssh -i ~/.ssh/id_ed25519 user@000.000.000.000
```

**Result:** You should now connect without a password prompt.

### Verify Key Permissions

List the authorized keys on the server:

```bash
cat ~/.ssh/authorized_keys
```

On Windows PowerShell, display your public key:

```powershell
type $env:USERPROFILE\.ssh\id_ed25519.pub
```

## SSH Configuration

### Disable Password Login (SSH Keys Only)

Open the SSH server configuration file:

```bash
sudo nano /etc/ssh/sshd_config
```

Locate and change the following entry:

```
PasswordAuthentication no
```

Save and exit the editor (Ctrl+X, Y, Enter).

Restart the SSH service:

```bash
sudo systemctl restart ssh.service
```

If a daemon-reload warning appears:

```
Warning: The unit file, source configuration file or drop-ins of ssh.service changed on disk. Run 'systemctl daemon-reload' to reload units.
```

Run the daemon-reload command:

```bash
sudo systemctl daemon-reload
```

Then restart SSH again:

```bash
sudo systemctl restart ssh.service
```

### Verify Password Authentication is Disabled

Log out from your server:

```bash
logout
```

Reconnect with your SSH key to ensure it still works:

```bash
ssh -i ~/.ssh/id_ed25519 user@000.000.000.000
```

Attempt to connect without your SSH key (should fail):

```bash
ssh -o PubkeyAuthentication=no user@000.000.000.000
```

**Expected result:** `Permission denied (publickey)` — Password authentication is now disabled.

### Create a Permanent SSH Alias

To create a convenient alias for connecting to your server, edit your shell configuration file.

**For Zsh:**

```bash
nano ~/.zshrc
```

Add the following line at the end:

```bash
alias myserver="ssh -i ~/.ssh/id_ed25519 user@000.000.000.000"
```

Save and exit (Ctrl+X, Y, Enter), then reload your configuration:

```bash
source ~/.zshrc
```

Now you can connect simply by typing:

```bash
myserver
```

And you're connected! ✓

## SSH Config for Multiple Identities

Managing multiple SSH keys for different services is easier with an SSH config file.

View existing SSH keys:

```bash
ls ~/.ssh
```

Edit your SSH config file:

```bash
nano ~/.ssh/config
```

Add entries for different hosts with their respective keys:

```
Host vserver
    HostName 000.000.000.000
    User username
    IdentityFile ~/.ssh/id_ed25519_vserver
    IdentitiesOnly yes

Host github
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_github
    IdentitiesOnly yes
```

Save and exit (Ctrl+X, Y, Enter).

Now you can connect to your server using the alias:

```bash
ssh vserver
```

The SSH client will automatically use the correct key from your config file.

## Nginx Installation

Install the Nginx web server:

```bash
sudo apt install nginx -y
```

Verify that Nginx is running:

```bash
systemctl status nginx.service
```

## Nginx Configuration

### Create Web Directory and HTML File

Create a new directory for your web content:

```bash
sudo mkdir -p /var/www/mysite
```

Create an HTML file:

```bash
sudo nano /var/www/mysite/index.html
```

Add some example content:

```html
<!DOCTYPE html>
<html>
<head>
    <title>My Server</title>
</head>
<body>
    <h1>Welcome to My V-Server!</h1>
    <p>Nginx is running successfully.</p>
</body>
</html>
```

Save and exit (Ctrl+X, Y, Enter).

### Create Nginx Server Configuration

Create a new Nginx configuration file:

```bash
sudo nano /etc/nginx/sites-enabled/mysite
```

Add the following server configuration:

```nginx
server {
    listen 80;
    server_name _;
    
    location / {
        root /var/www/mysite;
        index index.html;
    }
}
```

Save and exit.

### Reload and Verify Nginx

Reload Nginx to apply the new configuration:

```bash
sudo nginx -t
```

If the test is successful, reload Nginx:

```bash
sudo systemctl reload nginx
```

Verify the service is running:

```bash
systemctl status nginx.service
```

## Connect Server with GitHub

### Set Git User

Configure your Git username on the server (should match your GitHub username):

```bash
git config --global user.name "Your Name"
```

### Set Git Email

Configure your Git email on the server (should match your GitHub email):

```bash
git config --global user.email "your.email@example.com"
```

### Verify Git Configuration

Verify that your Git configuration was saved correctly:

```bash
git config --global --list
```

### Generate SSH Key Pair

Generate a new SSH key pair for GitHub on the server:

```bash
ssh-keygen -t ed25519 -C "your.email@example.com"
```

When prompted, use a descriptive name for the key:

```
Enter file in which to save the key (/home/username/.ssh/id_ed25519): id_ed25519_github
Enter passphrase (empty for no passphrase): [press Enter or add a passphrase]
Enter same passphrase again: [confirm]
```

The output will show:

```
Your identification has been saved in /home/username/.ssh/id_ed25519_github
Your public key has been saved in /home/username/.ssh/id_ed25519_github.pub
The key fingerprint is:
SHA256:example your.email@example.com
The key's randomart image is:
+--[ED25519 256]--+
|        o+.      |
|        .o.o     |
|        . + .    |
|       . o +     |
|  +     S . E    |
| o +.... ... o   |
|. + o+oo.o  o    |
|B=o..o+.=...     |
|B%*..o.=o+o      |
+----[SHA256]-----+
```

### Add Public Key to GitHub

Display your public key:

```bash
cat ~/.ssh/id_ed25519_github.pub
```

Copy the output and add it to GitHub:

1. Go to [GitHub Settings → SSH and GPG keys](https://github.com/settings/keys)
2. Click "New SSH key"
3. Give your key a descriptive name (e.g., "My V-Server")
4. Paste the public key
5. Click "Add SSH key"

Your connection is now ready for use.

### Test Connection

Test your GitHub SSH connection:

```bash
ssh -T git@github.com
```

**Expected output:**

```
Hi YourName! You've successfully authenticated, but GitHub does not provide shell access.
```

### Troubleshooting: Connection Fails

If you receive a "Permission denied (publickey)" error:

```
git@github.com: Permission denied (publickey).
```

Add your GitHub SSH key to the SSH agent:

```bash
ssh-add ~/.ssh/id_ed25519_github
```

Then test the connection again:

```bash
ssh -T git@github.com
```

Alternatively, ensure your SSH config file is properly configured (see [SSH Config for Multiple Identities](#ssh-config-for-multiple-identities)) with the correct key path.

Test again:

```bash
ssh -T git@github.com
```

It should now work! ✓

## Further References

- [OpenSSH Documentation](https://man.openbsd.org/ssh)
- [Nginx Official Documentation](https://nginx.org/en/docs/)
- [Ed25519 SSH Keys](https://wiki.archlinux.org/title/SSH_keys#Ed25519)
- [Debian/Ubuntu SSH Security](https://wiki.debian.org/SSH)
- [GitHub SSH Documentation](https://docs.github.com/en/authentication/connecting-to-github-with-ssh)
- [SSH Best Practices](https://www.ssh.com/academy/ssh/key)
# V-Server Setup

Dokumentation der Konfiguration meines ersten Cloud-Servers im Developer Akademie DevSecOps Kurs. Schritt-für-Schritt Anleitung zur Installation und Sicherung eines Webservers mit Nginx und SSH-Authentifizierung.

## Inhaltsverzeichnis

1. [Quickstart](#quickstart)
2. [Server Update](#server-update)
3. [Nginx Installation](#nginx-installation)
4. [SSH-Keys und erstes Login](#ssh-keys-und-erstes-login)
5. [SSH-Konfiguration](#ssh-konfiguration)
6. [Nginx Konfiguration](#nginx-konfiguration)
7. [SSH-Config für mehrere Identities](#ssh-config-für-mehrere-identities)

import GithubLinkAdmonition from '@site/src/components/GithubLinkAdmonition';

<GithubLinkAdmonition 
    link="https://github.com/WaldemarChorow/my-dso-blog"
    title="Github Repository" 
    type="tip"
/>

## Quickstart

1. Server updaten: `sudo apt update && sudo apt upgrade`
2. Nginx installieren: `sudo apt install nginx -y`
3. SSH-Keys generieren: `ssh-keygen -t ed25519`
4. Mit Server verbinden: `ssh -i ~/.ssh/keyname user@000.000.000.000`
5. SSH-Key auf Server kopieren: `ssh-copy-id -i ~/.ssh/keyname user@000.000.000.000`
6. SSH-Config anpassen und Password-Login deaktivieren

## Description

### Server Update

Zunächst sollte der Server aktualisiert werden:

```bash
sudo apt update
```

Wenn neue Pakete vorhanden sind:

```bash
sudo apt upgrade
```

### Nginx Installation

Nginx Webserver installieren:

```bash
sudo apt install nginx -y
```

Status des Services abfragen:

```bash
systemctl status nginx.service
```

Der Server ist nun über die IP-Adresse erreichbar.

### SSH-Keys und erstes Login

#### SSH-Keys erzeugen

```bash
ssh-keygen -t ed25519
```

Optionales Passwort eingeben. Dies erzeugt einen Public und Private Key.

Den Key-Pfad anzeigen:

```bash
ls ~/.ssh/example
```

#### Mit dem Server verbinden

```bash
ssh nutzer@000.000.000.000
```

- Fingerprint bestätigen
- Passwort eingeben
- Verbindung zum Server hergestellt

#### SSH-Key auf dem Server hinterlegen

```bash
ssh-copy-id -i ~/.ssh/example user@000.000.00.00
```

Der Private Key bleibt auf dem lokalen Rechner. Passwort des Servers eingeben.

Danach kann man sich mit dem SSH-Key verbinden:

```bash
ssh -i ~/.ssh/example_sshkey_name user@000.000.00.00
```

**Ergebnis:** Mit dem Server ohne Passwortabfrage verbunden.

#### Berechtigungen prüfen

Server-Verzeichnis auflisten:

```bash
ls -al ~/
```

Ins SSH-Verzeichnis wechseln:

```bash
cd /.ssh
```

Authorized Keys anzeigen:

```bash
cat ~/.ssh/authorized_keys
```

Unter Windows PowerShell:

```bash
type pfad_ssh_key/ssh_key_name.pub
```

### SSH-Konfiguration

#### Password-Login deaktivieren (nur SSH-Keys)

Konfigurationsdatei öffnen:

```bash
sudo nano /etc/ssh/sshd_config
```

Folgenden Eintrag ändern:

```
PasswordAuthentication no
```

Speichern und beenden (Ctrl+X, Y, Enter).

SSH-Service neu starten:

```bash
sudo systemctl restart ssh.service
```

Logout und Funktion verifizieren:

```bash
logout
```

Verbindung erneut herstellen:

```bash
ssh -i ~/.ssh/example_sshkey_name user@000.000.00.00
```

Nochmal logout und gegen mit nur Public Key überprüfen:

```bash
ssh -o PubkeyAuthentication=no user@000.000.00.00
```

**Ergebnis sollte sein:** `user@000.000.00.00: Permission denied (publickey)`

#### Alias für SSH-Verbindung

```bash
alias name_alias="ssh -i ~/.ssh/example_sshkey_name user@000.000.00.00"
```

### Nginx Konfiguration

#### Verzeichnis und HTML-Datei erstellen

Geschütztes Verzeichnis erstellen:

```bash
sudo mkdir /var/www/alternatives
```

HTML-Datei anlegen:

```bash
sudo touch /var/www/alternatives/alternate-index.html
```

Verzeichnis prüfen:

```bash
ls /var/www/alternatives
```

#### Nginx-Konfiguration hinzufügen

Neue Konfiguration unter `sites-enabled` erstellen:

```bash
sudo nano /etc/nginx/sites-enabled/alternatives
```

Beispiel-Konfiguration:

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

Speichern und beenden.

#### HTML-Datei anpassen

```bash
sudo nano /var/www/alternatives/alternate-index.html
```

Gewünschten HTML-Code einfügen und speichern.

#### Nginx neu starten und Status überprüfen

```bash
sudo service nginx restart
```

Status überprüfen:

```bash
systemctl status nginx.service
```

### SSH-Config für mehrere Identities

SSH-Config-Verzeichnis finden:

```bash
ls ~/.ssh
```

Inhalt der Config-Datei anzeigen:

```bash
cat ~/.ssh/config
```

Config-Datei bearbeiten:

```bash
nano ~/.ssh/config
```

Host, User, Authentifizierungsart und Verzeichnis des Public Keys angeben:

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

Speichern und beenden.

Nun kann man sich einfach mit `ssh vserver` verbinden.

## Further References

- [OpenSSH Documentation](https://man.openbsd.org/ssh)
- [Nginx Official Documentation](https://nginx.org/en/docs/)
- [Ed25519 SSH Keys](https://wiki.archlinux.org/title/SSH_keys#Ed25519)
- [Debian/Ubuntu SSH Security](https://wiki.debian.org/SSH)
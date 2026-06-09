# Using SSH Connection

## Basic syntax

```bash
ssh user_name@host_name
```

or

```bash
# If you need to specify the identity file
ssh user_name@host_name -i /path/to/identity/file
```

## SSH Server

```bash
sudo systemctl start ssh
```

### Check SSH status

```bash
sudo systemctl status ssh
```

### Restart SSH server

```bash
sudo systemctl restart ssh
```

### Firewall `ufw`

```bash
sudo ufw status
sudo ufw allow 22
```

## Use Certificatio File

### Generate Certification key pare

```bash
ssh-keygen -t rsa
```

This command will generate a key pare (default: `~/.ssh/id_rsa` and `~/.ssh/id_rsa.pub`)

Private key file (the file without `.pub` extention) should never leave your computer.

Set strong password for your future!!

### Set Public Key to Server

1. Copy the contents inside the public key (a file with `.pub` extention)
2. Save the contents to the file `~/.ssh/authorozed_keys` as a new line.

### Disable Password Authentication

Uncomment or create and set `PasswordAuthentication no`

## Proxy Jump

### Create a configuration file

E.g.: `~/.ssh/config`

```text
Host JumpServer1
  Hostname jump1.IP
  User user_on_jump1
  IdentityFile /path/to/identity/file_jump1

Host JumpServer2
  Hostname jump2.IP
  User user_on_jump2
  ProxyJump JumpServer1
  IdentityFile /path/to/identity/file_jump2

Host DestServer
  Hostname dest.IP
  User dest_user_name
  ProxyJump JumpServer2
  IdentityFile /path/to/identity/file_dest
```

### Use configuration file for proxy jump

```bash
ssh DestServer

#or if you want to specify the configuration file path
ssh -F /path/to/config DestServer
```

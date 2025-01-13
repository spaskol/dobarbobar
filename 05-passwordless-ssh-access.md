# Passowrdless SSH access

Here I will explain how I set passwordless SSH access to access linux servers.
This is needed for Ansible automation and also is a good security practice.

## Generate keys

```bash
ssh-genkey -t ed25519 -C "virtualbox" 
```

`-t` specify type like ed25519 (default) or rsa \
`-C` specify comment for reference

## Copy SSH key to the guest
```powershell
scp C:\Users\DobarBobar\.ssh\id_ed25519.pub virtualbox@192.168.56.1:~/.ssh/dobarbobar.pub
```
accept the key from the guest (192.168.56.1 in my case)
you need to enter the password

## Add SSH key to authorized_keys
Then use the following command to add the host key to the guest "authorized_keys" file

```bash
cat ~/.ssh/dobarbobar.pub | tee -a ~/.ssh/authorized_keys
```

## Access the guest
Then you should be able to access the guest without password.
You can change the password with a difficult one using `passwd`.

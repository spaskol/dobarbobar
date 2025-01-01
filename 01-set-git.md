# Set Git

Here I will describe my journey setting Git on my local machine.

## Install Git

Because I am using Ubuntu Linux I am using

```bash
sudo apt install git
```

## Config name and email

```bash
git config --global user.name example
git config --global user.email example@example.com
```

## Make pushs/pulls without using user name and password

Here I faced some issue. Here the steps I uses:

1. Generate SSH key on the local machine

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

2. Set passphrase

```bash
ssh-keygen -p -f ~/.ssh/id_ed25519
```

3. Set remote URL

```bash
git remote set-url origin git@github.com:username/repository.git
```

Test commit on GitHub web


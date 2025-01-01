# Set Git

Here I will describe my journey setting Git on my local machine.

## Install Git

Because I am using Ubuntu Linux I am using

```bash
sudo apt install git
```

## Config name and email

```bash
git config --global user.email "you@example.com"
git config --global user.name "Your Name"
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

3. Add the SSH key in GitHub

4. Set remote URL - this step is important

```bash
git remote set-url origin git@github.com:username/repository.git
```


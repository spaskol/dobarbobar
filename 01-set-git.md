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

While trying to push I recieved:

```bash
remote: Support for password authentication was removed on August 13, 2021.
remote: Please see https://docs.github.com/get-started/getting-started-with-git/about-remote-repositories#cloning-with-https-urls for information on currently recommended modes of authentication.
```
So I have decided to use SSH key as authentification to train it. 

1. Generate SSH key on the local machine

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

2. Add the SSH key in GitHub

```bash
cat ~/.ssh/id_ed25519.pub
```
Copy and paste the output of the above command to GitHub

4. Set remote URL - this step is important

```bash
git remote set-url origin git@github.com:username/repository.git
```

Source: https://docs.github.com/en/authentication/connecting-to-github-with-ssh

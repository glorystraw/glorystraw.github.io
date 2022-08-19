---
title: "Multiple GitHub accounts on a single Mac computer with SSH keys"
date: 2022-08-12T08:34:30-04:00
categories:
  - blog
tags:
  - GitHub
  - multiple accounts
  - SSH keys
---

Goal:
To use several GitHub accounts on a single Mac machine
Often you need to combine work and personal projects on the same computer, but you have different GitHub accounts for it. And the question arises how to divide these activities. Let's try to solve this problem in this post.

Let's start.
1. Organize workspace
First of all we need to orginize our workspace for different project types.
On my Mac I prefer to use such folder structure:
```shell
_projects_dir
 ┣ personal_dir
 ┗ work_dir_1
```

2. Generate SSH keys for your GitHub accounts.

Check .ssh directory in your user root folder and create if it doesn't exist
```shell
mkdir -p .ssh
```

We will use Ed25519 algorithm to generate new SSH keys:

For personal GitHub account:

```shell
ssh-keygen -t ed25519 -C "your_personal_email@example.com" -f ~/.ssh/ed25519_personal
```
You will be prompted to add passphrase:
```shell
Generating public/private ed25519 key pair.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /Users/username/.ssh/ed25519_personal
Your public key has been saved in /Users/username/.ssh/ed25519_personal.pub
```

For work GitHub account:

```shell
ssh-keygen -t ed25519 -C "your_work_email@example.com" -f ~/.ssh/ed25519_work
```
You will be prompted to add passphrase for the new key:
```shell
Generating public/private ed25519 key pair.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /Users/username/.ssh/ed25519_work
Your public key has been saved in /Users/username/.ssh/ed25519_work.pub
```

Great. Now we have SSH keys for our personal and work accounts. Lets add our keys to the ssh-agent and store passphrases in the keychain:

Start the ssh-agent
```shell
eval "$(ssh-agent -s)"
```
Check to see if your ~/.ssh/config file exists
```shell
open ~/.ssh/config
```
or create it if doesn't
```shell
touch ~/.ssh/config
```

Add following to the config file:
```shell
# Personal GitHub account
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/ed25519_personal

# Work GitHub account
Host github-work
  HostName github.com
  User git
  IdentityFile ~/.ssh/ed25519_work

# Common settings
Host *
  UseKeychain yes
  AddKeysToAgent yes
```

Now we need to add our SSH private keys to the ssh-agent and store passphrases in the keychain.

For the personal key:

```shell
ssh-add --apple-use-keychain ~/.ssh/ed25519_personal
```
```shell
Enter passphrase for /Users/username/.ssh/ed25519_personal:
Identity added: /Users/username/.ssh/ed25519_personal (your_personal_email@example.com)
```

For the work key:

```shell
ssh-add --apple-use-keychain ~/.ssh/ed25519_work
```
```shell
Enter passphrase for /Users/glorystraw/.ssh/ed25519_work:
Identity added: /Users/glorystraw/.ssh/ed25519_work (your_work_email@example.com)
```

For more detailed explanation for the generating SSH keys use this official instruction [Generating new SSH key](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)
{: .notice--info}

3. Now lets add created SSH keys to our accounts on GitHub.

Copy the SSH personal public key to your clipboard.
```shell
pbcopy < ~/.ssh/ed25519_personal.pub
```

Go to the settings

```
https://github.com/settings/keys
```

![GitHub Menu](/assets/images/github_menu.png){:width="160px"}

New SSH key or Add SSH key

Add descriptive title

Paste copied key

Click Add SSH key

*Authorizing an SSH key for use with SAML single sign-on if needed
Enable SSO

4. Adding .gitconfig files





Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]: https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/

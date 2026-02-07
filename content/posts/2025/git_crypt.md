+++
title = 'Git Repo with encrypted content'
date = '2025-08-28'
draft = false
tags = ['tools']
#author = 'adriano'
header_image = "/images/git_crypt.png"
+++

I have a private repo on GitHub where I sync the text I use every day: notes, code, drafts, etc. Even though the repo is private, sometimes i need to store sensitive information that I’d like to keep secure. Ideally, I want to continue using Git as usual, but i would like to have a way to place certain files in the repo encrypted. I came across a tool that allows me to do that, it's called [git-crypt](https://www.agwa.name/projects/git-crypt/).

## Set up git-crypt

Install the required tools
```Bash
brew install gnupg git-crypt
```

Then inside the repo where we want to have encrypted content we initialize git-crypt:
```Bash
git-crypt init
```

We need to specify which content to encrypt in **.gitattributes** file:
```Bash
etc/notes.txt    filter=git-crypt diff=git-crypt
```

By default, git-crypt uses GPG keys for access. Generate a new GPG key:
```Bash
gpg --full-generate-key
```
It will ask for a **passphrase** that should be then stored in some place safe.


We now can list the keys
```Bash
gpg --list-secret-keys
```

and then we should add our key to git-crypt:
```Bash
git-crypt add-gpg-user YOUR_GPG_KEY_ID
```
Now we can add and push the content
```Bash
git add .gitattributes
git commit -m "Setup git-crypt"
git push
```

From this point on, the files listed in **.gitattributes** will be stored encrypted in the repository. On GitHub, you’ll see only encrypted blobs, but locally you can still read and edit them in plaintext.

You can also manually lock and unlock your repo locally:
```Bash
git-crypt lock
git-crypt unlock
```

To check if the files are really encrypted in git, we can use this command:
```Bash
git show HEAD:etc/notes.txt
```

## Backup private key

if you lose your GPG private key or forget its passphrase, you will no longer be able to decrypt the files protected with git-crypt. To avoid this, make a backup of your keypair.

First, export your public key (safe to share):
```Bash
gpg --armor --export YOUR_KEY_ID > my-gpg-public.asc
```

Then export your private key (keep this file secret and secure):
```Bash
gpg --armor --export-secret-keys YOUR_KEY_ID > my-gpg-private.asc
```

These two files (.asc) along with your passphrase are enough to restore access on a new machine:
```Bash
gpg --import my-gpg-public.asc
gpg --import my-gpg-private.asc
```

## Share the encrypt content with public keys

If you want to allow another trusted person (or another machine of your own) to decrypt the protected files, you need to add their public GPG key to the repo.

### On their side

They generate a GPG key:
```Bash
gpg --full-generate-key
```

Then export their public key and send it to you:
```Bash
gpg --armor --export THEIR_KEY_ID > my-public.asc
```

### On your side

You import their key:

```Bash
gpg --import my-public.asc
```

And add them to the repo:
```Bash
git-crypt add-gpg-user THEIR_KEY_ID
git commit -m "Add access for teammate"
git push
```

From now on, when they clone the repo and run:
```Bash
git-crypt unlock
```

They will be able to transparently decrypt and edit the protected files.

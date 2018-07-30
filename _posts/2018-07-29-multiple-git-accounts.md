---
layout: post
title:  Multiple Git SSH keys by example
tags:   productivity git ssh
---

Here are my notes for setting up SSH keys for different accounts on [Bitbucket](https://bitbucket.org). These instructions should be similar for [GitHub](https://github.com) as well.

## Generate and setup SSH keys

SSH keys are stored in the `~/.ssh` folder. To look for existing private and public keys run `ls ~/.ssh`. A key pair looks like `id_rsa` for the private key and `id_rsa.pub` for the public pair.

To generate a new SSH key run-

```
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

You will be prompted for the file to save the key-

```
Enter a file in which to save the key (/Users/you/.ssh/id_rsa):
```

Configure the public key in your Github or Bitbucket settings. For multiple SSH keys, save the key with separate file names like `id_rsa_personal` and `id_rsa` (default for work).

These keys can loaded for automatic use with ssh-agent-

```
ssh-add ~/.ssh/id_rsa
ssh-add ~/.ssh/id_rsa_personal
```

This is one time setup.

## Git config file

Edit the `~/.ssh/config` file with host aliases to make the magic work.

```
# Bitbucket Personal account
Host bitbucket-personal
	HostName bitbucket.org
	User git
	IdentityFile ~/.ssh/id_rsa_personal

# Bitbucket work account (Default)
Host bitbucket.org
	HostName bitbucket.org
	User git
	IdentityFile ~/.ssh/id_rsa
```

The configuration above-

* First section creates a host alias bitbucket-personal to the real bitbucket.org. When that alias is used the identity file id_rsa_personal will be active.
* The second alias is just bitbucket.org pointing to bitbucket.org. This will be the default configuration and id_rsa key is used in that case.


This is one time setup.

## Repository config

For each git repository, the SSH key used will be based on the hostname used in the remote configuration.

Let's consider the open source project [asap-cli](https://bitbucket.org/atlassianlabs/asap-cli).

To clone using the default work SSH key run-

```
$ git clone git@bitbucket.org:atlassianlabs/asap-cli.git
```

To clone using the personal SSH use the alias path like-

```
$ git clone git@bitbucket-personal:atlassianlabs/asap-cli.git
```

Check the repository remote configuration like - 

```
$ git remote -v
origin	git@bitbucket-personal:atlassianlabs/asap-cli.git (fetch)
origin	git@bitbucket-personal:atlassianlabs/asap-cli.git (push)
```

This alias feature can also be used for adding a remote rather than a fresh clone.

This needs to be done once per repository.

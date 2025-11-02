---
title: "Basic Git and GitHub Setup and Configuration"
date: 2025-11-02T07:57:29+11:00
---

# Configuring Git and GitHub

## Installing Git on Your Laptop / Cloud Dev Space (if not already installed)

### Windows
To install Git on a Windows device, you can either download the installer from the [Git Project](https://git-scm.com/install/windows) (or follow your companies software provisioning process if you are on a locked down corporate device), or better yet, use the Windows Package Manager (aka [WinGet](https://github.com/microsoft/winget-cli)). To install using winget, open the ‘Command Prompt’ app from the Start Menu (or better yet, open [Microsoft’s Terminal App](https://github.com/microsoft/terminal)), and run the following command:

```batch
Microsoft Windows [Version 10.0.19045.6332]
(c) Microsoft Corporation. All rights reserved.

C:\Users\sam>winget install --id Git.Git -e --source winget
Found Git [Git.Git] Version 2.51.2
This application is licensed to you by its owner.
Microsoft is not responsible for, nor does it grant any licenses to, third-party packages.
Downloading https://github.com/git-for-windows/git/releases/download/v2.51.2.windows.1/Git-2.51.2-64-bit.exe
  ██████████████████████████████  63.1 MB / 63.1 MB
Successfully verified installer hash
Starting package install...
The installer will request to run as administrator. Expect a prompt.
Successfully installed
```
You will now have the following apps installed on your device:
* Git CMD
* Git Bash
* Git GUI

### Linux (i.e. Most Cloud Dev Spaces)

# Setting user.email

In GitHub:

1. Click on your profile picture in the upper right hand side, and then click 'Settings' from the drop down menu.
2. From the left hand sidebar in the settings page, click 'Emails'.
3. The top of the Emails page, there will be the following text:

> Emails you can use to sign in to your account. Your emails will not be used as the 'from' address for web-based Git operations, e.g. edits and merges. All web-based Git operations will be linked to <random_numbers>+<your_github_username>@users.noreply.github.com.

4. Copy and paste this <random_numbers>+<your_github_username>@users.noreply.github.com address somewhere; this it the address you should configure the git software with, as when GitHub is displaying all the commits that have been made to the repo, the commits made by this e-mail address are the commits it will attribute to your GitHub account.

```bash
git config --global user.email <random_numbers>+<your_github_username>@users.noreply.github.com
```

# Storing Your GitHub Credentials (securely)

Without providing git with your GitHub credentials, you won't be able to complete the most basic preqrequsitie of getting your work done; cloning your project repo to your development space. There are lots of ways to handle this, but let's focus in on an approach that doesn't require insecurely hardcoding your passwords somewhere, and doesn't require you to keep entering your password over and over again. Enter the (Git Credential Manager)[https://github.com/git-ecosystem/git-credential-manager]!

Some of the credential store backends that can be use by GCM are:
* Windows Credential Manager
* macOS Keychain
* Linux's [pass](https://www.passwordstore.org/) program

## Installing Git Credential Manager

If you installed Git for Windows, there is a very good chance that Git Credential Manager is already installed. If you want to check, run the following command:

```bash
git credential-manager --version
```

Git will also probably already be configured to use the GCM, if the following command returns 'helper' then you are good to:

```bash
git config credential.helper
```

If you are on Linux and you need to install it, the best option is probably the official install script, which you can run like this:

```bash
curl -L https://aka.ms/gcm/linux-install-source.sh | sh
```

## Configuring Git to Use GCM

```bash
git config --global credential.helper manager
```

On Windows, the credential store backend doesn't need to be explicitly set (it will use the Windows Credential Store by default), but it you are a belt and braces kinda person, you can explicitly set the chosen credential store to the Windows Credential Store with the following command:

```bash
git config --global credential.credentialStore wincredman
```

## Using GCM

This is the really easy bit; just use git as you normally would. There's no need to ever interact with the Git Credential Manager after it's been setup, git will call GCM on your behalf when it needs to. The first time you need to clone a private repo, or push changes to a repo, you will be prompted for your GitHub credentials. On Windows, you will get a dialog pop box that looks like this:

![Alt text](gcm-win-github-login-dialog-box.png "Optional Title")

If you choose the 'Sign in with your browser' option, you will be asked to sign into the GitHub website with your username and password, and after succesfully doing this GitHub will generate and provide the tool with an OAuth token to use for future logins (well, technically it will provide two tokens; a short lived access token for actually doing 'GitHub things' with, and then another more long lived refresh token to generate a new access token everytime it expires). OAuth is good option, but I tend to always use the 'Personal Access Token' option, which is what the token option in the dialog box is referring to. If you don't already have a GitHub PAT, you can generate one from your web browser: GitHub -> Settings -> Developer settings -> Personal access tokens -> Fine-grained tokens or Tokens (classic) -> Generate new token.

On Windows, if you ever need to manage the credentials stored in the Windows Credential Store, you can use the [cmd](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/cmdkey) command.

```batch
cmdkey /list
```


# Finally, Cloning Your Repo and Getting on with your Work

```bash
git clone --branch <branch-name> --single-branch --depth 1 <repo-url>
```
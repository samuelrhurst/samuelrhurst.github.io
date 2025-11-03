---
title: "Git and GitHub Setup Basics"
date: 2025-10-26
---

Introduction paragraph.

## Installing Git (if not already installed)

### Linux

### Windows
To install Git on a Windows device, you can download the installer from the [Git Project](https://git-scm.com/install/windows) (or if you are on a locked down corporate device, follow your company's software provisioning process). However, cool kids like us will use the Windows Package Manager (aka [WinGet](https://github.com/microsoft/winget-cli)) instead. To install git using winget, open the ‘Command Prompt’ app from the Start Menu (or better yet, open [Microsoft’s Terminal App](https://github.com/microsoft/terminal)), and run the following command:

```batch
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

## Basic Git Config Settings

### user.email

In GitHub:

1. Click on your profile picture in the upper right hand side, and then click 'Settings' from the drop down menu.
2. From the left hand sidebar in the settings page, click 'Emails'.
3. The top of the Emails page, there will be the following text:

> Emails you can use to sign in to your account. Your emails will not be used as the 'from' address for web-based Git operations, e.g. edits and merges. All web-based Git operations will be linked to __<random_numbers>+<your_github_username>@users.noreply.github.com__.

4. This <random_numbers>+<your_gh_username>@users.noreply.github.com address is the address you should configure the git software with, as when GitHub is displaying all the commits that have been made to the repo, the commits made by this e-mail address are the commits it will attribute to your GitHub account.

```bash
git config --global user.email <random_numbers>+<your_gh_username>@users.noreply.github.com
```

## Managing Your GitHub Credentials (securely)

Without providing git with your GitHub credentials, you won't be able to complete the most basic prerequisite of getting some work done; cloning your project repo to your development space. There are lots of ways to handle this, but let's focus on an approach that doesn't require storing your passwords in plaintext somewhere, and doesn't require you to keep entering your password over and over again. Enter the [Git Credential Manager](https://github.com/git-ecosystem/git-credential-manager)!

Some of the credential store backends that can be used by GCM are:
* Windows Credential Manager
* macOS Keychain
* Linux's [pass](https://www.passwordstore.org/) tool

### Installing Git Credential Manager

If you installed Git for Windows, there is a very good chance that Git Credential Manager is already installed. If you want to check, run the following command:

```bash
git credential-manager --version
```

On Windows, Git will also probably already be pre-configured to use GCM; if the following command returns 'helper' then you are good to go:

```bash
git config credential.helper
```

If you are on Linux and you need to install GCM, the best option is probably the official install script, which you can run like this:

```bash
curl -L https://aka.ms/gcm/linux-install-source.sh | sh
```

### Configuring Git to Use GCM

```bash
git config --global credential.helper manager
```

#### Setting a Credential Store Backend

On Windows, the credential store backend doesn't need to be explicitly set (it will use the Windows Credential Store by default), but if you are a belt and braces sortof person, you can do so with the following command:

```bash
git config --global credential.credentialStore wincredman
```

### Using GCM

This is the really easy bit; just use git as you normally would. There's no need to ever interact with the Git Credential Manager after it's been setup; git will call GCM on your behalf when it needs to. The first time you need to clone a private repo, or push changes to a repo (i.e. an action that needs authorisation), you will be prompted for your GitHub credentials. On Windows, you will get a dialog pop box from GCM that looks like this:

{{< figure src="gcm-win-github-login-dialog-box.png" alt="Example" width="315" >}}

If you choose the 'Sign in with your browser' option, you will be asked to sign into the GitHub website with your username and password, and after succesfully doing this GitHub will generate and provide GCM with an OAuth token to use for future logins (well, technically it will provide two tokens; a short lived 'access' token for actually doing 'GitHub things' with, and then another more long lived 'refresh' token to generate a replacment access token whenever it has expired). OAuth is good option, but I tend to always use the 'Personal Access Token' option, which is what the Token option in the dialog box is referring to. If you don't already have a GitHub PAT, you can generate one from GitHub in your web browser: GitHub → Settings → Developer settings → Personal access tokens → Fine-grained tokens or Tokens (classic) → Generate new token. Then paste this token into the Dialog box.

On Windows, if you ever need to manage the GitHub credentials stored in the Windows Credential Store backend, you can use the [cmdkey](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/cmdkey) command with the list argument, and then look for a credential with a 'target' value that looks something like 'LegacyGeneric:target=git:https://github.com':

```batch
cmdkey /list
```

If you ever need to delete a credential from the Windows credential store, use the /delete command with the target you found using the /list command:

```batch
cmdkey /delete:LegacyGeneric:target=git:https://github.com
```

## Cloning Your Repo and Getting on with your Work

```bash
git clone --branch <branch-name> --single-branch --depth 1 <repo-url>
```
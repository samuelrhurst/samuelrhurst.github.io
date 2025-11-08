---
title: "Git and GitHub Setup Basics"
date: 2025-10-26
draft: true
tags: ['howto', 'git', 'github']
---

Introduction paragraph.

## Installing Git

If not already installed.

### Linux

### Windows
To install Git on a Windows device, you can download the installer from the [Git Project](https://git-scm.com/install/windows) (or if you are on a locked down corporate device, follow your company's software provisioning process). However, cool kids like us will use the Windows Package Manager (aka [WinGet](https://github.com/microsoft/winget-cli)) instead. To install git using winget, open the ‘Windows Powershell’ app from the Start Menu (or better yet, open Powershell terminal using [Microsoft’s Terminal App](https://github.com/microsoft/terminal)), and run the following command:

```powershell
PS C:\Users\sam> winget install --id Git.Git -e --source winget
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

### user.name

```bash
git config --global user.name "<Your real name>"
```

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

__To-Do: Setting the credential store backend (for Mac and Linux)__

On Windows, the credential store backend doesn't need to be explicitly set (it will use the Windows Credential Store by default), but if you are a belt and braces sortof person, you can do so with the following command:

```bash
git config --global credential.credentialStore wincredman
```

### Using GCM

This is the really easy bit; just use git as you normally would. There's no need to ever interact with the Git Credential Manager after it's been setup; git will call GCM on your behalf when it needs to. The first time you need to clone a private repo, or push changes to a repo (i.e. an action that needs authorisation), you will be prompted for your GitHub credentials. On Windows, you will get a dialog pop box from GCM that looks like this:

{{< figure src="images/20252610_git_and_github_setup_basics/gcm-win-github-login-dialog-box.png" alt="Example" width="315" >}}

If you choose the 'Sign in with your browser' option, you will be asked to sign into the GitHub website with your username and password, and after succesfully doing this GitHub will generate and provide GCM with an OAuth token to use for future logins (well, technically it will provide two tokens; a short lived 'access' token for actually doing 'GitHub things' with, and then another more long lived 'refresh' token to generate a replacment access token whenever it has expired). OAuth is good option, but I tend to always use the 'Personal Access Token' option, which is what the Token option in the dialog box is referring to. If you don't already have a GitHub PAT, you can generate one from GitHub in your web browser: GitHub → Settings → Developer settings → Personal access tokens → Fine-grained tokens or Tokens (classic) → Generate new token. Then paste this token into the Dialog box.

On Windows, if you ever need to manage the GitHub credentials stored in the Windows Credential Store backend, you can use the [cmdkey](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/cmdkey) command with the list argument, and then look for a credential with a 'target' value that looks something like 'LegacyGeneric:target=git:https://github.com':

```powershell
cmdkey /list
```

If you ever need to delete a credential from the Windows credential store, use the /delete command with the target you found using the /list command:

```powershell
cmdkey /delete:LegacyGeneric:target=git:https://github.com
```

## Signing Your Commits

While not absolutely essential, it's a not a bad idea to setup your git installation to automatically sign your commits with a cryptographically verifiable signature. If you follow these steps, GitHub will add a neat 'Verified' label to all of your commits.

### Installing GPG

Windows: 

```powershell
PS C:\Users\sam> winget install --id GnuPG.Gpg4win -e
Found Gpg4win [GnuPG.Gpg4win] Version 4.4.1
This application is licensed to you by its owner.
Microsoft is not responsible for, nor does it grant any licenses to, third-party packages.
Downloading https://files.gpg4win.org/gpg4win-4.4.1.exe
  ██████████████████████████████  34.5 MB / 34.5 MB
Successfully verified installer hash
Starting package install...
Successfully installed
```

You should also take the opportunity to override the GNUPGHOME location (where the GPG keys will live) to be more 'linux-like', otherwise it will use a default location of %APPDATA%\gnupg, which will ultimately cause configuration headaches trying to get GPG to integrate nicely with git.

```powershell
PS C:\Users\sam> setx GNUPGHOME "$HOME\.gnupg"

SUCCESS: Specified value was saved.
```

### Generating Key

By default, git will look for a key using the same e-mail address that was set as user.email in the git config, so make sure when entering the e-mail address in the key generation wizard, you use the same one. Start the key generation wizard by running the `gpg --full-generate-key` command, and then make the following selections:

* Please select what kind of key you want: ECC (sign and encrypt)
* Please select which elliptic curve you want: Curve 25519
* Please specify how long the key should be valid: 0
* Real name: <your name, consistent with the name section of you GitHub profile>
* Email address: Your GitHub e-mail address i.e. <random_numbers>+<your_github_username>@users.noreply.github.com
* Comment: GitHub Key

### Telling GitHub about your key

In the terminal, copy the public key from your newly create key pair to the clipoard:

```powershell {filename="Windows, Powershell"}
gpg --armour --export <random_numbers>+<your_github_username>@users.noreply.github.com | Set-Clipboard
```
Then in GitHub:

1. In the upper-right corner of any page on GitHub, click your profile picture, then click Settings.
2. In the left hand sidebar, click  SSH and GPG keys.
3. Next to the "GPG keys" header, click New GPG key.
4. In the "Title" field, type a name for your GPG key.
5. In the "Key" field, paste the GPG key you copied when you generated your GPG key.
6. Click Add GPG key.
7. If prompted, authenticate to your GitHub account to confirm the action.

### Telling git to sign your commits

To tell git to automatically sign any commits or tags you create, set the following two git config options:

```bash
git config --global commit.gpgSign true
git config --global tag.gpgSign true
```

VS Code will use the `-c user.useConfigOnly=true` option when making commits, so you must set the `user.signingkey` git config. You can get the identifier for the key by running `gpg --list-secret-keys --keyid-format=long "<random_numbers>+<your_github_username>@users.noreply.github.com"`. You will be looking for the key with a __sec__ label to the left of it, and the key will be everything to the right of the `ed25529/` prefix. You can then use that id with the following git config command: `git config --global user.signingkey <value>`. Or alternatively, you can do it with a single command:

```powershell {filename="Powershell"}
git config --global user.signingkey ((gpg --list-secret-keys --keyid-format=long "<random_numbers>+<your_github_username>@users.noreply.github.com" | ForEach-Object { $_.ToString() -match '^sec\s+\S+/(\S+)' | Out-Null; $Matches[1] }) | Select-Object -First 1)
```

## Cloning Your Repo and Getting on with your Work

```bash
git clone --branch <branch-name> --single-branch --depth 1 <repo-url>
```
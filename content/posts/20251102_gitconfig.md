---
title: "Basic Git and GitHub Setup and Configuration"
date: 2025-11-02T07:57:29+11:00
draft: true
---

# Configuring Git and GitHub

## Installing Git on Your Laptop / Cloud Dev Space (if not already installed)

### Windows
To install Git on a Windows device, you can either download the installer from the Git Project https://git-scm.com/install/windows (or follow your companies software provisioning process if you are on a locked down corporate device), or better yet, use the Windows Package Manager (aka WinGet https://github.com/microsoft/winget-cli). To install using winget, open the ‘Command Prompt’ app from the Start Menu (or better yet, open Microsoft’s Terminal App https://github.com/microsoft/terminal), and run the following command:

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

# Finally, Cloning Your Repo and Getting on with your Work

```bash
git clone --branch <branch-name> --single-branch --depth 1 <repo-url>
```
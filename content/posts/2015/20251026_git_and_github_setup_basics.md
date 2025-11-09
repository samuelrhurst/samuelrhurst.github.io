---
title: "Git and GitHub Setup Basics"
date: 2025-10-26
tags: ['howto', 'git', 'github']
---

Working with Git and GitHub might not be the most earth shattering task for your average data professional, but it can still present some friction for new practitioners, especially those that don't come from a formal software engineering background. Taking the time to properly configure and setup Git and GitHub can reduce a lot of this friction, and let you spend more time on your analysis, and less time fighting your version control system.

## Installing Git

It's reasonably common for git to already be installed on your system (especially in cloud dev environments), so it's worth checking with the following command:

```bash
git --version
```

If it's not installed, proceed with the following installation instructions.

### Linux

You should check the official [git installation instructions](https://git-scm.com/install/linux) for your specific distro, but in general the git installation process is similar for all the different package managers.

Ubuntu:
```bash {filename="Bash"}
$ sudo apt install git
```

RHEL / Fedora:
```bash {filename="Bash"}
$ sudo dnf install git
```

### Windows
To install Git on a Windows device, you can download the installer from the [Git Project](https://git-scm.com/install/windows) (or if you are on a locked down corporate device, follow your company's software provisioning process). However, cool kids like us will use the Windows Package Manager (aka [WinGet](https://github.com/microsoft/winget-cli)) instead. To install git using winget, open Windows Powershell from the Start Menu (or better yet, open a Powershell terminal using [Microsoft’s Terminal App](https://github.com/microsoft/terminal)), and run the following command:

```powershell {filename="PowerShell"}
PS > winget install -e --id Git.Git
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

### Mac

To install git on a Mac, use the Homebrew package manager [^1]:

```zsh {filename="zsh"}
% brew install git
```

## Basic Git Config Settings

Here are three very basic, but very important git configuration settings.

### user.name

This setting will be the one that git uses to set the user name metadata fields on any commits you make. You should set it to your real name, and for neatness sake, it should ideally match what you have entered into the 'Name' field of your GitHub public profile (as opposed to your GitHub username).

```bash
git config --global user.name "<Your real name>"
```

### user.email

Despite have already set your `user.name`, the `user.email` field is arguably more important to GitHub users, as this is the field that GitHub will actually use to match commit authors to their GitHub profiles. It shouldn't be your personal e-mail though, as we obviously don't want to expose that. GitHub actually provides a special e-mail address for this very purpose, and that's the e-mail address you should configure git with.

To find your GitHub e-mail address from GitHub.com:

1. Click on your profile picture in the upper right hand side, and then click 'Settings' from the drop down menu.
2. From the left hand sidebar in the settings page, click 'Emails'.
3. The top of the Emails page, there will be the following text:

> All web-based Git operations will be linked to __<numbers+gh_username>@users.noreply.github.com__.

4. Copy and paste this <numbers+gh_username>@users.noreply.github.com address.

To set `user.email` to this address in git, run the following command:

```bash
git config --global user.email <numbers+gh_username>@users.noreply.github.com
```

### core.autocrlf

Annoyingly, text files on Windows use slightly different end-of-line characters vs Linux/Mac. Linux and Mac use a single LF (Line Feed character, i.e. /n) to mark the end of a line. Windows uses CRLF endings (a Carriage Return character, followed by the Line Feed character, i.e. /r/n). This could cause a lot of changes to show up in the git repo when someone using Windows is editing files, even though the only part of the file changing is the end-of-line characters. To avoid this, by default on Windows, git will autoconvert line endings (i.e. has a default setting for `core.autocrlf` of 'true'). This means that when you checkout files, git will automatically convert the line endings to CRLF. When you commit your changes, git will convert the line endings back to LF. In other words, your local Windows repo will have CRLF, and the GitHub repo will have LF. Which works just fine.

However, this doesn't make a lot of sense if you are going to be using a DevContainer for development purposes on Windows (and you should be using one), as you would be converting the files to Windows line endings when checking them out, but then working on them in a Linux environment that doesn't like those line endings. We can configure git to just keep everything LF, and the downsides of this are pretty much non-existent; even on Windows, python doesn't care about line endings, modern editors like VS Code don't care etc etc. On Windows, I therefore recommended setting `core.autocrlf` to 'input', which will continue to convert any CRLFs you commit to LF, but when checking out will leave the line endings as LF. In other words, everything will be LF.

```powershell {filename="PowerShell"}
PS > git config --global core.autocrlf input
```

## Managing Your GitHub Credentials (securely)

Without providing git with your GitHub credentials, you won't even be able to complete basic tasks, such as cloning your project repo into your development space. There are lots of different ways to have git manage those credentials, but let's focus on an approach that doesn't require you to store your passwords somewhere in plaintext, and doesn't require you to keep entering your password over and over again. Enter the [Git Credential Manager](https://github.com/git-ecosystem/git-credential-manager)!

Git Credential Manager is a tool that provides a consistent way for git to use a variety of different secure backends for storing credentials. Some of the credential store backends that are supported by GCM are:
* Windows Credential Manager
* macOS Keychain
* Linux's [pass](https://www.passwordstore.org/) tool

### Installing Git Credential Manager

#### Windows
If you installed Git for Windows, there is a very good chance that Git Credential Manager is already installed. If you want to check, run the following command:

```powershell
> git credential-manager --version
2.6.1+786ab03440ddc82e807a97c0e540f5247e44cec6
```

#### Linux
If you are on Linux and you need to install GCM, the best option is probably the [official install script](https://github.com/git-ecosystem/git-credential-manager/blob/release/docs/install.md#install-from-source-helper-script), which you can run like this [^2]:

```bash {filename="Bash"}
$ curl -L https://aka.ms/gcm/linux-install-source.sh | sh
```

Linux users will then also need to install the [pass](https://www.passwordstore.org/) backend:

Ubuntu/Debian:
```bash {filename="Bash"}
$ sudo apt install pass
```

Fedora/RHEL:
```bash {filename="Bash"}
$ sudo dnf install pass
```

It is also recommended to add a line to `bashrc` that will set the `GPG_TTY` environment variable to the current terminal at startup:

```bash {filename="Bash"}
$ grep -qxF 'export GPG_TTY=$(tty)' "$HOME/.bashrc" || echo 'export GPG_TTY=$(tty)' >> "$HOME/.bashrc"
```

You will also need initialise `pass` before configuring GCM to use it, and that will require generating a GPG Keypair (instructions [here](#generating-a-gpg-key-pair)). Once you have generated that key pair, initialise pass with it using the following command:

```bash {filename="Bash"}
pass init $(gpg --list-secret-keys --keyid-format=long "<numbers+gh_username>@users.noreply.github.com" 2>/dev/null | awk '/^sec/{split($2,a,"/"); print a[2]}')

```

#### Mac
On MacOS, install `pass` using Homebrew:

```zsh {filename="zsh"}
% brew install --cask git-credential-manager
```

### Configuring Git to Use GCM

To configure Git to use GCM, run the following command:

```bash
git config --global credential.helper manager
```

### Setting the GCM Backend

__Setting the Backend on Linux:__

On Linux, to use the pass/gpg backend, you will need to run the following command:

```bash {filename="Bash"}
$ git config --global credential.credentialStore gpg
```

__Setting the Backend on Windows:__

On Windows the credential store backend doesn't need to be explicitly set (it will use the Windows Credential Store by default), but if you are a belt and braces sortof person, you can explicitly set this with the following command:

```powershell {filename="PowerShell"}
PS > git config --global credential.credentialStore wincredman
```

__Setting the Backend on Mac:__

Similarly, on Mac the credential store backend doesn't need to be explicitly set (it will use MacOS Keychain by default), but if you are a belt and braces sortof person, you can explicitly set this with the following command:

```zsh {filename="zsh"}
git config --global credential.credentialStore keychain
```

### Using GCM

This is the really easy bit; just use git as you normally would. There's no need to ever interact with the Git Credential Manager after it's been setup; git will call GCM on your behalf when it needs to. The first time you need to clone a private repo, or push changes to a repo (i.e. an action that needs authorisation), you will be prompted for your GitHub credentials. On Windows, you will get a dialog pop box from GCM that looks like this:

{{< figure src="/images/20252610_git_and_github_setup_basics/gcm-win-github-login-dialog-box.png" alt="Example" width="315" >}}

If you choose the 'Sign in with your browser' option, you will be asked to sign into the GitHub website with your username and password, and after succesfully doing this GitHub will generate and provide GCM with an OAuth token to use for future logins (well, technically it will provide two tokens; a short lived 'access' token for actually doing 'GitHub things' with, and then another more long lived 'refresh' token to generate a replacement access token whenever it has expired). OAuth is good option, but I tend to always use the 'Personal Access Token' option, which is what the Token option in the dialog box is referring to.

If you don't already have a GitHub PAT, you can generate one from GitHub in your web browser: GitHub → Settings → Developer settings → Personal access tokens → Fine-grained tokens or Tokens (classic) → Generate new token. Then paste this token into the Dialog box.

### Managing Your Credentials

#### Windows

On Windows, if you ever need to manage the GitHub credentials stored in the Windows Credential Store backend, you can use the [cmdkey](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/cmdkey) command with the list argument, and then look for a credential with a 'target' value that looks something like 'LegacyGeneric:target=git:https://github.com':

```powershell {filename="PowerShell"}
PS > cmdkey /list
```

If you ever need to delete a credential from the Windows credential store, use the /delete command with the target you found using the /list command:

```powershell {filename="PowerShell"}
PS > cmdkey /delete:LegacyGeneric:target=git:https://github.com
```

## Signing Your Commits

While not absolutely essential, it's not a bad idea to setup your git installation to automatically sign your commits with a cryptographically verifiable signature. If you follow these steps, GitHub will add a neat 'Verified' label to all of your commits.

### Installing GPG

To sign your commits in a way that GitHub can verify, you will need to install [GNU Privacy Guard](https://www.gnupg.org/). Installation instructions for the various operating systems are included below.

__Linux:__

Ubuntu/Debian:
```bash {filename="Bash"}
$ sudo apt install gnupg2 pinentry pinentry-tty
```

Fedora/RHEL:
```bash {filename="Bash"}
$ sudo dnf install gnupg2 pinentry pinentry-tty
```

__Windows:__

```powershell {filename="PowerShell"}
PS > winget install -e --id GnuPG.Gpg4win
Found Gpg4win [GnuPG.Gpg4win] Version 4.4.1
This application is licensed to you by its owner.
Microsoft is not responsible for, nor does it grant any licenses to, third-party packages.
Downloading https://files.gpg4win.org/gpg4win-4.4.1.exe
  ██████████████████████████████  34.5 MB / 34.5 MB
Successfully verified installer hash
Starting package install...
Successfully installed
```

Sometimes on Windows, git will have trouble finding where GPG is installed, so it pays to explictly set the `gpg.program` config, as shown below. If running `where gpg` via cmd instead of powershell looks odd, it's because powershell has trouble with expanding the file path that gpg4win added to the PATH.

```powershell {filename="PowerShell"}
PS > git config --global gpg.program (cmd /c "where gpg" | Select-Object -First 1)
```

__Mac:__

```zsh {filename="zsh"}
brew install gnupg pinentry-mac
```

### Generating a GPG Key Pair

Start the key generation wizard by running the `gpg --full-generate-key` command, and then make the following selections:

* Please select what kind of key you want: ECC (sign and encrypt)
* Please select which elliptic curve you want: Curve 25519
* Please specify how long the key should be valid: 0
* Real name: <your name, consistent with the name section of you GitHub public profile>
* Email address: Your GitHub e-mail address i.e. <numbers+gh_username>@users.noreply.github.com
* Comment: GitHub Key
* When prompted for a passphrase to protect the key, leave it blank and press enter [^3].

### Configuring Git to Sign Your Commits with the Private Key

To tell git to automatically sign any commits or tags you create, set the following two git config options:

```bash
git config --global commit.gpgSign true
git config --global tag.gpgSign true
```

VS Code will use the `-c user.useConfigOnly=true` option when making commits, so you must set the `user.signingkey` git config. You can get the identifier for the key by running `gpg --list-secret-keys --keyid-format=long "<numbers+gh_username>@users.noreply.github.com"`. You will be looking for the key with a 'sec' label to the left of it, and the key id will be everything to the right of the `ed25529/` prefix. You can then use that id with the following git config command: `git config --global user.signingkey <keyid>`. Or alternatively, you can do it with a single command:

Linux:
```bash {filename="Bash"}
git config --global user.signingkey $(gpg --list-secret-keys --keyid-format=long "<numbers+gh_username>@users.noreply.github.com" 2>/dev/null | awk '/^sec/{split($2,a,"/"); print a[2]; exit}')
```

Windows:
```powershell {filename="PowerShell"}
PS > git config --global user.signingkey ((gpg --list-secret-keys --keyid-format=long "<numbers+gh_username>@users.noreply.github.com" | ForEach-Object { $_.ToString() -match '^sec\s+\S+/(\S+)' | Out-Null; $Matches[1] }) | Select-Object -First 1)
```

### Configuring GitHub to Verify Your Commits with the Public Key

The easy way to add the public GPG Key to your GitHub profile is use the terminal to copy it to the clipboard, and then paste it into relevant settings page in your web browser. To copy the key to the clipboard:

__Linux:__
```bash {filename="bash"}
$ gpg --armor --export "<numbers+gh_username>@users.noreply.github.com" | wl-copy
```

__Windows:__
```powershell {filename="PowerShell"}
PS > gpg --armour --export <numbers+gh_username>@users.noreply.github.com | Set-Clipboard
```

__Mac:__
```zsh {filename="zsh"}
% gpg --armor --export "<numbers+gh_username>@users.noreply.github.com" | pbcopy
```

Then in GitHub:

1. In the upper-right corner of any page on GitHub, click your profile picture, then click Settings.
2. In the left hand sidebar, click  SSH and GPG keys.
3. Next to the "GPG keys" header, click New GPG key.
4. In the "Title" field, type a name for your GPG key.
5. In the "Key" field, paste the GPG key you copied when you generated your GPG key.
6. Click Add GPG key.
7. If prompted, authenticate to your GitHub account to confirm the action.

However, the even easier way, is to use the github CLI. If you haven't already installed it, do so like this:

__Linux:__

Official linux installation instructions can be found on the [gh cli](https://github.com/cli/cli/blob/trunk/docs/install_linux.md) repo.

Ubuntu/Debian:
```bash {filename="Bash"}
(type -p wget >/dev/null || (sudo apt update && sudo apt install wget -y)) \
	&& sudo mkdir -p -m 755 /etc/apt/keyrings \
	&& out=$(mktemp) && wget -nv -O$out https://cli.github.com/packages/githubcli-archive-keyring.gpg \
	&& cat $out | sudo tee /etc/apt/keyrings/githubcli-archive-keyring.gpg > /dev/null \
	&& sudo chmod go+r /etc/apt/keyrings/githubcli-archive-keyring.gpg \
	&& sudo mkdir -p -m 755 /etc/apt/sources.list.d \
	&& echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null \
	&& sudo apt update \
	&& sudo apt install gh -y
```

Fedora/RHEL:
```bash {filename="Bash"}
sudo dnf install dnf5-plugins
sudo dnf config-manager addrepo --from-repofile=https://cli.github.com/packages/rpm/gh-cli.repo
sudo dnf install gh --repo gh-cli
```

__Windows:__
```powershell {filename="PowerShell"}
PS > winget install -e --id GitHub.cli
Found GitHub CLI [GitHub.cli] Version 2.83.0
This application is licensed to you by its owner.
Microsoft is not responsible for, nor does it grant any licenses to, third-party packages.
Downloading https://github.com/cli/cli/releases/download/v2.83.0/gh_2.83.0_windows_amd64.msi
  ██████████████████████████████  17.6 MB / 17.6 MB
Successfully verified installer hash
Starting package install...
Successfully installed
```

__Mac:__
```zsh  {filename="zsh"}
% brew install gh
```

Then login to GitHub from the CLI tool by running `gh auth login`. Select 'GitHub.com' if you are asked which instance of GitHub to use, and choose 'HTTPS' if given the choice of preferred protocol. Answer 'yes' to authenticating with your GitHub credentials, and then choose 'Paste an authentication token', and paste in your GitHub Personal Access Token. You might want to create a separate PAT for use with the GH CLI, as it will in general need a few extra allowed scopes beyond what you would allow a regular push/pull token to have.

The run one of the commands below to add your GPG Public Key to your GitHub account:

__Linux:__
```bash  {filename="Bash"}
$ gpg --armor --export "<numbers+gh_username>@users.noreply.github.com" | gh gpg-key add - --title "My Laptop Key"
```

__Windows:__

For some reason, this command only works from cmd, not powershell.
```batch {filename="cmd"}
> gpg --armour --export <numbers+gh_username>@users.noreply.github.com | gh gpg-key add - --title "My Laptop Key"
```


## Cloning Your Repo and Getting to Work

Finally, you should be able to clone your project repo, and start working on your project. If you are using VS Code, you should be able to use either the `Git: Clone` or `Dev Containers: Clone Repository in...` commands from the command palette, or if you are working directly with the git cli, run some variation of:

```bash
git clone --branch <branch-name> <https-repo-url>
```

[^1]: The homebrew package manager is not a built-in component of MacOS, and will need to be installed before using any `brew` commands. Installation instructions can be found at [brew.sh](https://brew.sh/), but basically involves downloading and running an installation script like this: `/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"`
[^2]: You shouldn't run shell scripts you find on random blog posts. Always go to the official source, confirm you trust it, and confirm the address of the script before running it.
[^3]: This is obviously less secure than setting a passphrase, but much more convenient. It's upto you to judge if the location where the GPG Keys are being stored (in this instance, your home directory) is secure enough (i.e. can anyone else access that location). If this is an unacceptable security compromise, you may wish to investigate storing your GPG Keys on a [Yubikey](https://www.yubico.com/)
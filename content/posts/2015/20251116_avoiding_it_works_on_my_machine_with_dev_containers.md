---
title: "Avoiding 'It Works on My Machine' with DevContainers"
date: 2025-11-16
draft: true
tags: ['howto', 'vscode', 'devcontainers', 'docker']
---

Data professionals often get stung by _'it works on my machine'_ issues twice; first when a colleague is trying to run your code and running into issues, and then again when you return to your own work 6mths later. Being able to recreate the exact specific environment for each project is the key to solving this issue, and Microsoft's [DevContainers](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers) extension for VS Code is one of the most popular ways to do this (over 34 million installs!)

## Containerised Environments

Containers are a core technology of modern cloud environments, and have become very popular as a way to distribute any software that has very specific configuration and dependency requirements. They're also fantastic for creating isolated development environments. You might already be in the habit of pinning your Python environment for each different project you work on, generating a requirements.txt or uv.lock file and working from a python virtual environment. That's a good start, but it won't lock down many other aspects of the environment, such as which distro you are using, what system packages are installed, environment variables etc etc.

What is needed is a way to document the ENTIRE development environment, in a way that makes it easy for other users to re-create. Microsoft has created a [Development Containers Specification](https://containers.dev/) that defines how to write a `devcontainer.json` file. These `devcontainer.json` files will contain all the details of the dev environment. Microsoft has also released a [Dev Containers VSCode Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers) that can create a dev environment based on a `devcontainer.json` file. The extension makes it so quick and easy to create a devcontainer environment when opening a project, there's no real excuse not to use it!

## devcontainer.json

__To-Do: Explain the devcontainer.json file a bit.__

If you are creating a `devcontainer.json` file from scratch for a new project, an easy way to do so is to use the `Add Dev Container Configuration File` command from the command palette:

1. Open the VS Code Command Palette (Ctrl+Shift+P or Cmd+Shift+P on macOS).
2. Run: `Dev Containers: Add Dev Container Configuration File...`
3. You will then be prompted to chose a container configuration template from a list. A good option for data projects is the `Python 3` image published by devcontainers. You might have to select `Show All Definitions` if you can't find it in the list. You will then get a choice of container images to use, the options covering different combinations of python versions and OS versions. For example, `3.12-bullseye` refers to Python v3.12 running on Debian 11 (aka Bullseye).
4. Then next prompt is to select from a list of additional features to install. Just be careful with any selections you might choose to make here, as most of them are community contributions, and involve you trusting the contributor.
5. Include optional file/directories. .github/dependabot.yml

The end result of this process is the creation of the following file:
```json {filename=".devcontainer/devcontainer.json"}
// For format details, see https://aka.ms/devcontainer.json. For config options, see the
// README at: https://github.com/devcontainers/templates/tree/main/src/python
{
	"name": "Python 3",
	// Or use a Dockerfile or Docker Compose file. More info: https://containers.dev/guide/dockerfile
	"image": "mcr.microsoft.com/devcontainers/python:2-3.12-bullseye"

	// Features to add to the dev container. More info: https://containers.dev/features.
	// "features": {},

	// Use 'forwardPorts' to make a list of ports inside the container available locally.
	// "forwardPorts": [],

	// Use 'postCreateCommand' to run commands after the container is created.
	// "postCreateCommand": "pip3 install --user -r requirements.txt",

	// Configure tool-specific properties.
	// "customizations": {},

	// Uncomment to connect as root instead. More info: https://aka.ms/dev-containers-non-root.
	// "remoteUser": "root"
}
```

__To-Do: Explain some of the options a bit.__

* __Name:__ A name for the dev container displayed in the UI.
* __Image:__ The name of an image in a container registry (DockerHub, GitHub Container Registry etc) that should use to create the dev container.

Instead of specifying an `image` option, you can specify a `build` option if you would rather use a Dockerfile that you have created. The `dockerfile` sub-option is where you specify the location of the Dockerfile you want to use. It's common to place the Dockerfile in the same `.devcontainer` dir as the `devcontainer.json`, leading to just setting the dockerfile option to "Dockerfile". Here's an example.

```json {filename=".devcontainer/devcontainer.json"}
{
	"name": "My project dev container",
	"build": {
		"dockerfile": "Dockerfile"
	}
}
```

## Dev Containers VSCode Extension

__To-Do: Explain the extension.__

### Where are things running?

In the context of DevContainer or remote environments, VS Code categorises extensions as follows:

* UI Extensions: These extensions are focused on the VS Code user interface, and run on the user's local machine, not in the development environment. Examples include themes, keymaps, and language grammars.
* Workspace Extensions: These extensions will run in the development environment.

When you are up and running with your Dev Container using VS Code, you don't necessarily need to know which is which, as when you install the extension, VS Code will install it into the correct location.
https://code.visualstudio.com/api/advanced-topics/remote-extensions#architecture-and-extension-kinds

Even if your local setup of VS Code has all your favourite extensions already installed, a lot of those extensions won't be available once you open your project in a Dev Container unless you a) reinstall those extensions again, this time in the Dev Container, or b) add those extensions to your projects devcontainer.json file. Here are some common extensions you might want to add to your devcontainer.json file:

* [Microsoft's Official Jupyter Extension](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.jupyter) (ms-toolsai.jupyter)

## Setup Instruction

### VS Code

If you haven't already, you will need to install the [Dev Containers VS Code Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers). You can do this through the Extensions sidebar panel, or by running the following command:

```bash {filename="Bash"}
$ code --install-extension ms-vscode-remote.remote-containers
Installing extensions...
Installing extension 'ms-vscode-remote.remote-containers'...
Extension 'ms-vscode-remote.remote-containers' v0.309.0 was successfully installed.
```

While not strictly required, the [Remote Explorer VS Code Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.remote-explorer) is highly recommended. It will allow you view and manage devcontainers.

```bash {filename="Bash"}
$ code --install-extension ms-vscode.remote-explorer
Installing extensions...
Installing extension 'ms-vscode.remote-explorer'...
Extension 'ms-vscode.remote-explorer' v0.5.0 was successfully installed.
```

If you think you are going to want to write your own custom Docker files to use with your devcontainer.json, you will likely find it helpful to install Microsoft's [Container Tools VS Code Extension](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-containers) while you are at it:

```bash {filename="Bash"}
$ code --install-extension ms-azuretools.vscode-containers
Installing extensions...
Installing extension 'ms-azuretools.vscode-containers'...
Extension 'ms-azuretools.vscode-containers' v2.3.0 was successfully installed.
```

### Docker

#### Linux

You should check the official [docker documentation](https://docs.docker.com/engine/install/) for installation instructions for your specific distro, but in general the docker installation process is similar for all the different package managers.

Ubuntu:
```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

RHEL / Fedora:
```bash
sudo dnf install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

#### Windows

Technically, containers for operating systems other than Linux do exist, but in the context of data professionals, a container = a Linux Docker container. Containers share the operating system kernel with the system they are running on, but for a Windows laptop, this presents an issue; the system kernel is a Windows kernel, not the required Linux kernel. The only way around this is to somehow get a Linux kernel up and running, and we do this by running a Linux Virtual Machine using [WSL2](https://learn.microsoft.com/en-us/windows/wsl/) (aka Windows Subsystem for Linux). This VM is where the Docker Engine that DevContainers extension will interact with.

To check that WSL is installed (and it is v2), run the following command in a powershell terminal:

```powershell
PS C:\Users\sam> wsl --version
WSL version: 2.6.1.0
Kernel version: 6.6.87.2-1
WSLg version: 1.0.66
MSRDC version: 1.2.6353
Direct3D version: 1.611.1-81528511
DXCore version: 10.0.26100.1-240331-1435.ge-release
Windows version: 10.0.19045.6332
```

If it is not install, run the following command in an admin powershell terminal:

```powershell
PS C:\Users\sam> wsl --install
```

To install Docker Desktop, run the following command in an admin powershell terminal:

```powershell
PS C:\Users\sam> winget install --id Docker.DockerDesktop -e
Found Docker Desktop [Docker.DockerDesktop] Version 4.49.0
This application is licensed to you by its owner.
Microsoft is not responsible for, nor does it grant any licenses to, third-party packages.
Downloading https://desktop.docker.com/win/main/amd64/208700/Docker%20Desktop%20Installer.exe
  ██████████████████████████████   543 MB /  543 MB
Successfully verified installer hash
Starting package install...
Successfully installed
```

Before VSCode will be able to find a running instance of the Docker Daemon to connect to, you will need to configure Docker Desktop to

1. Open Docker Desktop
2. Accept the Docker Subscription Service Agreement
3. Skip the login
4. Click the settings icon from the menu bar
5. Make sure the following items are checked:
	- Start Docker Desktop when you sign in to your computer
	- Use the WSL 2 based engine
6. Make sure the following item is unchecked:
	- Open Docker Dashboard when Docker Desktop starts
7. Click Apply, and then close the Docker Dashboard (Docker Desktop will continue running in the system tray)

The first time you open Docker Desktop, you may get a 'Welcome to Windows Subsytem for Linux' splash page open up. This shouldn't be a surprise, because as mentioned previously, the docker engine is running inside a linux VM. If you're curious, you can even check to see the VMs currently running in WSL with the `wsl --list` command. As shown below, Docker Desktop has launched it's own `docker-desktop` VM:

```powershell
PS C:\Users\sam> wsl --list
Windows Subsystem for Linux Distributions:
docker-desktop (Default)
```

Something to be aware of, is that Docker Desktop is only licenced for free use for personal/educational use, and for use by small businesses (fewer than 250 employees AND less than $10 million in annual revenue). If you are working for a large corporation, you might need to get them to cough up for licence, or alternatively, try to use [Podman](podman.io) as a drop in replacement for Docker.

#### Mac

__To-Do: More details about Mac.__

Install docker with the following command [^1]:

```zsh {filename="zsh"}
% brew install --cask docker
```

## Working on Your Project in a Dev Container

After opening your project folder in VS Code, as long as there is a valid `devcontainer.json` file the project folder, you can launch your DevContainer with the `Dev Containers: Reopen in Container` command. VS Code will buzz away for a moment or so, and then reopen, now running in a dev container. The first thing you might notice is that the blue icon remote/container context icon in the bottom left hand corner of VS Code will now show that you are working in a Dev Container:

{{< figure src="/images/20251107_avoiding_it_works_on_my_machine_with_dev_containers/dev_container_status.png" alt="Example" width="400" >}}

If you want to verify the details of your new devcontainer environment, in VS Code you can open a terminal and run any of the following commands:

```bash {filename="Bash"}
vscode ➜ /workspaces/my-proj $ python --version
Python 3.14.0

vscode ➜ /workspaces/my-proj $ cat /etc/os-release
PRETTY_NAME="Debian GNU/Linux 13 (trixie)"
NAME="Debian GNU/Linux"
VERSION_ID="13"
VERSION="13 (trixie)"
VERSION_CODENAME=trixie
DEBIAN_VERSION_FULL=13.1
ID=debian
HOME_URL="https://www.debian.org/"
SUPPORT_URL="https://www.debian.org/support"
BUG_REPORT_URL="https://bugs.debian.org/"

vscode ➜ /workspaces/my-proj $ uname -a
Linux 5c4752b1977c 6.6.87.2-microsoft-standard-WSL2 #1 SMP PREEMPT_DYNAMIC Thu Jun  5 18:30:46 UTC 2025 x86_64 GNU/Linux
```

Just remember, a dev containers is 'ephemeral'; you create it, do some work in it, then delete it. Then create it again (using the `devcontainer.json` file) the next time you are working on that project. Then delete it once you are done. So if you start customising it by installing libraries with `apt install` or `dnf install`, or editing system config files etc, when you delete the container, none of those changes will persist when you next recreate it. The changes also won't be there for anyone else using you `devcontainer.json` file, defeating the point of having one. So remember, if you want to make changes to the project's dev environment, do so by editing the `devcontainer.json` file.

## Remote Dev Containers

__To-Do: Details about remote execution.__

[^1]: The homebrew package manager is not a built-in component of MacOS, and will need to be installed before using any `brew` commands. Installation instructions can be found at [brew.sh](https://brew.sh/), but basically involves downloading and running an installation script like this: `/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"`
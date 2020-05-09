Ubuntu Install
==============

Here I will document the precise steps required to reproduce my installation of Ubuntu, to the best of my recollection.
It will not be perfect.

# Install Linux

I used a 2015 Macbook Pro (Version 11,4 I believe).
I created the installer on another 2015 Macbook Pro (work computer), by running the following after downloading an ubuntu image [here](https://ubuntu.com/download/desktop).
```bash
$ hdiutil convert -format UDRW -o ubuntu.img ubuntu.iso
$ sudo dd if=ubuntu.img.dmg of=/dev/rdiskN bs=1m
$ sudo diskutil eject /dev/diskN
```

# Install desktop programs

The key desktop programs I needed to install are alacritty, spotify, and slack.
I use firefox as my web browser[^1], and it comes packaged with ubuntu.

## VS Code

Microsoft packages VS Code as a deb file.
It can be downloaded and installed like
```bash
$ curl -o vscode.db -L https://go.microsoft.com/fwlink/?LinkID=760868
$ sudo apt install ./vscode.deb
```
Note that you may have to replace the download link with one provided on the vscode [website](https://code.visualstudio.com/).

# Install system packages

## Programming Languages

### Nodejs

I recommend using `nvm` to manage and isolate nodejs environments.
This is installed with
```bash
$ curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.3/install.sh | bash
```

### Java

I prefer to use OpenJDK to the proprietary OracleJDK when possible.
The java runtime open JRE can be installed with
```bash
$ sudo apt install openjdk-8-jre
```

### Rust

Rust is best installed and managed by the rusup tool:
```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

### C/C++

I use clang/clang++ for a variety of reasons.
On ubuntu this is easy to install with 
```bash
$ bash -c "$(wget -O - https://apt.llvm.org/llvm.sh)"
```

### Golang

### Latex

TexLive is the go-todistributin of LaTeX on unix.
ubuntu provides several packages of TexLive, each corresponding to different amounts of pre-installed latex libraries.
My preferred package is installed with `apt install texlive-latex-extra`.
Additionally, the `latexmk` tool is indespensible for compiling latex projects.
It is installed with `apt install latexmk`.

## Docker

Docker is also currently a mess on Ubuntu.
The docker and ubuntu development teams provide two different apt packages for docker; the docker teams lie in a ppa that they maintain.
Ubuntu's packages are, as expected, significantly behind nightly releases of docker.
However, the docker team does not sync its ppa updates with docker releases, and typically those ppas lag Ubuntu releases by about 6 months.
Thus, the only way to get docker on a recent Ubuntu release is either through the ubuntu team's apt package or by manually installing and updating .deb packages released by the docker team.
There should be a better way.

[And there is](https://podman.io/getting-started/).
Or so I thought.
Podman sounds great -- it runs containers without requiring root privileges or a system docker daemon.
It's also packaged as a snap, so it should be easy to install, right?
Wrong.
The snap claims that it is not ready for production use, and required that special flags be passed in order to install it.
This by itself isn't a big deal -- podman clearly is ready for production use, and a one-time flag is but a small inconvenience.
But the real problem is that snap *refuses* to update podman unless done so manually.
This is a dealbreaker for a development machine.

## Utilities

## Dotfiles

# Build cutting-edge software from source

I build all of my packages in `~/src`.
I currently build git, python, tmux, and neovim.
Git should be built first, and tmux is required for alacritty functionality.

## Git

## Python

## Tmux

## Neovim

# Configuration

## Dotfiles

I keep my dotfiles at [here](github.com/cderwin/dotfiles).
In order to build them, the dependency of gnu's `stow` command must be installed: `apt install stow`.
Then the dotfiles can be installed locally with a simple
```bash
git clone https://github.com/cderwin/dotfiles
cd dotfiles
make
```

## SSH

To generate an ssh key for use with github, run `ssh-keygen -t ed25518`.
Then, the public key (`~/.ssh/id_ed25519.pub`) must be uploaded to [github](https://github.com/settings/keys).
For use with work repositories, MFA must be enabled for that key.

Then, to create a github ssh remote, add the following section to `~/.ssh/config`:
```
Host github
    HostName github.com
    User git
    Port 22
    IdentityFile ~/.ssh/id_ed25519
```

## Macbook Configuration

Macbook hardware does not necessarily work out of the box.
In particular, macbooks will wake from suspend shortly after the lid is shut, resulting in the inability of the computer to hold a charge.
In order to fix this issue, systemd must beactivate the `XHC1` and `LID0` wakeup devices.
This is done by creating a `/etc/systemd/system/suspend-fix.service` file with the following contents:
```
[Unit]
Description=Fix for the suspend issue
[Service]
Type=oneshot
ExecStart=/bin/sh -c "echo XHC1 > /proc/acpi/wakeup && echo LID0 > /proc/acpi/wakeup"
[Install]
WantedBy=multi-user.target
```

To activate the systemd service, one must then run
```
systemctl enable suspend-fix.service
systemctl start suspend-fix.service
```


[^1] This is largely not by choice.
Chrome is currently broken on focal (ubuntu 20.04) -- it freezes on the 2fa page for logging into my google account -- and Chromium is currently a hot mess in focal.
It can only be installed via a snap, which means it is sandboxed in a way that breaks the functionality of common desktop application (slack, spotify, zoom) and development applications (jupyter).

# Software Distribution

## What are Linux packages

- You might know software by names like Chrome or Firefox, but on a technical level, they are **packages**

- A package is essentially an archive of files: the app's executables, config files, and documentations

- Paackages are managed by package manager

## The Software Supply Chain

- Upstream Providers

    - These are the developers who write the software
    
    - They compile the source code, create installation instructions, and release new versions and updates

- Package Maintainers

    - When a new version is ready, upstream providers send it to package maintainers
    
    - These maintainers review, manage, and distribute the software to end-users in the form of packages tailored for specific Linux distributions

## Common Package Formats

- Debian (.deb): Used by Debian and its derivatives, such as Ubuntu and Linux Mint.

- Red Hat Package Manager (.rpm): Used by Red Hat Enterprise Linux (RHEL), Fedora, and CentOS


# Package Repo

## What is a package repo

- While you could visit the download page for each piece of software, a more efficient solution is package repositories

- A package repository is a central storage location for software

- These repositories, hosted on servers across the internet

- Your system's package manager needs to know where to find these repositories, you provide it with a source link

## Central Repositories vs Third-Party Repositories

- Central Repositories

    - When you install a Linux distribution (such as Ubuntu, Debian, or Fedora), it comes pre-configured with official repositories
    
    - These are massive, central software archives maintained directly by the distribution's core team

    - `sudo apt install git`

- Third-Party Repositories

    - A software vendor creates their own repository, you add their repo to your system once

    - Then you can install and update their software using your normal package manager

    - `sudo add-apt-repository "deb https://download.docker.com/linux/ubuntu ..."`

    - `sudo apt install docker-ce`


# `tar` and `gzip`

## Archiving vs. Compression

- **Archive**: combine multiple files and directories into a single file (`tar`)

- **Compression**: reduce the file size to save disk space and speed up transfers (`gzip`)

## Compressing/Decompressing Single Files with `gzip`

- `gzip mycoolfile`

- `gunzip mycoolfile.gz`

## Creating Archives with `tar`

- `tar cvf myarchive.tar file1 file2 directory1`

- `c`: create a new archive

- `v`: verbose mode, which lists the files as they are processed

- `f`: file, which specifies that the next argument is the name of the archive file

## `tar` and `gzip` combined

```bash
# create a compressed archive
tar czvf myarchive.tar.gz file1 file2 directory1

# extracting tar and gzip archives
tar xzvf myarchive.tar.gz
```


# `rpm` and `dpkg`

## What they are

- Just like `.exe` is a single executable file, so is `.deb` and `.rpm`

    - You normally wouldn't see these if you use package repositories, but if you directly download packages, you will most likely get them in these popular formats

    - Obviously, they are exclusive to their distributions: `.deb` for Debian-based and `.rpm` for Red Hat-based

- Where is `.tar.gz`

    - `.deb` and `.rpm` are managed installation packages (like a `.msi` or `.apk` file) 

    - `.tar.gz` is just a compressed archive file (like a `.zip` file)

- To install these direct packages, you can use the package management commands: `rpm` and `dpkg`

## Package Management

```bash
# install a package
rpm -i some-package.rpm

# remove a package
rpm -e some-package.rpm

# list installed packages
rpm -qa
```


# `yum` and `apt`

## Differences from `rpm` and `dpkg`

- `dpkg` and `rpm` (low-level installers)

    - Primary Input: single, local package files (`file.deb` or `file.rpm`)

    - Repo Connection: cannot connect to online repo

    - Analogy: the local `.exe` installers on `Windows`

- `yum` and `apt` (high-level installers)

    - Primary Input: package name from a remote repo (e.g., nginx)

    - Analogy: `npm` in web dev

## Package Management

```bash
# updating & upgrading packages
sudo apt update
sudo apt upgrade
sudo apt full-upgrade

# finding & inspecting packages
apt search <package>
apt show <package>
apt list --installed

# installing & removing packages
sudo apt install <package>
sudo apt remove <package>
```
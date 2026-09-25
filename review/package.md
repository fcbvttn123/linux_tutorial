# Table of contents

- [Table of contents](#table-of-contents)
- [Software Distribution](#software-distribution)
  - [What are Linux packages](#what-are-linux-packages)
  - [The Software Supply Chain](#the-software-supply-chain)
  - [Common Package Formats](#common-package-formats)
- [Package Repo](#package-repo)
  - [What is a package repo](#what-is-a-package-repo)
  - [Central Repositories vs Third-Party Repositories](#central-repositories-vs-third-party-repositories)
  - [Check your central repo (`apt edit-sources`)](#check-your-central-repo-apt-edit-sources)
- [`tar` and `gzip`](#tar-and-gzip)
  - [Archiving vs. Compression](#archiving-vs-compression)
  - [Compressing/Decompressing Single Files with `gzip`](#compressingdecompressing-single-files-with-gzip)
  - [Creating Archives with `tar`](#creating-archives-with-tar)
  - [`tar` and `gzip` combined](#tar-and-gzip-combined)
- [`rpm` and `dpkg`](#rpm-and-dpkg)
  - [What they are](#what-they-are)
  - [Package Management](#package-management)
  - [Disadvantages](#disadvantages)
- [`yum` and `apt`](#yum-and-apt)
  - [Differences from `rpm` and `dpkg`](#differences-from-rpm-and-dpkg)
  - [Package Management](#package-management-1)


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

      - If you ever need software that isn't included in Debian's default collection (like Google Chrome, Docker, VS Code, or PostgreSQL)
      
      - The software vendor will instruct you to add a new line pointing to their own custom server URL inside your `/etc/apt/sources.list.d/` directory

    - Then you can install and update their software using your normal package manager

    - `sudo add-apt-repository "deb https://download.docker.com/linux/ubuntu ..."`

    - `sudo apt install docker-ce`

## Check your central repo (`apt edit-sources`)

```bash
sudo apt edit-sources

#deb cdrom:[Debian GNU/Linux 13.6.0 _Trixie_ - Official amd64 NETINST with firmware 20260711-09:42]/ trixie contrib mai>
deb http://deb.debian.org/debian/ trixie main non-free-firmware
deb-src http://deb.debian.org/debian/ trixie main non-free-firmware

deb http://security.debian.org/debian-security trixie-security main non-free-firmware
deb-src http://security.debian.org/debian-security trixie-security main non-free-firmware

# trixie-updates, to get updates before a point release is made;
# see https://www.debian.org/doc/manuals/debian-reference/ch02.en.html#_updates_and_backports
deb http://deb.debian.org/debian/ trixie-updates main non-free-firmware
deb-src http://deb.debian.org/debian/ trixie-updates main non-free-firmware

# This system was installed using small removable media
# (e.g. netinst, live or single CD). The matching "deb cdrom"
# entries were disabled at the end of the installation process.
# For information about how to configure apt package sources,
# see the sources.list(5) manual.
```

- Those URLs point to the official online software repositories (servers) hosted by the Debian organization

- When you run a command like s`udo apt install <package>`, APT contacts those specific web addresses

- It checks the index for the software you requested, downloads the compiled `.deb` files over HTTP, and installs them onto your system

- How the Mirror Network Works (`deb.debian.org`)

  - It is actually a Global Content Delivery Network (CDN) / Redirector

  - When your machine queries `deb.debian.org`, the service uses DNS and geolocation to silently direct your connection to the fastest, geographically closest mirror server near you


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

## Disadvantages

- The Dependency Hell Problem

  - If you install a `.rpm` directly using the `rpm -i app.rpm` command, you will often see errors like this:

    ```bash
    error: Failed dependencies:
    libcrypto.so.1.1 is needed by app-1.0.0.x86_64
    ```

  - `rpm` tells you that the software requires `libcrypto.so.1.1`, but `rpm` has no internet capabilities to go find and download it for you

  - You would have to manually find, download, and install that library

  - High-level managers like `apt` and `yum` solve this by querying central online repositories to resolve the entire dependency tree automatically


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

# example: check whether Nginx is already installed
$ apt list --installed 2> /dev/null | grep nginx
# example: if nothing comes back, check the package repository
$ apt policy nginx
nginx:
  Installed: (none)
  Candidate: 1.24.0-2ubuntu7
  Version table:
     1.24.0-2ubuntu7 500
        500 http://archive.ubuntu.com/ubuntu noble/main amd64 Packages
# example: update the package index: get the latest catalog from the repositories, it doesn't upgrade your installed packages
$ sudo apt update
# example: install nginx
$ sudo apt install nginx
# example: find exactly what files the package installed
$ dpkg -L nginx
/usr/sbin/nginx
/usr/share/doc/nginx
/usr/share/nginx
/usr/share/nginx/html
...
# example: upgrade
$ sudo apt upgrade
The following packages will be upgraded:
  curl nginx openssl
3 upgraded, 0 newly installed, 0 to remove.
```
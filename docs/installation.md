# Installation

## Why Git?

Git is the industry-standard version control system. It tracks every change to your code, lets you experiment safely on branches, collaborate with teams without overwriting each other's work, and roll back to any previous state at any time. Nearly every software project — from personal scripts to large open-source projects — uses Git.

Without Git (or any VCS), the only options are manual backups, careful file naming, and hoping nothing breaks. With Git, every mistake is recoverable and every decision is documented.

---

## Windows

### Option 1 — Git for Windows (recommended)

Download and run the installer from the [official site](https://git-scm.com/download/win). The installer includes Git Bash, Git GUI, and shell integration.

```powershell
# Verify after install
git --version
```

### Option 2 — winget

```powershell
winget install --id Git.Git -e --source winget
```

### Option 3 — Scoop

```powershell
scoop install git
```

### Option 4 — Chocolatey

```powershell
choco install git
```

> After installation on Windows, restart your terminal (or open Git Bash) for the `git` command to be available in PATH.

---

## Linux

### Debian / Ubuntu

```bash
sudo apt update && sudo apt install git -y
```

### Fedora

```bash
sudo dnf install git -y
```

### RHEL / CentOS 7

```bash
sudo yum install git -y
```

### Arch Linux / Manjaro

```bash
sudo pacman -S git
```

### openSUSE

```bash
sudo zypper install git
```

---

## Verify Installation

```bash
git --version
# git version 2.x.x
```

## First Steps After Install

Set your identity — Git attaches this to every commit you make:

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

See [Configuration](configuration.md) for full setup options.

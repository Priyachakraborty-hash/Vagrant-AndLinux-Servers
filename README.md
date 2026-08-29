# Vagrant & Linux Learning Documentation

## Table of Contents
1. [Overview](#overview)
2. [Environment Setup](#environment-setup)
3. [Vagrant Fundamentals](#vagrant-fundamentals)
4. [Common Workflows](#common-workflows)
5. [Vagrant Commands Reference](#vagrant-commands-reference)
6. [Boxes Used](#boxes-used)
7. [Directory Structure](#directory-structure)
8. [Troubleshooting](#troubleshooting)
9. [Learning Progress](#learning-progress)

---

## Overview

This documentation covers practical experience with **Vagrant** - an infrastructure-as-code tool for managing virtual machines, and **Linux** - specifically CentOS and Ubuntu distributions.

**Learning Goals:**
- Set up and manage virtual machines locally using Vagrant
- Understand Linux file systems and command-line operations
- Practice SSH connections and VM configuration
- Learn infrastructure provisioning workflows

---

## Environment Setup

### Prerequisites
- Vagrant installed and configured
- VirtualBox or other hypervisor
- Windows (Git Bash / WSL) or Linux host system
- Access to Vagrant Cloud for downloading boxes

### Directory Structure
```
C:/vagrantvms/
├── centos/
│   ├── Vagrantfile
│   └── .vagrant/
└── ubuntu/
    ├── Vagrantfile
    └── .vagrant/
```

### Initial Setup
1. Create project directory:
   ```bash
   mkdir -p /c/vagrantvms/{centos,ubuntu}
   cd /c/vagrantvms/centos
   ```

2. Initialize Vagrant project:
   ```bash
   vagrant init [box-name]
   ```

---

## Vagrant Fundamentals

### What is Vagrant?
Vagrant is a tool for building and maintaining portable development environments. It allows you to define your infrastructure in code (Vagrantfile) and spin up identical VMs on any machine.

### Key Concepts

| Concept | Definition |
|---------|-----------|
| **Vagrant Box** | A pre-built VM image (OS + base software) |
| **Vagrantfile** | Configuration file that defines VM settings |
| **Provider** | Hypervisor backend (VirtualBox, Hyper-V, etc.) |
| **Provisioning** | Automated setup/installation inside the VM |
| **Synced Folders** | Shared directories between host and VM |

---

## Common Workflows

### Workflow 1: Create & Launch a VM

```bash
# Navigate to project directory
cd /c/vagrantvms/centos

# Initialize Vagrant with a box
vagrant init eurolinux-vagrant/centos-stream-9

# Start the VM
vagrant up

# SSH into the VM
vagrant ssh

# Check OS info inside VM
cat /etc/os-release
```

### Workflow 2: Stop & Resume VM

```bash
# Check VM status
vagrant status

# Halt (gracefully stop) VM
vagrant halt

# Resume halted VM
vagrant up
```

### Workflow 3: Destroy & Cleanup

```bash
# Remove VM (keeps Vagrantfile)
vagrant destroy -f

# Remove all local VMs (be careful!)
vagrant destroy 707a59b  # By ID
```

### Workflow 4: Manage Multiple VMs Globally

```bash
# View all Vagrant VMs on system
vagrant global-status

# Cleanup invalid entries
vagrant global-status --prune

# Clean up with force flag
vagrant global-status --prune --force
```

### Workflow 5: Edit Configuration

```bash
# Edit Vagrantfile (Windows)
notepad Vagrantfile

# Edit Vagrantfile (Linux)
nano Vagrantfile
vim Vagrantfile
```

---

## Vagrant Commands Reference

### Initialization & Lifecycle

| Command | Purpose | Notes |
|---------|---------|-------|
| `vagrant init [box]` | Create new Vagrantfile | Must be run in empty directory |
| `vagrant up` | Create and boot VM | Runs provisioners if configured |
| `vagrant ssh` | Connect to VM via SSH | Interactive shell into VM |
| `vagrant halt` | Gracefully stop VM | VM can be resumed |
| `vagrant suspend` | Pause VM (save state) | Faster than halt |
| `vagrant resume` | Wake suspended VM | Restores saved state |
| `vagrant destroy` | Delete VM completely | Use `-f` flag to skip confirmation |
| `vagrant reload` | Restart VM | Useful after Vagrantfile changes |

### Status & Information

| Command | Purpose |
|---------|---------|
| `vagrant status` | Show current VM status |
| `vagrant global-status` | List all VMs on system |
| `vagrant global-status --prune` | Clean up stale entries |
| `vagrant box list` | Show installed boxes |
| `vagrant box remove [box]` | Delete a box locally |

### Provisioning & Configuration

| Command | Purpose |
|---------|---------|
| `vagrant provision` | Run provisioners without reboot |
| `vagrant reload --provision` | Restart and provision |

---

## Boxes Used

### CentOS Variants

**eurolinux-vagrant/centos-stream-9**
- Stream release (cutting edge updates)
- Red Hat compatible
- Good for learning Enterprise Linux

**geerlingguy/centos**
- Community-maintained image
- Optimized for Vagrant
- Good documentation

**centos/7**
- Original CentOS 7 box
- Older but stable

### Ubuntu

**ubuntu/jammy64**
- Ubuntu 22.04 LTS (64-bit)
- Long-term support release
- Debian-based Linux

### Finding More Boxes
- Official Vagrant Cloud: https://app.vagrantup.com/boxes/search
- Search by distribution or use case

---

## Directory Structure

### Working Directories on Host

```
C:\ (Windows C: drive)
├── vagrantvms/               # Main Vagrant projects
│   ├── centos/              
│   │   ├── Vagrantfile      # CentOS VM configuration
│   │   └── .vagrant/        # VM metadata (auto-generated)
│   └── ubuntu/
│       ├── Vagrantfile      # Ubuntu VM configuration
│       └── .vagrant/        # VM metadata (auto-generated)
│
├── vagrant-vms/             # Earlier attempt (not used)
│
└── Users/cpriy/OneDrive/Desktop  # Home directory
```

### Inside VM (Linux Filesystem)

```
/
├── etc/               # System configuration
│   └── os-release    # OS version info
├── home/              # User home directories
├── var/               # Variable data (logs, etc)
└── usr/               # User programs & libraries
```

### Common Commands for File Navigation

| Command | Purpose | Example |
|---------|---------|---------|
| `pwd` | Print working directory | `pwd` → `/c/vagrantvms/centos` |
| `ls` | List directory contents | `ls` → shows files/folders |
| `cd [path]` | Change directory | `cd /c/vagrantvms/centos` |
| `mkdir -p [path]` | Create nested directories | `mkdir -p /c/vagrantvms/centos` |
| `cat [file]` | Display file contents | `cat Vagrantfile` |
| `clear` / `cls` | Clear terminal screen | `clear` (Linux/Git Bash) |

---

## Troubleshooting

### Common Issues & Solutions

#### Issue: "vagrant command not found"
**Solution:**
- Ensure Vagrant is installed: `vagrant --version`
- Add to system PATH if needed
- Restart terminal after installation

#### Issue: Vagrant file location errors
**Symptoms:** Error about Vagrantfile not found
**Solution:**
```bash
# Make sure you're in correct directory
pwd
cd /c/vagrantvms/centos

# Confirm Vagrantfile exists
ls -la Vagrantfile
```

#### Issue: Multiple VMs causing confusion
**Solution:**
```bash
# Clean up stale entries
vagrant global-status --prune

# Destroy specific VM by ID
vagrant destroy 707a59b

# Fresh start
vagrant destroy -f
```

#### Issue: Port conflicts
**Solution:** Modify Vagrantfile to use different ports:
```ruby
config.vm.network "forwarded_port", guest: 80, host: 8080
```

#### Issue: SSH connection refused
**Symptoms:** "Permission denied" or connection timeout
**Solution:**
```bash
# Ensure VM is running
vagrant status

# Try SSH with verbose output
vagrant ssh -- -v

# Destroy and recreate
vagrant destroy -f
vagrant up
vagrant ssh
```

#### Issue: Out of disk space
**Solution:**
```bash
# Remove unused boxes
vagrant box list
vagrant box remove [box-name]

# Remove old VMs
vagrant destroy 707a59b
```

---

## Learning Progress

### What I've Learned ✓

- [x] Basic Vagrant initialization
- [x] Starting and stopping VMs
- [x] SSH into virtual machines
- [x] Managing Vagrantfile configuration
- [x] Working with multiple VMs
- [x] Global status management
- [x] Basic Linux navigation (`cd`, `ls`, `pwd`)
- [x] Viewing system info (`cat /etc/os-release`)
- [x] File permissions and editing (nano, vim, notepad)
- [x] VM lifecycle (create, halt, destroy)

### Next Steps to Explore

- [ ] **Provisioning:** Automate setup with shell scripts or configuration management (Ansible, Chef)
- [ ] **Synced Folders:** Share files between host and VM
- [x] **Multiple VMs:** Coordinate multiple machines (networking, communication)
- [ ] **Custom Boxes:** Build and package custom Vagrant boxes
- [ ] **Vagrantfile Templates:** Create reusable configuration templates
- [ ] **Linux Package Management:** Learn `apt-get`, `yum`, `dnf` commands
- [ ] **System Administration:** User management, permissions, services
- [ ] **Networking:** Configure IP addresses, port forwarding, networks

---

## Quick Reference Cheat Sheet

```bash
# Setup
vagrant init ubuntu/jammy64
vagrant up
vagrant ssh

# Management
vagrant status              # Current VM status
vagrant global-status       # All VMs on system
vagrant halt                # Stop VM
vagrant destroy -f          # Delete VM

# Inside VM
cat /etc/os-release         # Show OS info
sudo apt-get install vim    # Install packages (Ubuntu)
exit                        # Leave SSH session

# Cleanup
vagrant global-status --prune
vagrant destroy 707a59b     # By ID
```

---



---

**Last Updated:** 2026-08-29  
**Status:** Active Learning Document  
**Difficulty Level:** Beginner to Intermediate

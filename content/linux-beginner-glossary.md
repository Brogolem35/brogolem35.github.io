+++
draft = false
date = "2025-09-04"
title = "Linux Beginner Glossary"
tags = ["linux", "glossary"]
unlisted = false
+++

This post is made with the intention of teaching common terms and tools to people who
have little to no experience with Linux. Because of the target demographic, the given
descriptions prioritize simplicity over accuracy and may intentionally include imperfect
information. If you want to make improvements to the post, feel free to [create an issue or open a PR](https://github.com/Brogolem35/brogolem35.github.io).

The terms are ordered according to their importance and commonness. If you have zero
experience with Linux, I would highly recommend learning at least the first 15 terms. 

### Linux

An open source [kernel](#kernel) and the [family of operating systems](#distributiondistro)
built on top of the said kernel.

The operating system can sometimes be referred to as GNU/Linux, and most of the time they
mean the same thing. I won't be getting into the details of it in this post.

### Kernel

The core of an operating system, which handles software-hardware interactions, process
scheduling, and many other important tasks. Has elevated privileges on the hardware level
and all other processes run on top of it.

### Distribution/Distro

There is no **one absolute Linux operating system**. There are many operating systems
built on top of the [Linux kernel](#linux) and they are commonly referred to as distributions
(distro for short). While different distros are referred to as separate operating systems,
programs written for one work on others as well (save for some edge cases), as long
as the distro provides the [dependencies](#dependency).

The popular distros include Linux Mint, Debian, and Arch.

### Command-Line Shell

An interface that gives the user broad access to the system through either the execution
of commands entered into a prompt or shell scripts.

Usually just referred to as shell.

### root User

The superuser/admin user of a Linux system. Has absolute permission.

Logging in as the root should be avoided. If an operation requires root privileges, it
would be advised to use [sudo](#sudo).

### sudo

A [shell](#command-line-shell) utility that allows a regular user to execute a command as the [root user](#root-user).
It is useful, as some tools require root permissions to run. But the user must be careful
when using as it temporarily grants absolute power and is a common target for malware
to exploit.

Contrary to popular belief, using sudo does not solve all your problems. If you have
problems while running a command, just slapping sudo in front of it without understanding
the cause may not only not solve it but can also cause harm to the system. It is a
common rookie mistake.

### man pages

A form of documentation for Linux and other UNIX-like systems. It is the common way of
getting information for commands, software, and operating system features. It is a **man**ual.

To learn more, run `man man` on your [shell](#command-line-shell).

### Bash

A commonly used [shell](#command-line-shell) implementation and a scripting language. It
is pre-installed on almost every Linux [distro](#distributiondistro) and is the default
on most of them.

### Package Manager

A software to help users to manage the software installed on the system. It handles the
installation, updating, and removal of software along with their [dependencies](#dependency).

In Linux, the common way of installing software, unlike in Windows, is not to download
an installer from a website but to use the package manager or managers of the distro.

Commonly used package managers include `apt` (used by Linux Mint and Debian), `pacman` (Arch),
and [Flatpak](#flatpak) (universal).

### Flatpak

A universal package manager, meaning that the software installed and managed by Flatpak
can work on any Linux distro, and the installation of the software using it will not cause
[conflicts](#dependency-conflict) with other package managers.

Unlike with most other package managers, software installed with Flatpak will run in a sandboxed
environment. The amount of access the software has on the system is decided by the Flatpak's
permission system, akin to Android's permission system. [Flatseal](https://flathub.org/apps/com.github.tchx84.Flatseal)
can be used to manage permissions easily.

Currently, Flatpak is generally used with applications with graphical user interfaces.
While it is is possible to install and use [shell](#command-line-shell) applications with
Flatpak, it is mostly avoided, as they are not ergonomic to use with it.

### Dependency

A program or [library](#shared-object) that is required by another software to function.

### Dependency Conflict

Occurrence of multiple software depending on the same software of different versions.
It is the job of the [package manager](#package-manager) to avoid and handle such conflicts.

### Desktop Environment

A software bundle that implements a complete desktop experience. They generally
include taskbars, desktop widgets, and a [window manager](#window-manager).

Commonly used desktop environments are KDE Plasma, GNOME, and Cinnamon.

### Window Manager

A software that manages the placement of the windows. Can be used standalone or be a part
of a [desktop environment](#desktop-environment).

### Wine

A compatibility layer that allows execution of Windows applications on Linux and other
UNIX-like systems. Running under Wine may cause the application to have issues such as
specific features not working, decrease in responsiveness, and worse performance.

Wine, by itself, does not provide isolation and does not prevent malware from being run.

### Proton

A derivative of [Wine](#wine) that is more focused on video games. Can be obtained
through Steam or [ProtonUp-Qt](https://github.com/DavidoTek/ProtonUp-Qt).

### X11

A [display server protocol](#display-server) for Linux and other UNIX-like systems. It
can also be referred to as X Window System, or just X. The commonly used X11 server implementation
is X.org.

X11 is a legacy protocol with limited support. It is generally advised to use [Wayland](#wayland),
X11's successor, unless you have specific needs or have Wayland specific problems.

### Wayland

A [display server protocol](#display-server) for Linux and other UNIX-like systems.

### Display Server

A process that is the core of the graphical user environment. It handles device inputs,
window placement and order, window focus, and other miscellaneous functionality.

Processes that interact with the user through a graphical user interface have to communicate
with the display server in order to accomplish even the simplest tasks, such as creating
a window. The set of rules of the communication is referred to as display server protocol. 

They can be referred to as compositors in [Wayland](#wayland) context.

### Daemon

A background process that is generally not directly interacted with. They sometimes can be
referred to as services. 

### init Process

The first process that runs at the startup. It is used to initialize important components
of the system, such as device drivers and [daemons](#daemon).

### systemd

The most commonly used [init process](#init-process) implementation. It is also bundled
with other tools that were designed to be used with it.

The main way of interacting with systemd is through the `systemctl` command.

### Shared Object

A library that contains executable code that is shared by multiple processes or libraries
at runtime, akin to Windows's DLL files. Shared object files commonly have the `.so` file
extension.

Commonly referred to as shared library, dynamic library, and DLL.

### glibc

The most commonly used C Standard [Library](#shared-object) implementation on Linux systems.
Almost all Linux software [depends](#dependency) on it.

### Root Directory

The top-most directory in the file hierarchy. Has the file path `/`.

Can be referred to as the root folder.

### Kernel Panic

An occurrence when the [kernel](#kernel) enters an unrecoverable failure state. It is
a very rare occurrence and mostly stems from faulty hardware or driver.

In modern distros, kernel panics display a QR code in a blue screen. The QR code is for
a URL that contains information on the occurred panic. An example of the said blue screen
can be found [here](https://bbs.archlinux.org/viewtopic.php?pid=2256536#p2256536).

![Logo](https://user-images.githubusercontent.com/24392180/66733887-b52b4780-ee69-11e9-86ee-ab04caf98287.png)

# zps [![Release](https://img.shields.io/github/release/orhun/zps.svg?color=590000&style=flat-square)](https://github.com/orhun/zps/releases)

### A small utility for listing and reaping zombie processes on GNU/Linux.

![zps](https://user-images.githubusercontent.com/24392180/66898210-64e6ed80-f001-11e9-8e27-0ab3a7cabdca.gif)

[![Build](https://img.shields.io/github/workflow/status/orhun/zps/Continuous%20Integration?color=black&style=flat-square)](https://github.com/orhun/zps/actions?query=workflow%3A%22Continuous+Integration%22)
[![Docker Build](https://img.shields.io/github/workflow/status/orhun/zps/Docker%20Automated%20Builds?color=black&style=flat-square&label=docker)](https://github.com/orhun/zps/actions?query=workflow%3A%22Docker+Automated+Builds%22)
[![Codacy](https://img.shields.io/codacy/grade/3d40a551806b4c788befba6d2920675b.svg?color=black&style=flat-square)](https://www.codacy.com/manual/orhun/zps?utm_source=github.com&amp;utm_medium=referral&amp;utm_content=orhun/zps&amp;utm_campaign=Badge_Grade)
[![Codecov](https://img.shields.io/codecov/c/github/orhun/zps?color=black&style=flat-square)](https://codecov.io/gh/orhun/zps)
[![Stars](https://img.shields.io/github/stars/orhun/zps.svg?color=590000&style=flat-square)](https://github.com/orhun/zps/stargazers) [![License](https://img.shields.io/github/license/orhun/zps.svg?color=590000&style=flat-square)](./LICENSE)

On Unix and Unix-like computer operating systems, a [zombie process](https://en.wikipedia.org/wiki/Zombie_process) or defunct process is a process that has completed execution (via the [exit](https://en.wikipedia.org/wiki/Exit_(system_call)) system call) but still has an entry in the process table. This occurs for child processes, where the entry is still needed to allow the parent process to read its child's exit status: once the exit status is read via the [wait](https://en.wikipedia.org/wiki/Wait_(system_call)) system call, the zombie's entry is removed from the process table and it is said to be "reaped".

Unlike the normal processes, zombie processes cannot be removed from a system with the [kill](https://en.wikipedia.org/wiki/Kill_(command)) command since they are already dead. (This is where the term's metaphor [zombie - an undead person] comes from.) To reap a zombie process, `SIGCHLD` signal can be sent to the parent process manually using the [kill](https://en.wikipedia.org/wiki/Kill_(command)) command. If the parent process refuses to reap the zombie, then terminating the parent process (mostly with `SIGTERM` signal) can be an option. When a child process loses its parent, [init](https://en.wikipedia.org/wiki/Init) process becomes its new parent and it will reap any zombies since it executes the [wait](https://en.wikipedia.org/wiki/Wait_(system_call)) system call periodically.

Zombie processes are not harmful since they are not affecting other processes or using any system resources. However, they do retain their [process ID](https://en.wikipedia.org/wiki/Process_identifier). This can lead to preventing new processes to launch if all the available PIDs were assigned to zombie processes. Considering Unix-like systems have a finite number of process IDs (`/proc/sys/kernel/pid_max`), it's one of the problems that zombie processes can cause. Another danger of zombie processes is that they can cause [resource leak](https://en.wikipedia.org/wiki/Resource_leak) if they stay as a zombie in the process table for a long time. Apart from these issues, having a few zombie processes won't be a big deal for the system although they might indicate a bug with their parent process.

[zproc.c](https://github.com/orhun/zps/blob/master/example/zproc.c) file can be compiled and run to see how zombie processes are created.
```
cd example/ && gcc -O3 -Wall zproc.c -o zproc && ./zproc
```

__zps__ aims to list the running processes at a particular time with stats and indicate the zombie processes on this list. It can also reap these zombie processes automatically if `--reap` argument is provided. There's also `--lreap` argument for reaping zombie processes after listing. See [usage](https://github.com/orhun/zps#usage) for more information.    
Technically, __zps__ reads process stats from [/proc](https://www.tldp.org/LDP/Linux-Filesystem-Hierarchy/html/proc.html) filesystem and uses [C POSIX library](https://en.wikipedia.org/wiki/C_POSIX_library) to handle listing, sending signals and other operations.

  - [Installation](#installation)
    - [Arch Linux](#arch-linux)
    - [Fedora Linux](#fedora-linux)
    - [CMake](#cmake)
    - [Make](#make)
    - [GCC](#gcc)
    - [Docker](#docker)
      - [Building an image](#building-an-image)
      - [Running the image in container](#running-the-image-in-container)
  - [Usage](#usage)
    - [zps -r](#zps--r)
    - [zps -x](#zps--x)
    - [zps -l](#zps--l)
    - [zps -p](#zps--p)
  - [TODO(s)](#todos)
  - [License](#license)
  - [Copyright](#copyright)

## Installation

### Arch Linux

```
pacman -S zps
```

### Fedora Linux

```
dnf install zps
```

### CMake

```
mkdir -p build && cd build
cmake ../ -DCMAKE_INSTALL_PREFIX=/usr
make
sudo make install
sudo ldconfig
```

### Make

```
make
sudo make install
```

### GCC

```
cd src/ && gcc -s -O3 -Wall -Wextra -pedantic zps.c -o zps
```

### Docker

#### Building an image

```
docker build -f docker/Dockerfile -t zps .
```

#### Running the image in container

```
docker run zps
```

## Usage

```
Usage:
  zps [options]

Options:
  -r, --reap      reap zombie processes
  -x, --lreap     list and reap zombie processes
  -l, --list      list zombie processes only
  -p, --prompt    show prompt for selecting processes
  -f, --fd <num>  set maximum file descriptors (default: 15)
  -s, --silent    run in silent mode
  -v, --version   show version
  -h, --help      show help
```

### zps -r

![zps -r](https://user-images.githubusercontent.com/24392180/66898345-b68f7800-f001-11e9-86d7-694772a46ab7.gif)

### zps -x

![zps -x](https://user-images.githubusercontent.com/24392180/66898624-34ec1a00-f002-11e9-9d5a-dde84c925119.gif)

### zps -l

![zps -l](https://user-images.githubusercontent.com/24392180/67201180-5f791100-f40e-11e9-8ff6-fcbbca443e9a.gif)

### zps -p

![zps -p](https://user-images.githubusercontent.com/24392180/67624534-3c999300-f83a-11e9-95e4-46c3ce586197.gif)

## TODO(s)

* Improve listing processes for long process names.
* Send `SIGCHLD` signal to the parent instead of terminating it.

## License

GNU General Public License v3.0 only ([GPL-3.0-only](https://www.gnu.org/licenses/gpl.txt))

## Copyright

Copyright © 2019-2022, [Orhun Parmaksız](mailto:orhunparmaksiz@gmail.com)

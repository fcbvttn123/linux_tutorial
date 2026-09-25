# Table of contents

- [Table of contents](#table-of-contents)
- [`ps` (Processes)](#ps-processes)
  - [What is a Linux Process](#what-is-a-linux-process)
  - [Popular `ps` commands](#popular-ps-commands)
  - [Real-Time Monitoring with `top`](#real-time-monitoring-with-top)
  - [Controlling Terminal](#controlling-terminal)
  - [Process Creation (`fork`, `exec`)](#process-creation-fork-exec)
  - [Process Termination (`_exit`, `wait`)](#process-termination-_exit-wait)
  - [Zombie vs Orphan Process](#zombie-vs-orphan-process)
- [Signals](#signals)
  - [The Purpose of Signals](#the-purpose-of-signals)
  - [The Signal Lifecycle](#the-signal-lifecycle)
  - [Common Linux Process Signals](#common-linux-process-signals)
- [Niceness](#niceness)
  - [How the CPU Manages Processes](#how-the-cpu-manages-processes)
  - [What is Niceness in Linux](#what-is-niceness-in-linux)
  - [Adjusting Process Priority](#adjusting-process-priority)
- [Job Control](#job-control)
  - [What it is](#what-it-is)
  - [Commands](#commands)
- [Tracking Process: `top`](#tracking-process-top)
  - [CPU Usage Breakdown](#cpu-usage-breakdown)
  - [RAM](#ram)


# `ps` (Processes)

## What is a Linux Process

- A process is a program in execution

- More precisely, it is an instance of a running program to which the system has allocated resources like memory, CPU time, and I/O

- The Linux kernel manages processes, and each process is assigned a unique `PID`

    - When you execute a program, the kernel loads its code into memory, allocates necessary system resources, and begins tracking it as a process
    
    - The kernel maintains detailed information for each process

    - All active processes compete for system resources
    
    - The kernel acts as a scheduler, ensuring that each process receives a fair share of resources

- Basic ps Command Usage

    ```bash
    $ ps

    PID     TTY     STAT   TIME       CMD
    41230   pts/4   Ss     00:00:00   bash
    51224   pts/4   R+     00:00:00   ps
    ```

- Key details

    - `PID`: The unique Process ID

    - `TTY`: The controlling terminal for the process

    - `STAT`: The current status of the process

    - `TIME`: The total CPU time the process has used

    - `CMD`: The command that started the process

## Popular `ps` commands

- `ps aux` (BSD Style)

- `ps -ef` (System V style)

## Real-Time Monitoring with `top`

- `ps` only gives you a snapshot

- `top` provides a real-time, dynamic view of the processes

## Controlling Terminal

-  What is a TTY

   - TTY is an abbreviation for "Teletype," which historically was a physical device for interacting with a computer

   - In modern Linux systems, a TTY refers to the terminal that provides the standard input and output for a process

   - There are two main types of terminals you will encounter: **terminal** devices and **pseudo-terminal** devices

-  Terminal Devices vs Pseudo-Terminals

   - A true **terminal device** is a native console that allows you to type commands and see output directly

     - You can experience this by switching to a virtual console
     
     - On many systems, you can press `Ctrl-Alt-F1` to access TTY1
     
     - You'll see a login prompt in a purely text-based environment, with no graphical interface
     
     - This is a classic terminal device
     
     - To return to your graphical session, you can typically use `Ctrl-Alt-F7` (the exact key combination may vary)

   - A `pseudo-terminal (PTS)`, on the other hand, is what you most commonly use

       - When you open a terminal application within your graphical desktop environment, you are using a `PTS`
       
       - These emulate a terminal within a window
       
       - If you check the `ps tty` output for your shell, you will see its TTY listed as `pts/*`

-  The Role of the Controlling Terminal

   - Most processes are bound to a controlling terminal

   - This means the process's lifecycle is tied to the terminal session that started it

   - For example, if you run a program like `find` in your terminal window and then close that window, the `find` process will also be terminated

-  Processes Without a Controlling Terminal

   - Some processes, known as `daemons`, are designed to run in the background and manage system services

   - These processes often start when the system boots and stop only when it shuts down

   - In the output of `ps`, it has `?` in the `TTY` column

## Process Creation (`fork`, `exec`)

- The `fork` and `exec` Model

    - The primary mechanism for process creation involves an existing process cloning itself using the `fork` system call

    - The `fork` call creates a nearly identical child process

    - This new child process receives its own unique PID

    - The original process becomes its parent, identified by a Parent Process ID (PPID)

    - After forking, the child process can either continue running the same program as its parent or

    - More commonly, use the `execve` system call to load and run a new program

    - We can observe this parent-child relationship in action using the ps command: `ps l`

- The `init` process

    - If every process is a child of another, there must be an original ancestor (the `init` process)

    - When the system boots, the kernel creates `init` as the very first user-space process, assigning it a `PID` of `1`

    - The `init` process is the ultimate parent of all other processes and runs with `root` privileges to manage the system

    - It cannot be terminated until the system shuts down and is responsible for spawning many of the services that keep the system running

## Process Termination (`_exit`, `wait`)

- Step 1: child (`_exit`)

    - A process typically terminates by calling the `_exit` system call

    - Upon exiting, the process provides a termination status to the kernel, which is an integer value

    - By convention, a status of `0` indicates successful execution, while a non-zero value signals an error

- Step 2: parent (`wait`)

    - Calling `_exit` doesn't immediately erase the process
    
    - The parent process must acknowledge its child's termination by using the `wait` system call

    - This call allows the parent to retrieve the child's termination status

    - This two-step mechanism is essential for proper process cleanup

    - Another way to `linux kill child` process is by using `signals`

## Zombie vs Orphan Process

- `orphan process`

    - An active, running process whose parent has died
    
    - It is adopted by `init` and continues to execute until it finishes

- `zombie process` (Z state)

    - They are dead processes whose parent hasn't read their **exit status** via `wait()`
    
    - Zombies consume no CPU and no RAM, the only resource a zombie consumes is a PID entry in the OS process table

    - Find the parent process ID (PPID) using `ps -eo pid,ppid,stat,cmd | grep "Z"`

        ```bash
          PID   PPID S CMD
        12345   6789 Z [myprogram] <defunct>
        12456   6789 Z [worker] <defunct>
        ```

    - Find the parent process using `ps -p 6789 -o pid,ppid,state,cmd`

        ```bash
         PID    PPID S CMD
        6789    1200 S ./my-parent-program
        ```

    - You generally **cannot kill a zombie itself — it's already dead**. The parent needs to call `wait()` to collect the child's exit status


# Signals

## The Purpose of Signals

- A `signal` is a software interrupt sent to a process to notify it that an important event has occurred

- Each signal is defined by an integer, but they are almost always referred to by their symbolic names, which start with `SIG`

- You can send signals to processes using utilities like `kill` (e.g., `kill -9 1234`)

## The Signal Lifecycle

- When an event generates a `signal`, it is first delivered to a target process

- The `signal` remains in a "pending" state until the kernel runs the process

- When the process is scheduled, the `signal` is delivered

- However, processes have `signal masks`, which can be configured to block the delivery of specific signals

## Common Linux Process Signals

- `SIGHUP` (1): Hangup. Often used to tell a `daemon` to reload its configuration

- `SIGINT` (2): Interrupt. Sent by `Ctrl-C`. It's a request to terminate the process

- `SIGKILL` (9): Kill. This is an immediate, forceful termination. The process cannot catch, ignore, or block this signal

- `SIGSEGV` (11): Segmentation Fault. Indicates the process made an invalid memory reference

- `SIGTERM` (15): Termination. This is the standard, polite way to ask a process to terminate

- `SIGSTOP`: Stop. Pauses the process. Like SIGKILL, it cannot be caught or ignored


# Niceness

## How the CPU Manages Processes

- When you run multiple applications on your computer, it seems like they are all running simultaneously

- In reality, the CPU is rapidly switching between them, giving each process a small amount of processing time, called **time slice**

- By default, the Linux kernel schedules processes in a round-robin fashion, ensuring every process gets a fair share of CPU time until it completes

- The kernel's scheduler is highly efficient at managing these rapid switches

## What is Niceness in Linux

- `niceness` is used to influence the kernel's scheduling decisions (for time slice to processes)

- The niceness of a process is represented by a number ranging from `-20` (highest priority) to `19` (lowest priority)

## Adjusting Process Priority

```bash
# view the current niceness level of running processes, look for the NI column
top
## control the niceness value
nice -n 5 apt upgrade
renice 10 -p 3245
```


# Job Control

## What it is

- Manage tasks, which take a long time to complete

- This powerful feature allows you to run and manage multiple background processes

## Commands

```bash
# to start a process directly in the background, simply append an ampersand (&) to your command
sleep 1000 &
sleep 1001 &
sleep 1002 &

# listing Background Jobs
# the + symbol indicates the most recently started background job
# the - symbol marks the second most recent one
$ jobs
[1]    Running     sleep 1000 &
[2]-   Running     sleep 1001 &
[3]+   Running     sleep 1002 &

# send active job to the background: ctrl+Z to suspend the process, then bg
pete@icebox ~ $ sleep 1003
^Z
[4]+    Stopped     sleep 1003
pete@icebox ~ $ bg
[4]+    sleep 1003 &

# bringing a Job to the Foreground
fg %1

# terminating Background Jobs
kill %1
```


# Tracking Process: `top`

```bash
top - 18:06:26 up 6 days,  4:07,  2 users,  load average: 0.92, 0.62, 0.59
Tasks: 389 total,   1 running, 387 sleeping,   0 stopped,   1 zombie
%Cpu(s):  1.8 us,  0.4 sy,  0.0 ni, 97.6 id,  0.1 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem:  32870888 total, 27467976 used,  5402912 free,   518808 buffers
KiB Swap: 33480700 total,    39892 used, 33440808 free. 19454152 cached Mem

  PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
 6675 patty    20   0 1731472 520960  30876 S   8.3  1.6 160:24.79 chrome
 6926 patty    20   0  935888 163456  25576 S   4.3  0.5   5:28.13 chrome
```

## CPU Usage Breakdown

  - `us`: Percentage of CPU time spent running user processes that are not niced (NI = 0)

    - It represents the percentage of total CPU capacity spent running **non-kernel code**
    
    - When you write a program or run a service, the CPU executes instructions in different processor execution modes

    - Standard applications run in **Ring 3 (User Mode)**, which restricts them from touching memory or hardware directly

    - What fits under `us`

        - Application Logic: Web servers (Nginx, Apache), databases (PostgreSQL, MySQL), runtimes (Node.js, Python, Java), and containerized apps (Docker/Podman containers)

        - User Utilities: Shell commands, text processing (`grep`, `awk`, `tar`), or compilation tasks (`gcc`, `make`)

        - Desktop Applications: Web browsers, text editors, or desktop environments on GUI systems

  - `sy`: Percentage of CPU time spent running the kernel and its processes

    - The instant your app needs to write a file to disk, send a packet across the network, or ask for more RAM, it issues a System Call (`syscall`)
    
    - The CPU switches into **Ring 0 (Kernel Mode)** to fulfill the request, and time spent executing that kernel code counts toward sy, not us

    - What fits under `sy`

        - Heavy File System and Disk I/O: `tar` extracting a massive archive, compiling code with hundreds of files

        - Network Packet Processing: high-volume web servers handling thousands of concurrent HTTP connections

  - `ni`: Percentage of CPU time spent running niced (low priority) user processes

  - `id`: Percentage of CPU time that is idle

  - `wa`: Percentage of CPU time spent waiting for I/O operations to complete. A high value might indicate a disk or network bottleneck

  - `hi`: Percentage of CPU time spent servicing hardware interrupts

  - `si`: Percentage of CPU time spent servicing software interrupts

  - `st`: Steal time. In virtualized environments, this is the percentage of CPU time a virtual CPU waits for a real CPU, while the hypervisor is servicing another virtual processor

## RAM

- Physical RAM: `KiB Mem`

    - Real Available RAM: `Free + Buffers + Cached`

    - Notice that used shows 27,467,976 KiB (~26.8 GB used out of 32 GB)
    
    - This does not mean your server is running out of RAM

    - Linux follows the philosophy that "free RAM is wasted RAM."

    - The kernel automatically converts unused physical memory into buffers and cached file memory

    - Buffer and Cached Mem are available if an app (like a database or web server) suddenly needs that RAM

- `KiB Swap`

    - Swap is a dedicated space on a **storage drive** that acts as an **emergency** of physical RAM

    - When physical RAM fills up, it can be used to free up physical RAM
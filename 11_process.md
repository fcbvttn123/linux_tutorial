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


# Controlling Terminal

## What is a TTY

- TTY is an abbreviation for "Teletype," which historically was a physical device for interacting with a computer

- In modern Linux systems, a TTY refers to the terminal that provides the standard input and output for a process

- There are two main types of terminals you will encounter: **terminal** devices and **pseudo-terminal** devices

## Terminal Devices vs Pseudo-Terminals

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

## The Role of the Controlling Terminal

- Most processes are bound to a controlling terminal

- This means the process's lifecycle is tied to the terminal session that started it

- For example, if you run a program like `find` in your terminal window and then close that window, the `find` process will also be terminated

## Processes Without a Controlling Terminal

- Some processes, known as `daemons`, are designed to run in the background and manage system services

- These processes often start when the system boots and stop only when it shuts down

- In the output of `ps`, it has `?` in the `TTY` column


# Process Creation

## The `fork` and `exec` Model

- The primary mechanism for process creation involves an existing process cloning itself using the `fork` system call

- The `fork` call creates a nearly identical child process

- This new child process receives its own unique PID

- The original process becomes its parent, identified by a Parent Process ID (PPID)

- After forking, the child process can either continue running the same program as its parent or

- More commonly, use the `execve` system call to load and run a new program

- We can observe this parent-child relationship in action using the ps command: `ps l`

## The `init` process

- If every process is a child of another, there must be an original ancestor (the `init` process)

- When the system boots, the kernel creates `init` as the very first user-space process, assigning it a `PID` of `1`

- The `init` process is the ultimate parent of all other processes and runs with `root` privileges to manage the system

- It cannot be terminated until the system shuts down and is responsible for spawning many of the services that keep the system running


# Process Termination

## Termination Steps

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

- `zombie process`

    - A dead process that has completed its execution but still has an entry in the process table (the parent has not yet called `wait`)
    
    - It is waiting for its parent process to read its `exit` status


# Signals

## The Purpose of Signals

- A `signal` is a software interrupt sent to a process to notify it that an important event has occurred

- Each signal is defined by an integer, but they are almost always referred to by their symbolic names, which start with `SIG`

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


# Kill (Terminate)

## Default Termination with `kill sigterm`

- `kill 12445`

- `kill -15 12445`

## Forcing Termination with SIGKILL

- `kill -9 12445`

## Checking Process Existence with `kill -0`

- Checks if a process with the specified PID exists and if you have permission to signal it

- `kill -0 12445`

- If the command executes successfully (exit code 0), the process exists

- If it fails, the process does not exist or you lack permissions


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
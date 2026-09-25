# Table of contents

- [Table of contents](#table-of-contents)
- [The Shell (`Bash`, `Zsh`, and `Fish`)](#the-shell-bash-zsh-and-fish)
- [Shell Prompt (`$` and `#`)](#shell-prompt--and-)
- [Bash Shell `/bin/bash`](#bash-shell-binbash)


# The Shell (`Bash`, `Zsh`, and `Fish`)

- A **shell is a program** that accepts commands you type, asks the operating system to run them, and then prints the result back to your terminal

- The **terminal** is the window or app you type into. The **shell** is the program running inside it

- After you SSH into the server, it starts whatever login shell is configured for your account

    ```bash
    $ cat /etc/passwd
    alice:x:1000:1000:Alice:/home/alice:/bin/bash
    ```


# Shell Prompt (`$` and `#`)

- When you open a terminal, you will be greeted by the shell prompt `username@hostname:~/directory$`

- The `$` symbol indicates that the shell is ready to accept your input as a normal user

- The `#` symbol indicates that you are working as the root user


# Bash Shell `/bin/bash`

- `bash` is a file without any extension, it reads commands and interpret them (command interpreter)

- After you SSH into the server, it starts whatever login shell is configured for your account

    ```bash
    $ cat /etc/passwd
    alice:x:1000:1000:Alice:/home/alice:/bin/bash
    ```

- Bash Builtin Commands (e.g, `echo`)

    - When you type `echo`, Bash doesn't find and launch a separate `/bin/echo` program

    - Bash can execute its built-in `echo` directly

- External Executable Program (e.g, `ls`)

    - Bash typically searches directories listed in the `$PATH` to find the executable, such as `/bin/ls` or `/usr/bin/ls`

    - Bash finds `/bin/ls` and starts the `ls` program
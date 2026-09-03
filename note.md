- `/etc`

    - This folder is the central place for system-wide configuration files

    - It holds text files that control how your computer, network, and programs run

    - Key Facts

        - `Plain text`: Most files inside are simple text files you can open and read
        
        - `System-wide`: Changes you make here affect every user on the computer
        
        - `No programs`: It rarely stores runnable programs or apps; it only stores settings
        
        - `Admin only`: You need root (administrator) rights to change files in this folder

    - Common Examples Found Inside

        - `/etc/passwd`: A list of user accounts on the system
        
        - `/etc/hosts`: A local map of names to IP addresses
        
        - `/etc/resolv.conf`: Settings for your network's DNS servers
        
        - `/etc/fstab`: Information about disk drives and how to mount them.

- `-rwsr-x---   1 bandit21 bandit20`

    - The `x` permission acts as the door key (lets normal user `bandit20` starts the binary), without `x`, `bandit20` cannot run the file (`./suconnect`)

    - The `s` permission acts as the identity badge inside (elevates the running process to the owner's privileges - `bandit21`), without `s`, you run the file as `bandit20`, NOT `bandit21`

- Null Glob (`shopt -s nullglob`)

    - `shopt -s nullglob` is a Bash setting that changes how wildcard patterns (globs) behave when they don't match any files

    - Default Bash Behavior (Without nullglob)
    
        - Normally, if a pattern like `*.sh` fails to match any existing files, Bash does not return an empty list. 
    
        - Instead, it passes the raw wildcard string itself as the argument

        - Example

            ```bash
            # If there are NO .txt files in the directory:
            for file in *.txt; do
                echo "$file"
            done

            # Output:
            # *.txt   <-- The literal string is printed because no files matched!
            ``

        - This default behavior causes scripts to break because the loop runs once using the literal string "*.txt" as if it were an actual filename

    - Behavior With `shopt -s nullglob`

        - Enabling nullglob tells Bash: "If a wildcard pattern matches nothing, expand it to nothing (an empty list)."

        - `shopt`: Short for shell options. A built-in Bash command used to set or unset optional shell behaviors

        - `-s`: Stands for set. It enables (turns on) the specified option

        - `nullglob`: The specific shell option name that converts unmatched globs into a null (empty) string instead of keeping the literal pattern

- Curly Braces for variables in Bash

    - `$owner_backup`

    - `${owner}_backup`

- For Loop Syntax

    - `for i in {0000..9999}; do`

    - `for file in *.txt; do`

- `/etc/passwd`

    - Despite the file name, it doesn't contain user actual password. Password hashes are usually stored in `/etc/shadow`

    - Line: `username:password:UID:GID:comment:home:shell`
    
    - Example: `alice:x:1000:1000:Alice:/home/alice:/bin/bash`

        - `x` -> password information is stored elsewhere, typically `/etc/shadow`

- `/bin/bash`

    - `bash` is a file without any extension, it reads commands and interpret them (command interpreter)

    - Bash Builtin Commands (e.g, `echo`)

        - When you type `echo`, Bash doesn't find and launch a separate `/bin/echo` program

        - Bash can execute its built-in `echo` directly

    - External Executable Program (e.g, `ls`)

        - Bash typically searches directories listed in the `$PATH` to find the executable, such as `/bin/ls` or `/usr/bin/ls`

        - Bash finds `/bin/ls` and starts the `ls` program

    - After you SSH into the server, it starts whatever login shell is configured for your account

- Script

    ```bash
    #!/bin/sh
    export TERM=linux
    exec more ~/text.txt
    exit 0
    ```

    - `#!/bin/sh` (shebang)

        - It tells Linux use `/bin/sh` to interpret this file

        - When you execute the file `./myscript`, Linux sees the `#!` and effectively starts `/bin/sh ./myscript` -> `/bin/sh` is the interpreter for this script

        - Why not `#!/bin/bash`?

            - `#!/bin/sh` requests the system's `sh` shell

            - Historically, Unix had a shell called the Bourne shell, commonly invoked as `sh`

            - The author wants the script to be portable

    - `exec`

        - Normally, if a shell runs `more ~/text.txt`, the process relationship looks roughly like `shell -> more`

        - The shell starts `more` as another process

        - But `exec more ~/text.txt` means replace the current `shell` process with the `more` program

    - The `exit 0` is effectively unreachable

        - Because `exec more ~/text.txt` replaced the shell with `more`

        - The original shell doesn't continue executing afterward -> the SSH session is closed
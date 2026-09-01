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
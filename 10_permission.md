# Decoding the Permission String

- Example:

    ```bash
    $ ls -l Desktop/
    drwxr-xr-x 2 pete penguins 4096 Dec 1 11:45
    ```

- The first character indicates the file type

    - The `d` signifies that `Desktop` is a directory
    
    - For a regular file, it is a hyphen `-`

- Users, group and other: `d | rwx | r-x | r-x`

    - `r`: Read permission

    - `w`: Write permission

    - `x`: Execute permission

    - `-`: No permission granted


# Modifying Permissions

- Using Symbolic Mode

    - Example: add the execute permission for the user on a file `chmod u+x myfile`

    - This command adds (+) the executable (x) permission for the user (u) on `myfile`

    - To remove a permission, you use the `-` operator `chmod g-w myfile`

    - Modify multiple permissions at once `chmod ug+w myfile`

- Using Numerical (Octal) Mode

    - The permissions are represented by the following values: `4 (r), 2 (w), 1 (x)`

    - To set a permission set, you add the numbers together. For example, to grant read, write, and execute permissions, you would use `4 + 2 + 1 = 7`

    - Example `chmod 755 myfile`


# Ownership

- Changes the user owner of `myfile` to the user `patty`: `sudo chown patty myfile`

- Sets the group ownership of `myfile` to the group `whales`: `sudo chgrp whales myfile`

- Changing both user and group `sudo chown patty:whales myfile`


# SUID `setuid`

- There are many cases in which normal users need **elevated access**

- The Set User ID (SUID) allows a user to run a program as the owner of the program file

- Example: what is the `passwd` command doing? 

    - It's modifying a couple of files
    
    - But most importantly, it's modifying the `/etc/shadow` file, which is owned by `root`

        ```bash
        $ ls -l /etc/shadow
        -rw-r----- 1 root shadow 1134 Dec 1 11:45 /etc/shadow
        ```

- New permission bit (`s`) SUID

    - Example

        ```bash
        $ ls -l /usr/bin/passwd
        -rwsr-xr-x 1 root root 47032 Dec 1 11:45 /usr/bin/passwd
        ```

    - The SUID (Set User ID) permission on `/usr/bin/passwd` allows normal users to execute the program with the temporary elevated privileges of its owner (root), but standard users cannot modify or edit the binary file itself

    - Modifying SUID: `sudo chmod u+s myfile` or `sudo chmod 4755 myfile`

- You cannot use `passwd` to change other people's password

    - There are 3 UIDs associated with every process

    - **Effetive User ID**: what permissions the process has while running

    - **Real User ID**: the user who runs the process

    - **Saved User ID**: allow a process to switch between the above 2 UIDs

    - What happen when you run `passwd`

        - When you run the command, the `real UID` is 500

        - But the `passwd` has `SUID` enabled, so the `effective UID` is 0 (`root`)

        - You modifies Sally's password and she has `real UID` 600, the process declined because your `real UID` is 500


# Sticky Bit

- The sticky bit is a permission setting that can be applied to a directory

- When a directory has the sticky bit set, files within that directory can only be deleted or renamed by the file's owner, the directory's owner, or the root user

- This is particularly useful for **shared directories** where multiple users need to create and manage their own files without interfering with others

- A common use case for the sticky bit in Linux is the `/tmp` directory, which is a world-writable location for temporary files (notice the `t` at the end)

    ```bash
    $ ls -ld /tmp
    drwxrwxrwt 17 root root 4096 Dec 15 11:45 /tmp
    ```
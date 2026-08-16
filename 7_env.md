# Environment Variable

- The Linux system uses environment variables to store information that the shell and other processes can access: `echo $PATH`

- List all variables: `env`


# The PATH variable

- This command returns a colon-separated **list of directories**

- When you type a command, your system searches through these directories to find the corresponding executable file

- Imagine you manually install a program in a non-standard directory like `/opt/coolapp/bin`

- If you try to run it by typing `cool` command, you might get a "command not found" error

- This happens because the directory containing your program is not listed in the `PATH` variable, so the shell doesn't know where to look for it.

- To fix this, you can modify the `PATH` variable to include the new directory

- By adding your custom directory to `PATH`, you enable the shell to find and execute your programs from anywhere in the terminal
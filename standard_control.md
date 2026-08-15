# Standard Output

- By default, `stdout` is connected to the screen

- Redirect output with the `>` operator `echo Hello World > peanuts.txt`

- Appends the output to the end of the **existing file**: `echo Hello World >> peanuts.txt`


# Standard Input

- By default, `stdin` is connected to the keyboard

- Example: `cat < peanuts.txt > banana.txt`

    - If you open your terminal, type `cat` and press `Enter` with no arguments at all

    - `cat` starts up, looks in its IN tray (stdin), and waits

    - Because stdin is connected to your keyboard, it sits there until you type something (e.g., hello) and hit `Enter`

    - `cat` takes what you typed from the keyboard and dumps it into its OUT tray (your screen)

    - The `<` symbol is a redirection operator managed by the shell. It says "Disconnect the IN tray from the keyboard, and attach it to `peanuts.txt` instead"


# Standard Error

- In Linux, `stderr` is a default output stream used by programs to send error messages

- It is completely separate from the standard output (`stdout`) stream, which is used for normal program output

- By default, both `stdout` and `stderr` send their output to your terminal screen, which is why you see the error message directly

- To control `stderr`, we need to use the operator `2>`

- Examples:

    ```bash
    # Redirect stderr to a file
    ls /fake/directory 2> peanuts.txt
    # Redirect both stdout and stderr by using &>
    ls /fake/directory /etc/passwd &> peanuts.txt
    ```


# File Descriptor

- To manage I/O streams like `stdin`, `stdout`, and `stderr`, the system uses file descriptors

- A file descriptor is a non-negative number that the kernel uses to identify an open file or stream

    - 0: stdin (standard input)

    - 1: stdout (standard output)

    - 2: stderr (standard error)


# Pipe Operator

- The `|` takes the standard `stdout` of the command on its left and uses it as the `stdin` for the command on its right

- Send long output directly to another command, like `less`, for easy viewing `ls -la /etc | less`


# Tee Command

- What if you want to see the output on your screen and save it to a file simultaneously?

- The pipe and tee command is a classic combination for logging and monitoring: `ls | tee peanuts.txt`

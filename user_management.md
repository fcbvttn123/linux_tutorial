# `sudo` and `su`

- Authorized users can execute commands with root privileges using the `sudo` (superuser do) commands

- The `su` (substitute user) commands

    - Besides the `sudo` command, you can use `su` to gain superuser privileges

    - When run without a username, `su` attempts to open a new shell session for the linux root user, prompting you for the root password

    - You can also use this command to switch to any other user on the system, provided you know their password

- The `sudoer` file

    - Access is controlled by a configuration file located at `/etc/sudoers`

    - This file lists the users and groups who are granted permission to run commands as the superuser

    - To edit this file safely, you should always use the `visudo` command


# `/etc/passwd`

- The system identifies users with a User ID (UID) and groups with a Group ID (GID)

- The mapping between usernames and UIDs is stored in the `/etc/passwd` file

- A typical line in this file: `root:x:0:0:root:/root:/bin/bash`

    - **Username**: The login name of the user (e.g., root)

    - **Password**: A placeholder for the user's encrypted password

        - A `x` indicates the encrypted password is in the `/etc/shadow` file

        - A `*` means the account is locked and cannot be used for login

        - A blank field means the user has no password

    - **User ID (UID)**: The unique numerical identifier for the user. The root user always has a UID of 0

    - **Group ID (GID)**: The numerical identifier for the user's primary group

    - **GECOS Field**: A comment field that traditionally holds extra information like the user's full name, phone number, or office location. It is comma-delimited

    - **Home Directory**: The absolute path to the user's home directory (e.g., /root)

    - **Default Shell**: The user's default command-line interpreter, which is executed upon login (e.g., /bin/bash)

- System Users and Special Accounts

    - When you inspect the `/etc/passwd` file, you'll notice many accounts that don't belong to **human users**

    - These are **system accounts** used to run specific services or processes with limited permissions, enhancing system security

    - For example, the `daemon` user is used for running background daemon processes


# `/etc/shadow`

- Storing sensitive user authentication information

- Unlike the world-readable `/etc/passwd` file, it requires superuser privileges to access

- The primary purpose is to store encrypted user passwords and password aging policies

- Output: `root:MyEPTEa$6Nonsense:15000:0:99999:7:::`

    - **Username**: The user's login name

    - **Encrypted password**: The hashed user password. An asterisk (*) or exclamation mark (!) here means the account is locked

    - **Date of last password change**: The number of days since Jan 1 - 1970, that the password was last changed. A value of 0 forces a password change at the next login

    - **Minimum password age**: The minimum number of days that must pass before the user can change their password again

    - **Maximum password age**: The maximum number of days the password is valid. After this period, the user must change it

    - **Password warning period**: The number of days before the password expires that the user will receive a warning message

    - **Password inactivity period**: The number of days after a password expires that the account is disabled

    - **Account expiration date**: An absolute date, expressed as days since January 1, 1970, when the user account will be disabled

    - **Reserved field**: This field is reserved for future use


# `/etc/group`

- Output: `root:*:0:pete`

- Group Name: The unique name of the group

- Group Password
    
    - This field is a legacy feature and is rarely used
    
    - Modern systems use tools like sudo for elevated privileges instead of group passwords
    
    - You will typically see a placeholder like an asterisk (*) or an 'x'

- Group ID (GID): A unique numerical identifier for the group. The system often uses the GID internally instead of the group name

- List of Users: A comma-separated list of usernames that are members of this group


# Add/Remove users, change password

```bash
sudo useradd bob
sudo userdel bob
sudo userdel -r bob
passwd bob
```
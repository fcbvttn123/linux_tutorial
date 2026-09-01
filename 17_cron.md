# Contents

- [Contents](#contents)
- [What is `cron`](#what-is-cron)
- [Core Commands (`crontab` utility)](#core-commands-crontab-utility)
- [Syntax](#syntax)
- [Where to define `cron jobs`](#where-to-define-cron-jobs)
- [`/etc/crontab` vs `/etc/cron.d/`](#etccrontab-vs-etccrond)
- [Shortcuts: replace 5 time fields](#shortcuts-replace-5-time-fields)

# What is `cron`

- Cron is a time-based job scheduling daemon that runs commands automatically at scheduled times—for example, every hour, every day...

- You manage these scheduled tasks (known as `cron jobs`) by editing configuration files called `crontabs` (cron tables)

# Core Commands (`crontab` utility)

- `crontab -e`: Opens your crontab file in a text editor to create or modify jobs

- `crontab -l`: Lists all active cron jobs currently configured for your user

- `crontab -r`: Removes all scheduled cron jobs for your user

# Syntax

- `[minute] [hour] [day-of-month] [month] [day-of-week] /path/to/command`

- `*` (Asterisk) – Matches every value (e.g., every minute, every day)

    ```bash
    # run a backup script every single minute
    * * * * * /usr/local/bin/backup.sh
    ```

- `,` (Comma) – Specifies a list of discrete values (e.g., 1,3,5 in the minute field runs at minutes 1, 3, and 5)

    ```bash
    # runs a script at 9:00 AM, 12:00 PM, and 5:00 PM every single day
    0 9,12,17 * * * /path/to/your/script.sh
    ```

- `-` (Hyphen) – Defines a range of continuous values (e.g., 1-5 in the hour field runs every hour from 1 AM to 5 AM)

- `/` (Slash) – Specifies step values / intervals (e.g., */15 in the minute field runs every 15 minutes)

    ```bash
    # run a status check every 15 minutes during standard work hours (9 AM - 5 PM)
    */15 9-17 * * * /usr/local/bin/status_check.sh
    ```

# Where to define `cron jobs`

- `/var/spool/cron/crontabs/`:	Per-user crontabs

- `/etc/crontab`: System-wide cron jobs

- `/etc/cron.d/`: Additional system-wide cron job files

- `/etc/cron.hourly/`: Scripts run hourly

- `/etc/cron.daily/`: Scripts run daily

- `/etc/cron.weekly/`: Scripts run weekly

- `/etc/cron.monthly/`: Scripts run monthly

# `/etc/crontab` vs `/etc/cron.d/`

- `/var/spool/cron/crontabs/<username>` files include a username: `0 2 * * * /home/alice/backup.sh`

- `/etc/cron.d/<filename>`: `0 2 * * * alice /home/alice/backup.sh`

# Shortcuts: replace 5 time fields

- `@reboot`: Runs once at system startup

- `@yearly` or `@annually`: Runs once a year (0 0 1 1 *)

- `@monthly`: Runs once a month (0 0 1 * *)

- `@weekly`: Runs once a week (0 0 * * 0)

- `@daily` or `@midnight`: Runs once a day (0 0 * * *)

- `@hourly`: Runs once an hour (0 * * * *)

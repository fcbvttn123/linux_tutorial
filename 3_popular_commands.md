```bash

# Create an empty file
$ touch newFile.txt
# Update the timestamp
$ touch newTimestamp

# Show file content description
$ file banana.jpg
banana.jpg: JPEG image data

# View file content
$ cat myfile.txt
# Concatenating Files
$ cat dogfile birdfile
# Use less for long file
$ less /home/pete/Documents/text1

# Copy multiple files
$ cp report.txt notes.txt summary.txt /home/pete/Documents/
# Copy a directory and all of its contents (-r)
$ cp -r Pumpkin/ /home/pete/Documents
# Prompts for confirmation before overwriting (-i)
$ cp -i mycoolfile /home/pete/Pictures
cp: overwrite '/home/pete/Pictures/mycoolfile'?
# Force an overwrite without prompts (-f)
$ cp -f mycoolfile /home/pete/Pictures

# Rename a file or dir
$ mv oldfile newfile
# Move multiple files to a dir
$ mv file_1 file_2 somedirectory/
# Specify the target directory first then move (-t)
$ mv -t somedirectory/ file_1 file_2
# Prompts for confirmation before overwriting (-i)
$ mv -i source_file destination_directory

# Create multiple dir
$ mkdir books paintings
# Create nested dir (-p)
$ mkdir -p books/hemingway/favorites
# Setting dir permission
$ mkdir -m 755 public

# Remove multiple files
$ rm notes.txt old-report.txt draft.md
# Prompts before deleting each file (-i)
$ rm -i important.txt
rm: remove regular file 'important.txt'?
# Delete dir with -r (-r)
$ rm -r old-project

# Search for a file named puppies.jpg within the /home directory
$ find /home -name puppies.jpg
$ find . -name "*.txt"
# Search dir
$ find /home -type d -name MyFolder
# Searching by Size and Time
$ find . -type f -size +10M
$ find . -type f -size -1k

# Output the first 10 lines
head /var/log/syslog

# Displays the last 10 lines ⇒ useful for checking the most recent entries of a file
tail /var/log/syslog
# Monitor files in real-time (-f)
tail -f /var/log/syslog

```
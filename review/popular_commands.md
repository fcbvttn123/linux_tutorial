```bash

# create an empty file
$ touch newFile.txt
# update the timestamp
$ touch newTimestamp

# show file content description
$ file banana.jpg
banana.jpg: JPEG image data

# view file content
$ cat myfile.txt
# use less for long file
$ less /home/pete/Documents/text1

# copy multiple files
$ cp report.txt notes.txt summary.txt /home/pete/Documents/
# copy a directory and all of its contents (-r)
$ cp -r Pumpkin/ /home/pete/Documents
# prompts for confirmation before overwriting (-i)
$ cp -i mycoolfile /home/pete/Pictures
cp: overwrite '/home/pete/Pictures/mycoolfile'?
# force an overwrite without prompts (-f)
$ cp -f mycoolfile /home/pete/Pictures

# rename a file or dir
$ mv oldfile newfile
# move multiple files to a dir
$ mv file_1 file_2 somedirectory/
# specify the target directory first then move (-t)
$ mv -t somedirectory/ file_1 file_2
# prompts for confirmation before overwriting (-i)
$ mv -i source_file destination_directory

# create multiple dir
$ mkdir books paintings
# create nested dir (-p)
$ mkdir -p books/hemingway/favorites
# setting dir permission
$ mkdir -m 755 public

# remove multiple files
$ rm notes.txt old-report.txt draft.md
# prompts before deleting each file (-i)
$ rm -i important.txt
rm: remove regular file 'important.txt'?
# delete dir with -r (-r)
$ rm -r old-project

# search for a file named puppies.jpg within the /home directory
$ find /home -name puppies.jpg
$ find . -name "*.txt"
# search dir
$ find /home -type d -name MyFolder
# searching by Size and Time
$ find . -type f -size +10M
$ find . -type f -size -1k

# output the first 10 lines
head /var/log/syslog

# displays the last 10 lines ⇒ useful for checking the most recent entries of a file
tail /var/log/syslog
# monitor files in real-time (-f)
tail -f /var/log/syslog
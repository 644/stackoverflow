# Question
New to shell scripting, just wanted to understand how to write a shell script to create a tar file whenever a particular directory gets modified.

When we say modification, its like new files got added/removed to/from that particular directory.

Tarfile should be something like `nameofDir_timestamp.tar`

```bash
find path/to/dir -mtime 10m exec tar -cvf {} ;
```

# Answer
Assuming you have bash installed, you could do this
```bash
#!/usr/bin/env bash
dirname='folder'
dirlastmod='folder-last-modified'

[[ ${dirlastmod} -nt ${dirname} ]] && exit

timestamp=$(date '+%s')

tar -cvf "${dirname}_${timestamp}.tar" "${dirname}"
touch "${dirlastmod}"
```
and add it to a cronjob.

This will check if the directory `folder` is newer than the file `folder-last-modified`, and exit if it's not. If it is, it will create a tar file of the directory `folder`, and use `touch` to update/create the timestamp on the `folder-last-modified` file.

# Original Link
https://stackoverflow.com/questions/66561714/shell-script-to-create-a-tar-file-whenever-a-particular-folder-gets-modified/66566262#66566262

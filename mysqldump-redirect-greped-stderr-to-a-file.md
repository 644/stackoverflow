# Question
`mysqldump` generates STDOUT (used to dump \*.sql), and also generates STDERR - that I need to filter + write to a file.

```bash
mysqldump --user=db_username --password=db_password --add-drop-database --host=db_host db_name \
    2>> '/srv/www/data_appsrv/logs/mysql_date_ym.log' \
    > '/mnt/backup_srv/backup/daily/file_nm.sql'
```
The code above will write `STDERR` to `mysql_date_ym.log`

I need to exclude `Warning: Using a password` string from `STDERR` that is written to `mysql_date_ym.log`

I tried variations with `grep`, `2>>` and `>`, but none works.

# Answer
This should work
```bash
command 1>stdout.txt 2> >(grep -v "Thing to remove from stderr" >stderr.txt)
```
Which will redirect STDOUT to stdout.txt, and STDERR via process substitution into the grep filter, then finally to stderr.txt.

So your full command would look like this
```bash
mysqldump --user=db_username --password=db_password --add-drop-database --host=db_host db_name \
    1>'/mnt/backup_srv/backup/daily/file_nm.sql' \
    2> >(grep -v "Warning: Using a password" > '/srv/www/data_appsrv/logs/mysql_date_ym.log')
```
------
You could also use a sed script to run on the mysql_date_ym.log file after redirecting STDOUT/STDERR normally to the output files.
```bash
sed -i 's/Warning: Using a password//g' mysql_date_ym.log
```

# Original Link
https://stackoverflow.com/questions/66530205/mysqldump-redirect-greped-stderr-to-a-file/66530844#66530844

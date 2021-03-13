# Question
```bash
#!/bin/sh

rows=$(sqlite3 db 'select * from subscriptions;')

for row in $rows; do
  // curl $row['email']
done
```

I have a db that has email and keyword i want to make some curl requests for each row in the db.

# Answer

If the output is delimited by newlines, you could do
```bash
while IFS= read -r row; do
    echo "${row}"
done < <(sqlite3 db 'select * from subscriptions;')
```
Or you could read the rows into an array first, and iterate over it
```bash
mapfile -t rows < <(sqlite3 db 'select * from subscriptions;')
for row in "${rows}"; do
    echo "${row}";
done
```

# Original Link
https://stackoverflow.com/questions/66608258/how-do-i-loop-over-rows-in-an-sqlite-db-from-bash/66608280#66608280

# Question
I have a file where my newdir and finish directories are saved:

    newdir 26.01.2020_17:28 Directory1
    finish 26.01.2020_17:28 Directory1
    newdir 26.01.2020_17:29 Directory2
    finish 26.01.2020_17:29 Directory2
    newdir 26.01.2020_17:28 Directory3
    finish 26.01.2020_17:28 Directory4
    newdir 26.01.2020_17:29 Directory5
    finish 26.01.2020_17:29 Directory3
    newdir 26.01.2020_17:28 Directory6
    finish 26.01.2020_17:28 Directory6
    newdir 26.01.2020_17:29 Directory7
    finish 26.01.2020_17:29 Directory7
    newdir 26.01.2020_17:28 Directory8
    finish 26.01.2020_17:28 Directory9
    newdir 26.01.2020_17:29 Directory10
    finish 26.01.2020_17:29 Directory11

i want to remove all where the directory name and newdir and finish match.
or put differently: Is directory name negotiate with newdir and finish, then remove it in file

example: Directory2 has newdir and finish, then remove both it in file

    newdir 26.01.2020_17:29 Directory2
    finish 26.01.2020_17:29 Directory2

unfortunately I have no approach to the solution ..
can someone help me?

Thanks.

Regards

edit:
expected result in the file:

    finish 26.01.2020_17:28 Directory4
    newdir 26.01.2020_17:29 Directory5
    newdir 26.01.2020_17:28 Directory8
    finish 26.01.2020_17:28 Directory9
    newdir 26.01.2020_17:29 Directory10
    finish 26.01.2020_17:29 Directory11

# Answer
I would do this, but awk might also be a good way.
```bash
sort -k3 dirlist | uniq -u -f2
```
It will order the file dirlist by the 3rd column, and get only unique lines based on the 3rd column.

**Output**
```
newdir 26.01.2020_17:29 Directory10
finish 26.01.2020_17:29 Directory11
finish 26.01.2020_17:28 Directory4
newdir 26.01.2020_17:29 Directory5
newdir 26.01.2020_17:28 Directory8
finish 26.01.2020_17:28 Directory9
```

**Edit**

With awk (this will also preserve the order)
```awk
awk '{seen[$3]++; line[++count]=$0; key[count]=$3}
  END{
  for(i=1; i <= count; i++)
    if(seen[key[i]] == 1)
      print line[i]
  }' dirlist
```

# Original Link
https://stackoverflow.com/questions/59920701/remove-duplicate-lines-with-2-words/59920986#59920986

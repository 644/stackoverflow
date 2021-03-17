# Question
I have 2 files file1.txt
```
1
3
5
2
```
File2.txt
```
1 aaa
2 bbb
3 ccc
4 aaa
5 bbb
```
Desired output:
```
1 aaa
3 ccc
5 bbb
2 bbb
```
Command used : `cat File1.txt |grep -wf- File2.txt but the output was:`
```
1 aaa
2 bbb
3 ccc
5 bbb
```
Is it a way to return the output in the query order? Thanks in advance!!!

# Answer
**Important Edit**

On second thought, do not use grep with redirection as it's incredibly slow. Use awk to read the original patterns to get the order back.

**Use this instead**

```bash
grep -f patterns searchdata | awk 'NR==FNR { line[$1] = $0; next } $1 in line { print line[$1] }' - patterns > matched
```

----------


**Benchmark**
```bash
#!/bin/bash
paste <(shuf -i 1-10000) <(crunch 4 4 2>/dev/null | shuf -n 10000) > searchdata
shuf -i 1-10000 > patterns

printf 'Testing awk:'
time grep -f patterns searchdata | awk 'NR==FNR { line[$1] = $0; next } $1 in line { print line[$1] }' - patterns > matched
wc -l matched

cat /dev/null > matched

printf '\nTesting grep with redirection:'
time {
  while read -r pat; do
    grep -w "$pat" searchdata >> matched
  done < patterns
}
wc -l matched
```

**Output**
```
Testing awk:
real	0m0.022s
user	0m0.017s
sys	0m0.010s
10000 matched

Testing grep with redirection:
real	0m36.370s
user	0m28.761s
sys	0m7.909s
10000 matched
```
----------

**Original**

To preserve the query order, read the file line-by-line:

```bash
while read -r pat; do grep -w "$pat" file2.txt; done < file1.txt
```

I don't think grep has an option to support this, but this solution will be slower if you have large files to read from.

# Original Link
https://stackoverflow.com/questions/59892760/how-to-obtain-the-query-order-output-when-we-use-grep/59894845#59894845

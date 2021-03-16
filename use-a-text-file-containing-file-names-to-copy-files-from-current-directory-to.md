# Question
I have created a file (`search.txt`) containing file names of `.fasta` files I want to copy from the current directory (which also contains many unwanted `.fasta` files). Is it possible to use this text file to find and copy the matching files in the current directory to a new location?

The search.txt file contains a list of names like this:

```
name_1
name_2
name_3
```

I tried to build the search term using find and grep, like this:

```bash
find . *.fasta | grep -f search.txt
```

which is returning output like this for each matching file:

```
./name_1.fasta
./name_2.fasta
./name_3.fasta
name_1.fasta
name_2.fasta
name_3.fasta
```

It's finding the correct files, but I'm not sure if this output is useful / can be used to copy these files?

# Answer
To get only matching filenames from search.txt I would do this:

```bash
find . -type f -name '*.fasta' -print0 | grep -zf search.txt | xargs -r0 cp -t target-dir/
```

It will find all files with the extension .fasta, display only the ones with matching patterns in search.txt, and bulk cp them to target-dir, and each filename is terminated with a nullbyte in case filenames contain newlines.

# Original Link
https://stackoverflow.com/questions/59883854/use-a-text-file-containing-file-names-to-copy-files-from-current-directory-to/59884679#59884679

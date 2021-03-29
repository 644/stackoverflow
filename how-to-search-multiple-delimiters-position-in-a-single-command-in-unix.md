# Question
I have a command "`awk -F'[~|^]' 'BEGIN{RS=FS}/*searching string*/{print NR-1}' <<< *user defined string*`", which will provide me the delimiters count based on the given search string. 
However the user defined string is not having any pre-defined delimiters. I got this command from this link of stackoverflow "https://stackoverflow.com/questions/12204192/using-multiple-delimiters-in-awk"

But the above code is working only if the delimiters are in single character. When I am using "^|" as a delimiter it is not considering 2 characters as a single delimiter.

Please find a sample as per my above statement,

<br>Input : a=name^|phone^|contact^|title^|address^|mobilenumber (delimiter may vary)</br>
<br>Searching String : title</br>
<br>Command : `awk -F'[~|^]' 'BEGIN{RS=FS}/title/{print NR-1}' <<< $a` </br>
<br>Output: 3 </br>

So could anyone please help me how i will fit all the delimiters (like - "~", "|", "^", "^|") in the above mentioned command.

# Answer
You need to define each possible field separator individually. The `|` means `OR`, so it will not combine them. Instead you can escape the `|` and `^` characters with `\\\\` (double backslash), then separate characters (fields) with `|`

```awk
a='f1|f2^|f3~f4^f5'
awk -F'\\^\\||~|\\^|\\|' '/f1/{print NF-1}' <<< "${a}"
# outputs 4
```

So this will use `^|`, `~`, `^`, and `|` as field separators, match the string `f1`, and print the number of `fields-1`.

# Original Link
https://stackoverflow.com/questions/61750605/how-to-search-multiple-delimiters-position-in-a-single-command-in-unix/61757992#61757992

# Question
[pandoc](www.pandoc.org) prints the available extensions for each format with a leading `+` or `-` (depending on whether the extension is enabled or disabled). So for example

```bash
$ pandoc --list-extensions=markdown
-abbreviations
+all_symbols_escapable
-angle_brackets_escapable
+auto_identifiers
-autolink_bare_uris
+backtick_code_blocks
...
```

In order to diff two format's available extensions, I simply do (bash)

```bash
$ diff -y <(pandoc --list-extensions=markdown | tr -d '+-') <(pandoc --list-extensions=commonmark | tr -d '+-')
abbreviations                                                 <
all_symbols_escapable                                           all_symbols_escapable
angle_brackets_escapable                                      | ascii_identifiers
auto_identifiers                                                auto_identifiers
autolink_bare_uris                                              autolink_bare_uris
backtick_code_blocks                                            backtick_code_blocks
blank_before_blockquote                                       <
blank_before_header                                           <
```

In the output I'd like to keep the leading `+` or `-`, that is for example

```bash
+abbreviations                                                 <
+all_symbols_escapable                                           -all_symbols_escapable
-angle_brackets_escapable                                      | -ascii_identifiers
+auto_identifiers                                                -auto_identifiers
-autolink_bare_uris                                              -autolink_bare_uris
+backtick_code_blocks                                            -backtick_code_blocks
+blank_before_blockquote                                       <
+blank_before_header                                           <
```

Please notice that this is different from running

```bash
$ diff -y <(pandoc --list-extensions=markdown) <(pandoc --list-extensions=commonmark)
-abbreviations                                                | -all_symbols_escapable
+all_symbols_escapable                                        | -ascii_identifiers
-angle_brackets_escapable                                     | -auto_identifiers
+auto_identifiers                                             <
-autolink_bare_uris                                             -autolink_bare_uris
+backtick_code_blocks                                         | -backtick_code_blocks
+blank_before_blockquote                                      <
+blank_before_header                                          <
```

What's the simplest way of doing it?

# Answer
Not really a nice solution, but you don't have an answer yet and this was the best I could think of to keep the output the same as you intended
```bash
#!/bin/bash
mapfile -t markdown < <(pandoc --list-extensions=markdown)
commonmark="$(pandoc --list-extensions=commonmark)"
out=(); i=0
while read -r line; do
  change="$(echo "${line}" | grep -Eo '<|\|')" || change=' ' && line="$(echo "${line}" | awk '{print $NF}')"
  com="$(echo "${commonmark}" | grep "${line}")"
  out+=("${markdown[i]},${change} ${com}"); ((i++))
done < <(diff -y <(printf '%s\n' "${markdown[@]}" | tr -d '+-') <(echo "${commonmark}" | tr -d '+-'))
printf '%s\n' "${out[@]}" | column -t -s ','
```

outputs
```
-abbreviations                      < 
+all_symbols_escapable                -all_symbols_escapable
-angle_brackets_escapable           | -ascii_identifiers
+auto_identifiers                     -auto_identifiers
-gfm_auto_identifiers               
-autolink_bare_uris                   -autolink_bare_uris
+backtick_code_blocks                 -backtick_code_blocks
+blank_before_blockquote            < 
+blank_before_header                < 
+bracketed_spans                    < 
+citations                          < 
-compact_definition_lists           < 
+definition_lists                   < 
-east_asian_line_breaks               -east_asian_line_breaks
-emoji                                -emoji
...
```

# Original Link
https://stackoverflow.com/questions/59882188/command-line-comparison-e-g-diff-ignoring-non-whitespace-first-character-of-e/59887013#59887013

# Question
I have a script that outputs a few lines and I want to have it open in an editor on my system (nano, vim, whatever) to be ready to edit. However I don't want to save it to a temporary file or variable but directly in the editor (I think I need to bring the out somehow into the editor's buffer).

I naively have tried something like this (I was thinking I could pipe the output into nano somehow):

```
echo "test" | nano # throws Too many errors from stdin
```

Alternatively I thought of opening the output like it's a file:

```
nano $(echo "test")
```

It opens only the editor but it's empty. Is this even possible or is a temp file strictly necessary?

# Answer
You can do

```
echo 'test' | nano -
```

The `-` option tells nano to read from stdin rather than a file. This is an editor specific solution though. A more robust solution would require temporary files, which you can use `mktemp` for.

# Original Link
https://stackoverflow.com/questions/66554015/redirect-bash-script-output-in-editor-without-saving-file/66554056#66554056

# Question
Been tinkering tonight but am unable to find a solution.

I've written a small script that I call with a "popup-shell" and that uses `fzf` to select a file. I pass this file to `marktext` appimage and disown the process, after which the "popup-shell" closes. Unfortunately, upon shell closure, the `marktext` app is also closed.

I think the problem lies in that the executable is properly disowned, but the appimage mount-stuff is still subprocessing to the "popup-shell". Once the "popup-shell" close, so does the appimage mount-stuff, which in turn will cause `marktext` to close.

process-tree-ish:

```
popup-shell
  +- script that spawns marktext appimage
  |  +- /tmp/.mount_markXXXXX/marktext ...
  |  |  +- /tmp/.mount_markXXXXX/marktext ...
  |  |     +- /tmp/.mount_markXXXXX/marktext ...
  |  |        +- /tmp/.mount_markXXXXX/marktext ...
  |  +- /tmp/.mount_markXXXXX/marktext ...
  |  |  +- /tmp/.mount_markXXXXX/marktext ...
  |  +- /tmp/.mount_markXXXXX/marktext ...
  |  +- /tmp/.mount_markXXXXX/marktext ...
marktext ...
```

The actual `marktext` binary seems to have been properly disowned.

The script isn't complicated and just run:

```bash
marktext <file> &
disown -h %1
```

Any pointers would be much appreciated.

# Answer


You can use

```bash
setsid -f marktext <file>
```

To run a program in a new session.

The `-f` option will tell `setsid` to immediately fork into the background. A quick test reveals that `disown` will still keep the parent process, whereas `setsid` won't

```bash
sleep 100 &
disown -h %1
ps -C sleep -oppid,pid
```

Outputs

```
PPID     PID
32772   32806
```

And

```bash
setsid -f sleep 100
ps -C sleep -oppid,pid
```

Outputs

```
PPID     PID
1       32814
```

[Some further reading](https://unix.stackexchange.com/questions/3886/difference-between-nohup-disown-and)

# Original Link
https://stackoverflow.com/questions/66573324/unable-to-properly-disown-appimage/66573335#66573335

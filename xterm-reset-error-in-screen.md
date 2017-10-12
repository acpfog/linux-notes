
## How to deal with an error: 'screen.xterm-256color': unknown terminal type

If you use GNU Screen and faced with an error "unknown terminal type" when initializing a terminal by the reset command or clearing the screen using Ctrl+l.

```console
user@host:~> reset
reset: unknown terminal type screen.xterm-256color
Terminal type?
```

Check the value of TERM variable. Probably you have an incorrect terminal type.

```console
user@host:~> printenv | grep TERM
TERM=screen.xterm-256color
```

In this case to avoid the incorrect terminal type rerun GNU screen with -T xterm-256color

```console
user@host:~> screen -T xterm-256color
```

Check again the value of TERM variable and try reset your terminal

```console
user@host:~> printenv | grep TERM
TERM=xterm-256color
user@host:~> reset
```

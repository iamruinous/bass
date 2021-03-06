# Bass

Bass makes it easy to use utilities written for Bash in
[fish shell](https://github.com/fish-shell/fish-shell/).

Regular bash scripts can be used in fish shell just as scripts written in any
language with proper shebang or explicitly using the interpreter (i.e. using
`bash script.sh`). However, many utilities, such as virtualenv, modifies the
shell environment and requires to be sourced, and therefore cannot be used
in fish. Sometimes, counterparts (such as the excellent
[virtualfish](http://virtualfish.readthedocs.org/en/latest/)) are
created, but that's often not the case.

Bass is created to make it possible to use bash uilities in fish shell without
any modification. It works by capturing what environment variables are modified
by the utility of interest, and replay the changes in fish.

# Installation

Use the Makefile.

`make install` will copy two files to `~/.config/fish/functions/`.

`make uninstall` will remove those two files.

Relaunch the shell for the change to take effect.

# Example

Bass is simple to use. Just prefix your bash utility command with `bass`:

```
> bass export X=3
> echo $X
3
```

Notice that `export X=3` is bash syntax. Bass "transported" the new bash
environment variable back to fish.

Bass has a debug option so you can see what happened:

```
> bass -d export X=4
# updating X=3 -> 4
set -g -x X 4
```

Here is a more realistic example, using the excellent
[nvm](https://github.com/creationix/nvm):

```
> bass source ~/.nvm/nvm.sh ';' nvm use iojs
Now using io.js v1.1.0
```

Note that semicolon is quoted to avoid being consumed by fish.

This example takes advantage of the nvm bash utility to switch to iojs. This is
a feature [not supported](https://github.com/Alex7Kom/nvm-fish/issues/20) by
its fish counterpart, [nvm-fish](https://github.com/Alex7Kom/nvm-fish).
After the command, iojs is accessible:

```
> which iojs
/Users/edc/.nvm/versions/io.js/v1.1.0/bin/iojs
```

You can then very easily pack the command as a function and feel more at home:

```
> funced mynvm
mynvm> function mynvm
           bass source ~/.nvm/nvm.sh ';' nvm $argv
       end

> mynvm list
->  iojs-v1.1.0
         system
> mynvm ls-remote
        v0.1.14
        v0.1.15
...
```

# Caveats

At the moment, Bass does not work with interactive utilities. This is not hard
to fix, but I cannot think of a use case. File a ticket if this is something
you find missing.

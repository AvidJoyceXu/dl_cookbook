# Play with Module

- https://www.cnblogs.com/yeungchie/p/16268954.html
# Modulefiles
- in `tcl` file

```tcl
#%Module1.0#####################################################################

##

## ICON dependencies env

set ICON_ROOT /home/yxy/icon-model

setenv ICON_ROOT /home/yxy/icon-model

prepend-path PATH $ICON_ROOT/deps/bin

prepend-path LD_LIBRARY_PATH $ICON_ROOT/deps/lib:$ICON_ROOT/deps/lib64

prepend-path --delim " " CPPFLAGS " -I$ICON_ROOT/deps/include"

prepend-path --delim " " CFLAGS   " -I$ICON_ROOT/deps/include"

prepend-path --delim " " FCFLAGS  " -I$ICON_ROOT/deps/include"

prepend-path --delim " " LDFLAGS  " -L$ICON_ROOT/deps/lib -L$ICON_ROOT/deps/lib64"

source-sh bash /home/yxy/aocl/4.2.0/aocc/amd-libs.cfg(base) [xly@sc1 icon-model]$ vim envs
```

- When you use `prepend-path`, you add a directory or path to the beginning of an existing environment variable.
- This is useful when you want to prioritize a specific directory for executable files over others. The newly added directory becomes the first one searched when you run a command.
# Profiling/debugging

### Note on software project growth and optimization

(From talk with JB)

Ideally, software projects should get smaller (in lines of code) as time goes by, because we will figure out better/more concise ways to write the same concepts, and also build a better understanding of the material.

### Valgrind

Valgrind is a suite of debugging and profiling tools.
tools include: memcheck (find memory-related errors e.g. leaks), callgrind (call graphs, expensive functions)

##### callgrind

[Tutorial](https://baptiste-wicht.com/posts/2011/09/profile-c-application-with-callgrind-kcachegrind.html)

"When you use Callgrind to profile an application, your application is transformed in an intermediate language and then ran in a virtual processor emulated by valgrind. This has a huge run-time overhead, but the precision is really good and your profiling data is complete."

1. Profile with: `valgrind --tool=callgrind ./[program]`
2. Visualize results with: `kcachegrind callgrind_filename.out`

<https://baptiste-wicht.com/posts/2011/09/profile-c-application-with-callgrind-kcachegrind.html>

### gdb

* `gdb [program]` to start program in gdb.
* `r` to start running, `q` to quit
* Moving around
  * `c` : continue until next breakpoint
  * `n` : advances by one line without stepping into functions
  * `s` : runs the next line of the program (steps into functions)
* variables
  * `info variables` : list "All global and static variable names".
  * `info locals` : list "Local variables of current stack frame" (names and values), including static variables in that function.
  * `info args` : list "Arguments of the current stack frame" (names and values)
  * `print [var]` : print variable
  * `set [var]=[value]`
* `where`,`backtrace` : see stack trace of current location (e.g. for segfault)
* `list` : show local code ?
* `break [file]:[linenumber]` : set breakpoint (or use CLion)
* [list of commands](https://www.tutorialspoint.com/gnu_debugger/gdb_commands.htm)
* [segfault](https://www.cprogramming.com/debugging/segfaults.html) = program trying to access memory that does not belong to it

### clion

Set breakpoints within IDE, then use GDB

* [Using the debugger in Clion](https://blog.jetbrains.com/clion/2015/05/debug-clion/)
* [attaching to local process](https://blog.jetbrains.com/clion/2016/01/attach-to-local-process/)

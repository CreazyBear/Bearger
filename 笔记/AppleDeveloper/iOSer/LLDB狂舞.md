# LLDB

## 一、LLVM、LLDB、GDB

1. LLVM：LLVM 核心库提供了与[编译器](https://baike.baidu.com/item/编译器)相关的支持，可以作为多种语言编译器的后台来使用。能够进行程序语言的编译期优化、链接优化、在线编译优化、[代码生成](https://baike.baidu.com/item/代码生成)。
2. UNIX及UNIX-like下的调试工具。
3. LLDB： LLDB的Xcode默认的调试器，它与LLVM编译器一起，带给我们更丰富的流程控制和数据检测的调试功能。



## 二、LLDB指令



#### 统一的格式

`<command> [<subcommand> [<subcommand>...]] <action> [-options [option-value]] [argument [argument...]]`



1. Command and subcommand
   1. *Command* and *subcommand* are names of LLDB debugger objects. Commands and subcommands are arranged in a hierarchy: a particular command object creates the context for a subcommand object which follows it, which again provides context for the next subcommand, and so on.
2. action
   1. The *action* is the operation you want to perform in the context of the combined debugger objects (the string of *command subcommand..* entities preceding it).
3. Options
   1. Options are *action modifiers*, which often take a value. 
4. argument
   1. Arguments represent a variety of different things according to the context of the command being used

#### 别名

```sh
(lldb) command alias bfl breakpoint set -f %1 -l %2
```

>  LLDB reads the file `~/.lldbinit` at startup



#### Help

```sh
Debugger commands:
  apropos           -- List debugger commands related to a word or subject.
  									-- 用于搜索关键字
  breakpoint        -- Commands for operating on breakpoints (see 'help b' for shorthand.)
  bugreport         -- Commands for creating domain-specific bug reports.
  command           -- Commands for managing custom LLDB commands.
  disassemble       -- Disassemble specified instructions in the current
                       target.  Defaults to the current function for the
                       current thread and stack frame.
  expression        -- Evaluate an expression on the current thread.  Displays
                       any returned value with LLDB's default formatting.
  frame             -- Commands for selecting and examing the current thread's
                       stack frames.
  gdb-remote        -- Connect to a process via remote GDB server.  If no host
                       is specifed, localhost is assumed.
  gui               -- Switch into the curses based GUI mode.
  help              -- Show a list of all debugger commands, or give details
                       about a specific command.
  kdp-remote        -- Connect to a process via remote KDP server.  If no UDP
                       port is specified, port 41139 is assumed.
  language          -- Commands specific to a source language.
  log               -- Commands controlling LLDB internal logging.
  memory            -- Commands for operating on memory in the current target
                       process.
  platform          -- Commands to manage and create platforms.
  plugin            -- Commands for managing LLDB plugins.
  process           -- Commands for interacting with processes on the current
                       platform.
  quit              -- Quit the LLDB debugger.
  v          -- Commands to access registers for the current thread and
                       stack frame.
  reproducer        -- Commands for manipulating reproducers. Reproducers make
                       it possible to capture full debug sessions with all its
                       dependencies. The resulting reproducer is used to replay
                       the debug session while debugging the debugger.
                       Because reproducers need the whole the debug session
                       from beginning to end, you need to launch the debugger
                       in capture or replay mode, commonly though the command
                       line driver.
                       Reproducers are unrelated record-replay debugging, as
                       you cannot interact with the debugger during replay.
  script            -- Invoke the script interpreter with provided code and
                       display any results.  Start the interactive interpreter
                       if no code is supplied.
  settings          -- Commands for managing LLDB settings.
  source            -- Commands for examining source code described by debug
                       information for the current target process.
  statistics        -- Print statistics about a debugging session
  target            -- Commands for operating on debugger targets.
  thread            -- Commands for operating on one or more threads in the
                       current process.
  type              -- Commands for operating on the type system.
  version           -- Show the LLDB debugger version.
  watchpoint        -- Commands for operating on watchpoints.

Current command abbreviations (type 'help command alias' for more info):
  add-dsym  -- Add a debug symbol file to one of the target's current modules
               by specifying a path to a debug symbols file, or using the
               options to specify a module to download symbols for.
  attach    -- Attach to process by ID or name.
  b         -- Set a breakpoint using one of several shorthand formats.
  bt        -- Show the current thread's call stack.  Any numeric argument
               displays at most that many frames.  The argument 'all' displays
               all threads.
  c         -- Continue execution of all threads in the current process.
  call      -- Evaluate an expression on the current thread.  Displays any
               returned value with LLDB's default formatting.
  continue  -- Continue execution of all threads in the current process.
  detach    -- Detach from the current target process.
  di        -- Disassemble specified instructions in the current target. 
               Defaults to the current function for the current thread and
               stack frame.
  dis       -- Disassemble specified instructions in the current target. 
               Defaults to the current function for the current thread and
               stack frame.
  display   -- Evaluate an expression at every stop (see 'help target
               stop-hook'.)
  down      -- Select a newer stack frame.  Defaults to moving one frame, a
               numeric argument can specify an arbitrary number.
  env       -- Shorthand for viewing and setting environment variables.
  exit      -- Quit the LLDB debugger.
  f         -- Select the current stack frame by index from within the current
               thread (see 'thread backtrace'.)
  file      -- Create a target using the argument as the main executable.
  finish    -- Finish executing the current stack frame and stop after
               returning.  Defaults to current thread unless specified.
  image     -- Commands for accessing information for one or more target
               modules.
  j         -- Set the program counter to a new address.
  jump      -- Set the program counter to a new address.
  kill      -- Terminate the current target process.
  l         -- List relevant source code using one of several shorthand formats.
  list      -- List relevant source code using one of several shorthand formats.
  n         -- Source level single step, stepping over calls.  Defaults to
               current thread unless specified.
  next      -- Source level single step, stepping over calls.  Defaults to
               current thread unless specified.
  nexti     -- Instruction level single step, stepping over calls.  Defaults to
               current thread unless specified.
  ni        -- Instruction level single step, stepping over calls.  Defaults to
               current thread unless specified.
  p         -- Evaluate an expression on the current thread.  Displays any
               returned value with LLDB's default formatting.
  parray    -- Evaluate an expression on the current thread.  Displays any
               returned value with LLDB's default formatting.
  po        -- Evaluate an expression on the current thread.  Displays any
               returned value with formatting controlled by the type's author.
  poarray   -- Evaluate an expression on the current thread.  Displays any
               returned value with LLDB's default formatting.
  print     -- Evaluate an expression on the current thread.  Displays any
               returned value with LLDB's default formatting.
  q         -- Quit the LLDB debugger.
  r         -- Launch the executable in the debugger.
  rbreak    -- Sets a breakpoint or set of breakpoints in the executable.
  re        -- Commands to access registers for the current thread and stack
               frame.
  repl      -- Evaluate an expression on the current thread.  Displays any
               returned value with LLDB's default formatting.
  run       -- Launch the executable in the debugger.
  s         -- Source level single step, stepping into calls.  Defaults to
               current thread unless specified.
  si        -- Instruction level single step, stepping into calls.  Defaults to
               current thread unless specified.
  sif       -- Step through the current block, stopping if you step directly
               into a function whose name matches the TargetFunctionName.
  step      -- Source level single step, stepping into calls.  Defaults to
               current thread unless specified.
  stepi     -- Instruction level single step, stepping into calls.  Defaults to
               current thread unless specified.
  t         -- Change the currently selected thread.
  tbreak    -- Set a one-shot breakpoint using one of several shorthand formats.
  undisplay -- Stop displaying expression at every stop (specified by stop-hook
               index.)
  up        -- Select an older stack frame.  Defaults to moving one frame, a
               numeric argument can specify an arbitrary number.
  v         -- Show variables for the current stack frame. Defaults to all
               arguments and local variables in scope. Names of argument,
               local, file static and file global variables can be specified.
               Children of aggregate variables can be specified such as
               'var->child.x'.  The -> and [] operators in 'frame variable' do
               not invoke operator overloads if they exist, but directly access
               the specified element.  If you want to trigger operator
               overloads use the expression command to print the variable
               instead.
               It is worth noting that except for overloaded operators, when
               printing local variables 'expr local_var' and 'frame var
               local_var' produce the same results.  However, 'frame variable'
               is more efficient, since it uses debug information and memory
               reads directly, rather than parsing and evaluating an
               expression, which may even involve JITing and running code in
               the target program.
  var       -- Show variables for the current stack frame. Defaults to all
               arguments and local variables in scope. Names of argument,
               local, file static and file global variables can be specified.
               Children of aggregate variables can be specified such as
               'var->child.x'.  The -> and [] operators in 'frame variable' do
               not invoke operator overloads if they exist, but directly access
               the specified element.  If you want to trigger operator
               overloads use the expression command to print the variable
               instead.
               It is worth noting that except for overloaded operators, when
               printing local variables 'expr local_var' and 'frame var
               local_var' produce the same results.  However, 'frame variable'
               is more efficient, since it uses debug information and memory
               reads directly, rather than parsing and evaluating an
               expression, which may even involve JITing and running code in
               the target program.
  vo        -- Show variables for the current stack frame. Defaults to all
               arguments and local variables in scope. Names of argument,
               local, file static and file global variables can be specified.
               Children of aggregate variables can be specified such as
               'var->child.x'.  The -> and [] operators in 'frame variable' do
               not invoke operator overloads if they exist, but directly access
               the specified element.  If you want to trigger operator
               overloads use the expression command to print the variable
               instead.
               It is worth noting that except for overloaded operators, when
               printing local variables 'expr local_var' and 'frame var
               local_var' produce the same results.  However, 'frame variable'
               is more efficient, since it uses debug information and memory
               reads directly, rather than parsing and evaluating an
               expression, which may even involve JITing and running code in
               the target program.
  x         -- Read from the memory of the current target process.
For more information on any command, type 'help <command-name>'.
```



```sh
(lldb) help process
     Commands for interacting with processes on the current platform.

Syntax: process <subcommand> [<subcommand-options>]

The following subcommands are supported:

      attach    -- Attach to a process.
      connect   -- Connect to a remote debug service.
      continue  -- Continue execution of all threads in the current process.
      detach    -- Detach from the current target process.
      handle    -- Manage LLDB handling of OS signals for the current target
                   process.  Defaults to showing current policy.
      interrupt -- Interrupt the current target process.
      kill      -- Terminate the current target process.
      launch    -- Launch the executable in the debugger.
      load      -- Load a shared library into the current process.
      plugin    -- Send a custom command to the current target process plug-in.
      save-core -- Save the current process as a core file using an appropriate
                   file type.
      signal    -- Send a UNIX signal to the current target process.
      status    -- Show status and stop location for the current target process.
      unload    -- Unload a shared library from the current process using the
                   index returned by a previous call to "process load".

For more help on any particular subcommand, type 'help <command> <subcommand>'.
(lldb) help process launch
     Launch the executable in the debugger.

Syntax: process launch <cmd-options> [<run-args>]

Command Options Usage:
  process launch [-s] [-A <boolean>] [-p <plugin>] [-w <directory>] [-a <arch>] [-v <none>] [-c[<filename>]] [-i <filename>] [-o <filename>] [-e <filename>] [<run-args>]
  process launch [-st] [-A <boolean>] [-p <plugin>] [-w <directory>] [-a <arch>] [-v <none>] [-c[<filename>]] [<run-args>]
  process launch [-ns] [-A <boolean>] [-p <plugin>] [-w <directory>] [-a <arch>] [-v <none>] [-c[<filename>]] [<run-args>]
  process launch [-s] [-A <boolean>] [-p <plugin>] [-w <directory>] [-a <arch>] [-v <none>] [-X <boolean>] [<run-args>]

       -A <boolean> ( --disable-aslr <boolean> )
            Set whether to disable address space layout randomization when
            launching a process.

       -X <boolean> ( --shell-expand-args <boolean> )
            Set whether to shell expand arguments to the process when
            launching.

       -a <arch> ( --arch <arch> )
            Set the architecture for the process to launch when ambiguous.

       -c[<filename>] ( --shell=[<filename>] )
            Run the process in a shell (not supported on all platforms).

       -e <filename> ( --stderr <filename> )
            Redirect stderr for the process to <filename>.

       -i <filename> ( --stdin <filename> )
            Redirect stdin for the process to <filename>.

       -n ( --no-stdio )
            Do not set up for terminal I/O to go to running process.

       -o <filename> ( --stdout <filename> )
            Redirect stdout for the process to <filename>.

       -p <plugin> ( --plugin <plugin> )
            Name of the process plugin you want to use.

       -s ( --stop-at-entry )
            Stop at the entry point of the program when launching a process.

       -t ( --tty )
            Start the process in a terminal (not supported on all platforms).

       -v <none> ( --environment <none> )
            Specify an environment variable name/value string (--environment
            NAME=VALUE). Can be specified multiple times for subsequent
            environment entries.

       -w <directory> ( --working-dir <directory> )
            Set the current working directory to <path> when running the
            inferior.
     
     This command takes options and free-form arguments.  If your arguments
     resemble option specifiers (i.e., they start with a - or --), you must use
     ' -- ' between the end of the command options and the beginning of the
     arguments.
(lldb) 
```



#### 常用

1. 打印

   `p、po`

2. 执行表达式

   `expression`

   `expression (void)[CATransaction flush]`

3. 设置断点

   `breakpoint set —-one-short true —-name -[UILabel setText:]`

4. 跳过

   `thread jump —-by 1`

   xcode支持拖动

5. 添加监视

   `watchpoint`

   使用xcode可以直接添加

   

#### Python

https://github.com/facebook/chisel 大概看了下，有的指令还是先实用的。



## 三、Eg

1. https://developer.apple.com/library/archive/documentation/IDEs/Conceptual/gdb_to_lldb_transition_guide/document/lldb-command-examples.html#//apple_ref/doc/uid/TP40012917-CH3-SW1





## 四、单飞的LLDB

目前没有使用场景，先略了









---

参考文献：

1. [Debug](./Debug.md)
2. https://lldb.llvm.org/use/map.html
3. https://developer.apple.com/library/archive/documentation/DeveloperTools/Conceptual/debugging_with_xcode/chapters/quickstart.html#//apple_ref/doc/uid/TP40015022-CH7-SW1
4. https://developer.apple.com/library/archive/documentation/IDEs/Conceptual/gdb_to_lldb_transition_guide/document/Introduction.html#//apple_ref/doc/uid/TP40012917
5. https://github.com/facebook/chisel

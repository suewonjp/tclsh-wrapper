### :coffee: Tclsh-Wrapper

**Tclsh-Wrapper** is a tiny wrapper for **tclsh** ( Shell/interpretor application for [Tcl](https://www.tcl.tk) programming language )

##### :tea: Motivation
Most shell applications (Bash, Python, etc) have command history navigation feature (mostly invoked by pressing UP key). However, `tclsh` offers no such basic functionality. It doesn't even understand UP/DOWN key press!!!

That is not a big problem since [Tcl/Tk](https://www.tcl.tk) package ships with an alternative shell application **tkcon**. It's a GUI shell, and much better. 

But still, if `tclsh` can offer command history navigation, I'll prefer it to `tkcon` since it's much more productive and convenient to terminal geeks anyway.

After a bit of research, I found [this code](http://wiki.tcl.tk/20215) which offers what I needed. I changed some code in it and added a few new convenient features and that is **Tclsh-Wrapper**.

##### :tea: What have been changed

- Removed a dependency to some 3rd party package
    - I guess the original intent was supporting cross platform compatibility, but when you use `tclsh` only in Unix like systems, that 3rd party package was a complete overkill.
- Added convenient cursor movement from word to word
- Added convenient glob search for command history
- Colorized the prompt, error messages, etc.
- Changed some (seemingly) inefficient code

### :coffee: How To Use It

Assume you copy the package to `~/lib/tcl`. (Of course, you can copy it anywhere you like)

1. Clone the package
    - `git clone https://github.com/suewonjp/tclsh-wrapper.git ~/lib/tcl/tclsh-wrapper`
1. Create a launcher script like so:

        touch tcl; chmod a+x tcl; echo "
        #!/bin/sh
        exec tclsh ~/lib/tcl/tclsh-wrapper/TclReadLine/TclReadLine.tcl
        " > tcl
1. Copy the generated `tcl` script somewhere in the system path.
1. Now execute `tcl` instead of `tclsh`
    - Notice that the `tcl` script above is intended for interactive uses only and not a complete replacement for `tclsh`.
    - You still need to use `tclsh` for executing Tcl scripts.

Alternatively, create a `~/.tclshrc` file and append the code as follows:

        lappend auto_path "~/lib/tcl/tclsh-wrapper"

        if {$::tcl_interactive} {
            package require TclReadLine
            TclReadLine::interact
        }
In this case, you can run `tclsh` directly, then it will read `~/.tclshrc` at startup and eventually load **Tclsh-Wrapper**.  

> Once **Tclsh-Wrapper** invoked, Type `help` to print out all key bindings and additional help messages.

### :coffee: Key Bindings

- CONTROL + a / HOME
    - Move the cursor to the start of the line
- CONTROL + b / LEFT
    - Move the cursor to the previous character
- CONTROL + d
    - Terminate the shell. Same as executing `exit` command.
- CONTROL + e / END
    - Move the cursor to the end of the line
- CONTROL + f / RIGHT
    - Move the cursor to the next character
- CONTROL + g
    - Clear the entire line
- CONTROL + k
    - Clear everything from the cursor to the end of the line
    - And the cleared text will be YANKED (remembered)
- CONTROL + y
    - Paste the text previously yanked
- CONTROL + n / DOWN
    - Retrieve an older command line from the command history
- CONTROL + p / UP
    - Retrieve a newer command line from the command history
- CONTROL + u
    - Clear everything from the start of the line to the cursor
    - And the cleared text will be YANKED (remembered)
- CONTROL + w
    - Delete the previous word
- CONTROL + h / Backspace
    - Delete the previous character
- TAB
    - Keyword completion for commands, variable, file path, etc.

> Depending on terminals you're using, **Tclsh-Wrapper** may not recognize ALT key press. Read the topic of **ALT key limitation** below for more detail

- ALT + 0
    - Move the cursor to the start of the line. Same as CONTROL + a
- ALT + b
    - Move to the start of the word
- ALT + d
    - Delete the next word
- ALT + e
    - Move to the end of the word
- ALT + f / ALT + w
    - Move to the start of the next word
- ALT + /
    - Clear the entire line and insert `?` at the start of the line.
    - This will quickly let you search commands with glob patterns through the command history
    - Read *History Search Mode* below for more detail
- ALT + k
    - Retrieve an older command line from the command history
    - Same as CONTROL + p or UP
- ALT + j
    - Retrieve a newer command line from the command history
    - Same as CONTROL + n or DOWN
- ALT + h
    - Move the cursor to the previous character
    - Same as CONTROL + b or LEFT
- ALT + l
    - Move the cursor to the next character
    - Same as CONTROL + f or RIGHT
- ALT + x / DELETE
    - Delete the next character
- Shift + LEFT
    - Move the cursor to the previous blank
- Shift + RIGHT
    - Move the cursor to the next blank

### :coffee: History Search Mode
You can navigate the command history one by one by typing `UP/CONTROL+p/ALT+k` (retrieving older command lines) or `DOWN/CONTROL+n/ALT+j` (retrieving newer command lines). 

But sometimes this may be so tedious. When you know some word contained in your target command line, you can quickly retrieve it by using that word.

For example, type `?foo`(question mark + [your search word]) and press `ENTER`. **Tclsh-Wrapper** will quickly retrieve the most recent command line containing `foo`. Also this will let you in a special mode called **History Search Mode**. Remember, in **History Search Mode**, `UP/CONTROL+p/ALT+k` and `DOWN/CONTROL+n/ALT+j` behave a little bit differently. When you press `UP` in the previous example, **Tclsh-Wrapper** will retrieve one step older command line containing `foo` if any.

In **History Search Mode**, navigation will be restricted to only command lines containing the search word.

The **History Search Mode** will end as soon as you execute some command. To manually end the **History Search Mode** without executing any command, type `CONTROL+g` and `ENTER`. ( Alternatively, type `ALT+/` and `ENTER` )

### :coffee: How to Customize it

Most of these features were already there at the [base code](http://wiki.tcl.tk/20215) and quite useful.

##### :tea: Configuration File
**Tclsh-Wrapper** reads a configuration file if it discovers one at startup and executes arbitrary Tcl commands in that file.

The path to the file is `~/.tcllinerc` by default, but you can change the path by assigning a new path to **TCLLINERC** environment variable.

##### :tea: Command Aliases
If you find yourself typing same command lines over and over, then aliases may help you.

        > TclReadLine::alias p puts
        > p {hello}
        hello
Aliases defined inside an interactive session will be valid only in that session. You can write aliases in your configuration file (`~/.tclshrc`) and make them persistent.

Also there is a `TclReadLine::unalias` command so you can remove them.

> You should use command aliases at the start of command line. Aliases in anywhere else won't be recognized

> Also when the aliased command has whitespaces in them, you should quote it with braces (See examples below)

**Useful Aliases:**

        ### Print a long horizontal line in magenta color
        TclReadLine::alias l {puts "\\033\\[35m===========================================\\033\\[0m"}

        ### I'm kind of lazy so I'd like to type `p` instead of `puts`
        TclReadLine::alias p puts

        ### Alias on the fly (e.g., %a iii {info globals})
        TclReadLine::alias %a {TclReadLine::alias}

        ### Unalias on the fly (e.g., %u iii)
        TclReadLine::alias %u {TclReadLine::unalias}

##### :tea: Keyword Completion
Simple keyword completion is provided. For example, `fore<TAB>` will expand to `foreach`.

By default, you can expand patterns if that matches **variables/Tcl/Tk commands/executables in the system path/files under the working directory**. By default, you can't match every Tcl/Tk command. Also you can't match subcommands.

However, you can extend the list of match candidates using `TclReadLine::keyword` API. For example, inserting the line `TclReadLine::keyword {TclReadLine::}` in the configuration file will let you type just `TclR<TAB>` for `TclReadLine::`.

For another example, inserting the following lines into the configuration file will let you just type `s*tol<TAB>` for `string tblower`.

        TclReadLine::keyword {string tolower}
        TclReadLine::keyword {string totitle}
        TclReadLine::keyword {string toupper}
        TclReadLine::keyword {string trim}
        TclReadLine::keyword {string trimleft}
        TclReadLine::keyword {string trimright}

However, `s*trim<TAB>` will print out 3 candidates (`string trim`, `string trimleft`, `string trimright`). If your target were `string trimleft`, then what you do is just appending `l` and type `<TAB>`. What if your target were `string trim`? Then, type `s*trim$<TAB>`. `$` notation at the end will match words ending with the character before that `$`.

##### :tea: History File Path
**Tclsh-Wrapper** persists the command history records to a file. (`~/tclline_history` by default)

You can change the path by assigning a new path to **TCLLINE_HISTORY** environment variable.

This may be useful when you want to maintain separate command history for `tclsh` and `wish`. In my opinion, sharing the same command history for both shells is not a good idea since `tclsh` doesn't understand Tk commands by default and when using `tclsh` after using `wish`, the command history would be polluted with a bunch of commands that `tclsh` can't even execute.

For example, you can change the launcher script (named `tcl`) introduced earlier like so:

        #!/bin/sh

        if [ "$1" = "w" ]; then
            TCLLINE_HISTORY=~/.tclline_history_w exec wish ~/test/trysomething/tclsh-wrapper/TclReadLine/TclReadLine.tcl
        else
            TCLLINE_HISTORY=~/.tclline_history exec tclsh ~/test/trysomething/tclsh-wrapper/TclReadLine/TclReadLine.tcl
        fi

You can invoke `tclsh` as before by running `tcl`, and invoke `wish` by running `tcl w`. Notice that `~/.tclline_history` will be used for `tclsh` sessions, and `~/.tclline_history_w` will be used for `wish` sessions.

### :coffee: Issues

- This package will work for most of Unix like systems (Linux, Mac OS X, Cygwin, etc), but it doesn't support Windows for now.
    - I mean `tclsh.exe`, the Windows executable, not `tclsh` on Cygwin
- You many notice that the command line cursor flickers at times when it moves fast. But it's not a problem in terms of functionalities and you can safely ignore it.
- ALT key limitation
    - **Tclsh-Wrapper** assumes your terminal handles ALT keys in a *ESC prefixed* way  
    - When you're not sure, execute `cat -v` and type `ALT+a` (Press `ALT` and `a` together)
    - When the terminal prints something like `^[a`, then you're OK.  
    - If your terminal prints something different, **Tclsh-Wrapper** may not recognize key bindings with ALT key press.  
        - Many terminals offer an option for you to switch how it handles ALT key press. Consult the help or manual of your terminal.


### :copyright: COPYRIGHT/LICENSE/DISCLAIMER

    Copyright (c) 2018 Suewon Bahng, suewonjp@gmail.com
    
    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at
    
        http://www.apache.org/licenses/LICENSE-2.0
    
    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.

### :busts_in_silhouette: CONTRIBUTORS
Suewon Bahng  

* * *
Updated by Suewon Bahng ( Jan 2018 )


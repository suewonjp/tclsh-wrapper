### :coffee: Tclsh Wrapper

This is a tiny wrapper code for **tclsh** ( Shell/interpretor application for [Tcl](https://www.tcl.tk/) programming language )

###### Motivation
Most shell applications (Bash, Python, etc) have command history navigation feature (mostly invoked by pressing UP key). However, tclsh offers no such basic functionality.  It doesn't even understand UP/DOWN key press!!!

That is not a big problem since Tcl/Tk package contains an alternative shell application **tkcon**. It's a GUI shell, and much better. 

But if tclsh can offer command history navigation, I'll prefer it to tkcon since it's much more productive and convenient to me.

After bit of research, I found [this code](http://wiki.tcl.tk/20215) which offers what I needed. I changed some code in it and added a few new convenient features and that is **Tclsh Wrapper**.

###### What have been changed

1. Removed a dpendency to 3rd party package  
  - I guess the original intent was supporting cross platform compatibility, but when you use tclsh only in Unix like systems, that 3rd party package was a complete overkill.
1. Added convenient cursor movement from word to word  
1. Added convenient glob search for command history  
1. Removed some (possibly) unnecessary code  

### :coffee: How To Use It

Assume you copy the package to `~/lib/tcl`. (Of course, you can copy it anywhere you like)

1. Clone the package
  - `git clone https://github.com/suewonjp/tclsh-wrapper.git ~/lib/tcl/tclsh-wrapper`
1. Create a launcher script like so:

        touch tcl; chmod a+x tcl; echo "
        > #!/bin/sh
        > exec tclsh ~/lib/tcl/tclsh-wrapper/TclReadLine/TclReadLine.tcl
        > " > tcl
1. Copy `tcl` somewhere in the system path.
1. Now execute `tcl` instead of execute `tclsh`

Altertatively, create a `~/.tclshrc` file and append the code as follows:

        lappend auto_path "~/lib/tcl/tclsh-wrapper"

        if {$::tcl_interactive} {
            package require TclReadLine
            TclReadLine::interact
        }
In this case, you can run `tclsh` directly, then it will read `~/.tclshrc` at startup and eventually load **Tclsh Wrapper**.

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

> Depending on terminals you're using, **Tclsh Wrapper** may not recognize ALT key press. Read the topic of **ALT key limitation** below for more detail

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
    - Clear the entire line and insert `/` (slash) at the start of the line.
    - This will quickly let you search command in the command history 
- ALT + k
    - Same as CONTROL + p or UP
- ALT + j
    - Same as CONTROL + n or DOWN
- ALT + h
    - Same as CONTROL + b or LEFT
- ALT + l
    - Same as CONTROL + f or RIGHT
- ALT + x / DELETE
    - Delete a next character
- Shift + LEFT
    - Move the cursor to the previous blank
- Shift + RIGHT
    - Move the cursor to the next blank

###### History Search Mode
You can navigate the command history one by one by typing UP/CONTROL+p/ALT+k (retrieving older command lines) or DOWN/CONTROL+n/ALT+j (retrieving newer command lines). 

But sometimes, this may be so tedious. When you know some word contained in your target command line, you can quickly retrieve it by using that word.

For example, type `/foo`(slash + [your search word]) and press ENTER. **Tclsh Wrapper** will quickly retrieve the most recent command line containing `foo`. Also this will let you in a special mode called **History Search Mode**. Remember, in history search mode, UP/CONTROL+p/ALT+k and DOWN/CONTROL+n/ALT+j behave a little bit differently. When you press UP in the previous example, **Tclsh Wrapper** will retrieve one step older command line containing `foo` if any.

In history search mode, navigation will be restricted to only command lines containg the search word.

To end the history search mode, type `CONTROL+g` and ENTER. ( Alternatively, type `ALT+/` and ENTER )

### :coffee: Issues

- This package will work for most of Unix like systems (Linux, Mac OS X, Cygwin, etc), but it doesn't support Windows for now.
    - I mean tclsh.exe, Windows executable, not tclsh on Cygwin
- You many notice that the command line cursor flickers at times when it moves fast. But it's not a problem in terms of functionalities and you can safely ignore it.
- ALT key limitation
    - **Tclsh Wrapper** assumes your terminal handles ALT keys in a *ESC prefixed* way  
    - When you're not sure, execute `cat -v` and type `ALT+a` (Press `ALT` and `a` together)
    - When the terminal prints something like `^[a`, then you're OK.  
    - If your terminal prints something different, **Tclsh Wrapper** may not recognize key bindings with ALT key press.  
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


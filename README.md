fef (for-each-file)
===================

Written by Nihat Engin Toklu < http://github.com/engintoklu >.

`fef` is a command-line tool for listing and/or
doing operations on multiple files.
fef can recursively traverse within directory trees.
It can be considered as an alternative to the
classical `find` command which exists in unix-like systems.

The usage of `fef` is as follows:

fef *OPTIONS* *DIR1* *DIR2* ... *DIRn* *EXPRESSION1* *EXPRESSION2* ... *EXPRESSIONn*

In the command line above, the arguments can be explained as follows:

* *EXPRESSION*s are to be evaluated and executed for each file
  within the specified directories.
  The syntax of an expression is
  `[ commandName commandArgument1 commandArgument2 ... ]`.
  If an expression returns logical false
  (which is represented by an empty string in fef),
  the rest of the expressions are not executed,
  and fef switches to the next file.

* *DIR*s are directory names.
  If a directory name is not given at all, the current directory (`.`)
  is accepted as the directory in which the *EXPRESSION*s will be executed.

* *OPTIONS* can be omitted, or can be given as `-r` or `-R`.
  If `-r` or `-R` is provided, `fef` recursively traverses
  the subdirectories of the specified *DIR*s.


Further documentation can be received
by giving fef the following arguments:

    fef --help                Show this help screen
    fef --license             Show the license
    fef --commands            List all commands
    fef --explain COMMAND     Explain COMMAND

Examples
========

Within the current directory,
to print all the files whose names match with `'*.txt'`,
we would use `fef` as follows:

    fef [ iname "*.txt" ] [ print ]

The double quotation marks (`"`) are to prevent the system shell
to intervene and expand `*.txt`.
Because we want `fef` to process the wildcards,
not the shell.
If you are using a POSIX-compliant shell (`sh`, `bash`, `zsh`, etc.)
you could also use single quotation marks (`'`).

The command `iname` is a query, which returns logical true
if the file matches the provided pattern.
The letter `i` at the beginning of the command `iname`
means that the lower/upper case differences will be ignored
(i.e. a file named `UPPERCASE.TXT` matches `*.txt` according to `iname`).
If you want to be strict with lower/upper case differences,
you can use the command `name` instead of `iname`.

To sum up, the example command line above means
"considering each file within the current directory,
if a file's name matches the pattern `*.txt`, print its name".

If we want to print the names of all text files and HTML files
within the current directory,
we can give `iname` multiple arguments:

    fef [ iname "*.txt" "*.htm" "*.html" ] [ print ]

If we want to print the names of all text files and HTML files
within the current directory AND within all the subdirectories
of the current directory,
we can give `fef` the argument `-r`:

    fef -r [ iname "*.txt" "*.htm" "*.html" ] [ print ]

If we want to do this printing operation not within the current
directory, but within another directory `/home/myself/mydir1`,
we use the following command line:

    fef -r /home/myself/mydir1 [ iname "*.txt" "*.htm" "*.html" ] [ print ]

Multiple directories can be specified:

    fef -r /home/myself/mydir1 /home/myself/mydir2 [ iname "*.txt" "*.htm" "*.html" ] [ print ]

Within the current directory and its subdirectories,
if we want to list all files
which have `test` in their names and which are not directories,
we use the following command line:

    fef -r [ iname "*test*" ] [ isfile ] [ print ]

If we want to list all the non-directory files
which have `test` in their names,
or in their contents,
we can use the following command line:

    fef -r [ isfile ] [ or [ iname "*test*" ] [ icontains "test" ] ] [ print ]

In the example above, we see
how we can embed an expression within another.
`or` is a command which returns logical true
if one of its arguments are true
(Note: there is also an `and` command which returns
true only if all its arguments are false;
and a `not` command which returns true if its argument is false,
and false if its argument is true).
As the arguments to `or`,
we give two expressions:
one which uses the `iname` command
and another one which uses the `icontains` command.
The `icontains` command returns true if a file
contains a text matching the specified regular expression
by ignoring the upper/lower case differences
(if upper/lower case differences should not be ignored,
you can use the command `contains` instead).

Consider the previous example again.
If we want to copy the files into `/home/myself/destination`
instead of printing their names,
we can use the following command line:

    fef -r [ isfile ] [ or [ iname "*test*" ] [ icontains "test" ] ] [ run cp [ path ] /home/myself/destination ]

In the example above,
the command `run`, as its name implies,
runs an outer program with the specified arguments.
The command `path` returns the path to the file
which is being processed by `fef`.
So, each file which matches the specified criteria
is copied into `/home/myself/destination`.
Note that this example above assumes that
you have the outer program `cp` available in a directory
which listed in your `PATH` environment variable.
This is usually the case on systems like Linux, *BSD, Mac OS X.
However, on Windows, you might not have such an outer program.
In that case, you can do the copying by using `cmd`:

    fef -r [ isfile ] [ or [ iname "*test*" ] [ icontains "test" ] ] [ run cmd /c copy [ path ] c:\home\myself\destination ]

Now, suppose that, in the current directory
and within all its subdirectories,
we want to all the text and html files into `/home/mydocs`,
and all the python scripts
(all files with their names matching `*.py`,
or all files containing the line `#!/usr/bin/env python`)
into `/home/myscripts`.
We can use the following command line:

    fef -r [ isfile ] [ if [ iname "*.txt" "*.htm" "*.html" ] [ run cp [ path ] /home/myself/mydocs ] [ or [ iname "*.py" ] [ contains "#!/usr/bin/env python" ] ] [ run cp [ path ] /home/myself/myscripts ] ]

License information
===================

Copyright (c) 2014, 2015, Nihat Engin Toklu < https://github.com/engintoklu >
All rights reserved.

Redistribution and use in source and binary forms, with or without
modification, are permitted provided that the following conditions are met:

1. Redistributions of source code must retain the above copyright notice, this
   list of conditions and the following disclaimer.
2. Redistributions in binary form must reproduce the above copyright notice,
   this list of conditions and the following disclaimer in the documentation
   and/or other materials provided with the distribution.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND
ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED
WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT OWNER OR CONTRIBUTORS BE LIABLE FOR
ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES
(INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES;
LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND
ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT
(INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS
SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

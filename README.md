# shell-utils
Utils for Shell Scripting

## Documentation and References
There is a lot of documentation about shell scripting language. For example one of the most common is **Bash**. You can find the manual in several formats (html, pdf, asci...) here:

*Bash manual* --> https://www.gnu.org/software/bash/manual/

From the console and offline yo can also get documentation:

```
man bash
info bash
```
Info has some hyperlinks. You don’t use the mouse ut the keys for navigate throng them.

## Considerations on your script file

- Start your file with first line as:
```
#!/usr/bin/env bash
```
or typically
```
#!/bin/bash
```

- Make your script executable if you need to: ```sh chmod u+x filename.sh ```. You then can execute your file as *./filename.sh* If you don’t make it executable you still can execute it as *bash filename.sh*

- Normally we use extension *.sh when naming a bash script

## Variables, the two areas
We have two areas to keep variables:
- Enviroment (exported variables) ```export variable=“value”```
We define this ones out of our script. We can do it in the terminal console before launching the script, for example:
```
export MY_ENV_VAR=100
sh my_script.sh # --> At this point inside the script we will be able to use the MY_ENV_VAR
```
We can also define this type of variables on the user profile for example in the file ".bash_profile" file in some systems.
- Process variables that live in the current process

## Typeset
Functions share variables, they don’t get a copy, so if you modify a variable in a *function {}* it is modified globally. If you need to create a variable in a function that will not ve abailable outside you can declare it with do it with **typeset**. Also is good to know that arithmetic is much faster for variables defined as integers.
```
typeset x # —> We use this inside a function to make a variable private to the function 
typeset -i x # —> Integer
```

## Declare
Check your Bash version as if you have Bash v3 some declare command may not be supported
```
declare -l lstring=“aBCDfe” # —> string, uppercase will be converted to lowercase
declare -u ustring=“aBCDfe” # —> uppercase string
declare -r readonly=“Readonly value” # —> Readonly
declare -a MyArray # —> Indexed array 
declare -A MyAssocArray # —> Associative Array (dictionary)
```

In case you need to upgrade your *Bash* :
- Linux or similar --> ```sh sudo apt-get update && sudo apt-get install --only-upgrade bash```
- MacOSX --> https://ea4hch.blogspot.com/2020/06/bash-upgrade-on-macos.html

## Atirthmetics
typeset -i x
x=1; let x++; let x=2*x; let y=x**3

## Parentesis ()
When you use parenthesis a new process is created for those command inside. This process go away you closes “()”
Example:
```
a=100
(
a=90; echo $a
)
Echo $a
```

## Curly Braces {}
We use braces to group things. We can use braces like this: 

```
abc=Hello
echo {$abc}DEF # {Hello}DEF
echo ${abc}DEF # HelloDEF
```

*Indirection*:

```
x=abc
abd=def
echo ${!x} # it will print "def"
```

*Unset or Null Variables*:

```
${variable <OPR> value}
```

Example:

```
x=${var:-Default}
:- # If var unset/null , return value; otherwise, return var value
:= # If var unset/null , var is assigned value & returned
:? # Display an error and exit script if var unset/null
:+ if var unset/null return nothing; otherwise, return value
```

*String Operations*:

```
${var:offset} # value of var starting at offset
${var:offset:len} # value of var starting at offset up to length len
${#var} # length of var
```

Prefix/Postfix are usefull for processing filenames, paths:

```
${var#pre} # remove matching prefix
${var%post} # remove sufix
```

## Bash Builtins
You can check what it is enabled in your Bash just typing *enable*
For example one very useful built-in for real time is: *time*

## Startup settings and files

**.bash_profile**
Is a file read when Bash is invoked as a login shell
We put here typically environment variables in it:

```
PATH=$PATH:/usr/local/bin
export PATH
```

You can check your profile this way:

```
cat $HOME/.bash_profile
```

**.bashrc**
Is executed when a new shell is started
We normally put here **aliases** (as they are not exported).

### Aliases samples
Aliases is how we rename things of make command shortcuts. You can get a list of all aliases by just “alias”. A typicall use could be:
```
alias ls=“ls —color”
alias ll=“ls -l”
alias lr=“ls -ltr”
alias copy=cp
alias myserver='ssh -p 22 username@192.168.1.204' # My cloud server
```

You also can unalias, for example: ```sh unalias ls ```

## Sourcing scripts
source environment.sh
This way we get the Shell executed the script in the shell’s own process instead of in a new process (as it would be when just runnig ./enviroment.sh)
 
## Echo Command

-n —> don’t print a trailing newline

-e —> enable backslash escaped chars like \n and \t

-E —> disable backslash escaped chars in case they where enabled by default

```
echo 'Warning Message!' >&2 # (output to stderr)
```

## Read command

read a b —> Read first word into a and rest into b

read a b c —> Read first word into a, second into b and the rest into c


## While Loops

```
while
    read a b
do
     echo a is $a and b is $b  
done <data_file
```

```
ls -l | while
read a b c d e f g h i
do echo owner of file $i is $c
done
```

## For Loops

The structure is:

```
for <var> in <list>
do
commands
done
```

For example:

```
for i in {1..10}
do
echo $i
done

for i in {a..z}
do
echo $i
done

for f in $(find . -name *.c)

for f in $(find . -iname *4.1*)
```

## Bash Functions

```
function myfunction {
    echo "Hello !"
    return
}
```

You can asign the result of a function to a variable:

```
myvar=$(myfunction)
```

Also you can export a function to be available for other shells

```
export -f myfunction
```

## Exit
exit <VALUE> sets the exit status of the whole shell program (not just a function)
is is represented by **$?** so after script execution you can read the exit with:
	
``` status with echo $? ```

## Redirection and Pipes

Normally processes have three files open:

0 --> stdin, 1 --> stdout, 2--> stderr

We use them like this:

```
command > stdout_file 2> stderr_file < stdin_file

command &> stdout_and_stderr_file (file is created or overwritten with stdout and stderr)
command &>> (appends stdout and stderr)

command1 | command2 (command2 stdin comes from command1 stdout)

command1 2>&1 | command2 (gets stdout and stderr from command1)
```

## Here Documents: <<
```
sort <<END
car
airplane
skate
bike
END
```

## Open and Close File Descriptors

We use lsof -p PID to see what file descriptors for a process are open,

```
# $$ is shell's PID
lsof -p $$	

# Opens file descriptor 5 for reading from file "myfile"
exec 5< myfile

# Opens file descriptor 5 for writing to file "myfile"
exec 5> myfile
```

# Case statement

```
case expression in
pattern 1 )
    commands;;
pattern 2 )
    commands;;
esac
```

Sample:

```
case $answer in
yes|YES|y|Y )
    echo "Yes";;
n*|N* )
    echo "No";;
esac
```

## If-Then-Else Statement

```
if 
    comands # last result is used
then
   commands
else
    commands
fi
```

Samples:

```
if 
    grep -q error myfile
then
   echo myfile has Errors
else
    echo myfile has not Errors
fi

if [[ -f myfile ]]; then echo "file exists"; fi

if test -f myfile; then echo "file exists"; fi

if [[ $x -gt 5 ]]; then echo "It is greater"; fi
```

**Test Operators**

```
[[ expr1 -eq expr2 ]]
[[ expr1 -ne expr2 ]]

[[ expr1 -lt expr2 ]]
[[ expr1 -gt expr2 ]]

[[ expr1 -le expr2 ]]
[[ expr1 -ge expr2 ]]

((expr1 == expr2))
((expr1 != expr2))

((expr1 < expr2))
((expr1 > expr2))

((expr1 <= expr2))
((expr1 >= expr2))

((expr1 && expr2))
((expr1 || expr2))

# Success if X is a directory
test -d X

# Success if X is a regular file
test -f X

# Success if X exists and not empty
test -s X

# Success you hace x permision on X
test -x X

# Success you hace w permision on X
test -w X

# Success you hace r permision on X
test -r X
```

## Aritmetic operations

Use in (( )) or with let

```
id++ id-- ++id --id
! ** * / % % + -
<< >> <= >= < >
== != & ^ | && ||
expr?expr:expr
= *= /= %= += -= <<= >>= &= ^= |=
expr1 , expr2
```

Samples:

```
((n=2**3 + 5))
((y=n^4))  # (xor)
```

## Filters

In Linux a program is a "filter" that reads from stdin and writes on stdout.

We use filters to combine input and output sequence of commands typically to get a report.

```
head file.txt # Prints the first lines of file or stdin
tail file.txt # Prints the last lines of file or stdin

cat myfile | head -10 # First 10 lines of myfile
cat myfile | tail -10 # Last 10 lines of myfile

cat myfile | head -10 | tail -5 # Lines 6-10 of myfile
cat myfile | head -5 | tail -3 # Lines 3-5 of myfile

wc # Word counts
wc -l # number of lines
```

## sed Command

sed is a non-interactive stream editor. Works great as a filterand it is ideal for batch editing task applied to all lines in a file. Some examples using it to substitute strings:

```
date | sed 's/Jun/Junio/; s/Thu/Jueves/'
sed 's/w/White/; s/b/Blue/'
sed -e 's/[xX]/Y/' -e 's/b.*n/blue/'
sed 's/[xX]/Y/; s/Y/Yellow/'
sed '1,5p'
```

You can put sed instructions in a file and use it as a sed script:

```
sed -f sedscript myfile.txt # sedscript file contains the substitution

sed '/alpha/s/beta/gamma/' # If the line has alpha then substitute beta for gamma

sed '/apple/,/orange/d' # find a line with apple on it then find a line with orange on it and then delete the sequence on lines from apple to orange

sed '/Error/!s/old/new/' # substitute only if line does not contain Error

cat myfile | sed -n "4,10p" # Take lines 4 to 10 from myfile
```

## The awk language

- A pattern matching language
- Works as a filter
- Good for reporting
- Processes one line at a time as sed
- Brakes a line into fields, $1, $2, etc... 
- Fields delimited by values in variable FS, normally white space
- $0 is the entire line

Samples:

```
ps -el | \
awk '/pts/||$8~/35/{printf("%5d %5d %s\n", $4, $5, $14)}' --> If line contains pts or field 8 contains 35 then we print some values
```

Here a awk script samples using END. END is a special key. Indicates that when ending procesing the lines then execute the commands:

*sample.awk*:
```
#!/usr/bin/awk -f
{szsum+=$9
rssum+=$8}
END{printf("RSS\tSZ\n%d\t%d\n",rssum,szsum)}
```

```
$ ps -l | ./sample.awk
```

*words.awk*:
```
#!/usr/bin/awk -f
{for (i=1;i<NF;i++)
    words[$i]++}
END{printf("is=%d, ls=%d, the=%d\n", words["is"], words["ls"], words["the"])}
```

```
$ man ls | col -b | awk -f words.awk
```

## Command line arguments 

- Parameters (positional): $1, $2, $3, ...
- To reference multidigit we use {}, e.g., ${10}
- $0 is the path to the program itself
	echo Usage: $0 arg1 ...
- Shift moves the positional arguments, $2 into $1, $3 into $2, etc... We use this to loop and process arguments (allways $1) until there are no more arguments

Sample: 

```
#!/usr/bin/env bash
while [[ $# -gt 0 ]]
do
	echo $1
	shift
done
```

## Coprocesses

With this technology we create a script that works as a filter and use it as a background running process we can use with no need to launch it every time.

coproc ./mycoproc.sh # This way we create a coprocess for our script

We can write to ([1]) or read ([0])  from:

echo VALUE >&"${COPROC[1]}

cat <&"${COPROC[0]}

We can also give our process a name:

coproc mycoproc {  ./mycoproc.sh;  }

echo VALUE >&"${mycoproc[1]}

cat <&"${mycoproc[0]}

## More info
Remember to check *info* and *man* to get more information and usefull documentation.

# Enjoy !

![version](https://img.shields.io/github/v/release/pforret/LearnBashQuickly)
[part of ![Bashful Scripting](https://img.shields.io/badge/bashful-scripting-orange) network](https://blog.forret.com/portfolio/bashful/)

# Learn Bash in 27 minutes
Learn Bash scripting in 27 minutes

![Bash logo](learnbash.jpg)
This is inspired by 
[Learn Go in ~5mins](https://gist.github.com/prologic/5f6afe9c1b98016ca278f4d507e65510),
[A half-hour to learn Rust](https://fasterthanli.me/articles/a-half-hour-to-learn-rust)
and [Zig in 30 minutes](https://gist.github.com/ityonemo/769532c2017ed9143f3571e5ac104e50).

## Why bash scripting?
`bash` (Bourne Again Shell) was developed in 1989, which makes it younger than C 
but probably older than anything you're used to developing in.
This does not make it old-fashioned or obsolete. 
Bash runs practically everywhere: on **Unix/Linux**, on **MacOS** and **Windows** (WSL),
it is used in all kinds of 'modern' software (Docker, deployment/build scripts, CI/CD) 
and the chances that you will have a rich developer career
without ever using `bash` are quasi zero. So why not get good at it?

## Your first script

Whenever you are typing in your Terminal/Console, you are in an interactive `bash` shell 
(or its more sophisticated cousins `zsh` or `fish`). 
Any command you can type here, like `ls`, `whoami`, `echo "hello"` qualifies as a bash command, 
and can be used in a script.

While in your terminal, enter the following command (don't copy the >, it's there to indicate the start of the command line):
```bash
> touch myscript.sh       # create the file 'myscript.sh' as an empty file
```

Go edit this new file with your favorite text editor (Sublime/Vcode/JetBrains/...) and add the following 2 lines:

```bash
#!/usr/bin/env bash
echo "Hello world!"
```

Now go back to your Terminal and type
```bash
> chmod +x myscript.sh  # make the file executable, so you can call it directly as ./myscript.sh
> ./myscript.sh         # execute your new script
Hello world!
```

## Variables

Bash variables are untyped.
The value and/or context of a variable determines if it will be interpreted as an integer, a string or an array.

```bash
width=800               # variables are assigned with '='
name="James Bond"       # strings with spaces should be delimited by " or '
user_name1=jbond        # variable names can contain letters, digits and '_', but cannot start with a digit

echo "Welcome $name!"   # variable are referenced with a $ prefix
file="${user}_${uniq}"  # ${var} can be used to clearly delimit the name of the variable
echo "${width:-400}"    # if variable $width is not set, the default 400 will be used
echo "${text/etc/&}"    # replace the 1st text 'etc' by '&' in the variable before printing it
echo "${text//etc/&}"   # replace all texts 'etc' by '&' in the variable before printing it

w=$((width + 80))       # $(()) allows arithmetic: + - / * % 

input=$1                # $1, $2 ... are the 1st, 2nd ... parameters specified on the command line
input="$1"              # put quotes around any variable that might contain " " (space), "\t" (tab), "\n" (new line) 
script=$0               # $0 refers to the actual script's name, as called (so /full/path/script or ../src/script)
temp="/tmp/$$.txt"      # $$ is the id of this process and will be different each time the script runs

echo "$SECONDS secs"    # there are preset variables that your shell sets automatically: $SECONDS, $HOME, $HOSTNAME, $PWD

LANG=C do_something     # start the subcommand do_something but first set LANG to "C" only for that subcommand

script=$(basename "$0") # execute what is between $( ) and use the output as the value
```

## Test or [[ ]]
Bash has an essential '[test](https://ss64.com/bash/test.html)' program (e.g. `test -f output.txt`) 
that is most common used as `[[ -f output.txt ]]`. 
There is also an older syntax of `[ -f output.txt ]`, but the double square brackets are preferred. 
This program tests for a certain condition and returns with 0 ('ok') if the condition was met. 
The purpose of this will become clear in the next chapter.
```bash
[[ -f file.txt ]]       # file exists
[[ ! -f file.txt ]]     # file does not exist -- ! means 'not'
[[ -f a.txt && -f b.txt ]] # both files exist -- && means AND , || means OR
[[ -d ~/.home ]]        # folder exists
[[ -x program ]]        # program exists and is executable
[[ -s file.txt ]]       # file exists and has a size > 0
[[ -n "$text" ]]        # variable $text is not empty
[[ -z "$text" ]]        # variable $text is empty
[[ "$text" == "yes" ]]  # variable $text == "yes"
[[ $width -eq 800 ]]    # $width is the number 800
[[ $width -gt 800 ]]    # $width is greater than 800
[[ file1 -nt file2 ]]   # file1 is newer (more recently modified) than file2
```

## Flow control structures

```bash
if [[ ! -f "out.txt" ]] ; then                    # if ... then ... else ... fi
  echo "OK"
else
  echo "STOP"
fi

[[ -f "out.txt" ]] && echo "OK" || echo "STOP"    # can also be written as 1 line if the 'then' part is 1 line only

while [[ ! -f output.txt ]] ; do                  # while ... do ... done
  (...)     # wait for output.txt
  continue  # return and do next iteration
done

for file in *.txt ; do                            # for ... in ... do ... done
  echo "Process file $file"
  (...)
done

for (( i = 0; i < n; i++ )); do                   # for (...) do ... done
    (...)
done

case $option in                                   # case ... in ...) ;; esac
export) do_export "$1" "$2"                       # you might know this as a 'switch' statement
  ;;
refresh) do_refresh "$2"
  ;;
*)      echo "Unknown option [$option]"
esac
```


## Functions
```bash
myfunction(){           # bash functions are defined with <name>(){...} and have to be defined before they are used
  # $1 = input          # functions can be called with their own parameters, and they are also referenced as $1 $2
  # $2 = output         # this means that the main program's $1,$2...parameters are no longer available inside the function
  local error           # a variable can be defined with local scope. if not, variables are always global
  (...)
  return 0              # this explicitly exits the function with a status code (0 = OK, > 0 = with an error)
}
```

## Arrays
```bash
numbers=(1 2 3)           # define array with all values
numbers[0]="one"          # replace 1st element (indexes start at 0) by "one"
echo ${numbers[@]}        # [@] represents the whole array
numbers=(${numbers[@]} 4) # add new element to array
${#numbers[@]}            # nb of elements in the array

for element in ${numbers[@]} ; do
  ...
done
```

## Stdin, Stdout, Stderr
each script, function, program has 3 default streams (file descriptors): stdin, stdout and stderr. 
E.g. 'sort' is a program that reads lines of text on stdin and outputs them sorted on stdout, 
and shows any errors it encounters in stderr.
```bash
# by default, `stdin` is your interactive terminal, `stdout` and `stderr` are both your terminal
sort                      # stdin = read from terminal (just type and end with CTRL-D), stdout = terminal
< input.txt sort          # stdin = read from input.txt, stdout = terminal
< input.txt sort > sorted.txt  # stdin = read from input.txt, stdout = written to sorted.txt
< input.txt sort >> sorted.txt # stdin = read from input.txt, stdout = append to sorted.txt
< input.txt sort > /dev/null   # stdin = read from input.txt, stdout = just ignore it, throw it away

echo "Output"             # writes "Output" to stdout = terminal
echo "Output" >&2         # writes "Output" to stderr = terminal
program 2> /dev/null > output.txt # write stdout to output.txt, and ignore stderr
program &> /output.txt    # redirect both stdout and stderr to output.txt

find . -name "*.txt" \
| while read -r file ; do # while read is a good way to run some code for each line
      output="$(basename "$file" .txt).out"
      [[ ! -f "$output" ]] && < "$file" sort > "$output"
  done
```
## Pipes
The `|` (pipe) character is bash's superpower. It allows you to build sophisticated chains of programs, 
where each passes its `stdout` to the next program's `stdin`. 
If the [Unix philosophy](https://en.wikipedia.org/wiki/Unix_philosophy) prescribes 
"_Write programs that do one thing and do it well_", 
then bash is the perfect tool to glue all those specialised programs together.
To sort, use `sort`, to search, use `grep`, to replace characters, use `tr`; 
to chain all these together, use bash and its pipes.

```bash
ls | sort | head -1       # ls lists filenames to its stdout, which is 'piped' (connected) to sort's stdin. 
                          # sort sends the sorted names to its stdout, which is piped to stdin of 'head -1'.
                          # head will just copy the first line from stdin to stdout and then stop
        
# the following chain will return the 5 most occurring non-comment lines in all .txt files              
cat *.txt | grep -v '^#' | sort | uniq -c | sort -nr | head -5
# this line gives a lowercase name for the current script (MyScript.sh -> myscript)
script_name=$(basename "$0" .sh | tr "[:upper:]" "[:lower:]")
```
## Processes
```bash
(                         # ( ... ) starts a new subshell with its own stdin/stdout
cat *.txt
curl -s https://host/archive.txt
) | sort

start_in_background &     # start the program and return immediately, don't wait for it to end

git commit -a && git push   # 'git push' will only execute if 'git commit -a' finished without errors
```

## Error handling
* `set -uo pipefail`: stop the script when errors happen
* install [shellcheck](https://github.com/koalaman/shellcheck), ideally in your IDE

## Go and script!
- use a strong bash boilerplate template like [pforret/bashew](https://github.com/pforret/basher)
- discover all the [SS64 Linux tools for Bash](https://ss64.com/bash/)
- [Learn bash in Y minutes](https://learnxinyminutes.com/docs/bash/) 

## Advanced Bash
- [Awesome Bash](https://github.com/awesome-lists/awesome-bash)
- [Bash pitfalls](https://mywiki.wooledge.org/BashPitfalls)

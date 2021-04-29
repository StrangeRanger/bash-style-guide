# Bash Style Guide

This style guide outlines how to write bash scripts with a style that makes them safe and predictable.  Most of this guide is based on [this wiki](http://mywiki.wooledge.org), specifically this page: http://mywiki.wooledge.org/BashGuide/Practices

If anything is not mentioned explicitly in this guide, it defaults to matching whatever is outlined in the wiki.

You can fork this style guide on GitHub: https://github.com/bahamas10/bash-style-guide

## Preface

This guide will try to be as objective as possible, providing reasoning for why individual decisions were made.  For choices that are purely aesthetic (and may not be universally agreeable), refer to the `Aesthetics` section below.

## Aesthetics

### Indents

Indents should be four spaces. Don't use tabs.

``` bash
var=true

if [[ var = true ]]; then
    echo "true"
fi
```

### Columns

It's preferred that columns are 88 characters or less. The only reason that the character limit should be exceeded is if it would negatively affect readability.

### Semicolons

Unless two commands/pieces of code are being placed on the same line, do not use semicolons where they are not needed.

``` bash
# Wrong
name="dave";
echo "hello $name";

# Right
name="dave"
echo "hello $name"

# Right
name="dave"; echo "hello $name"
```

The exception to this rule is outlined in the `Block Statements` section below. Namely, semicolons should be used for control statements like `if` or `while`.

### Functions

Don't use the `function` keyword. Instead, `()` should be appended to the end of the function name. All variables created in a function should be made local unless you plan on calling them from outside of the function they're defined in.

``` bash
# Wrong
function foo {
    i=foo  # This is now global, wrong depending on intent
}

# Right
foo() {
    local i=foo  # This is local, preferred
}
```

### Block Statements

`then` should be on the same line as `if`, and `do` should be on the same line as `while`.

``` bash
# Wrong
if true
then
    ...
fi

# Also wrong
true && {
    ...
}

# Right
if true; then
    ...
fi
```

### Spacing

No more than three consecutive newline characters, meaning no more than two blank lines.

### Comments

#### Generals

In general, the first letter of the first word of a comment, unless referencing a variable, should be capitalized. Allow for two spaces when appending comments to the end of a piece of code. Finally, don't change someone's comments for aesthetic reasons unless you are rewriting or updating them.

```bash
var=$(echo "True")  # This prints "True"  (Two spaces between ')' and '#')
box="Box"           # This is a box       (Aligned with the comment above for better readability)
```

#### Functions

When commenting a function and describing its purpose, always follow the format below:

```bash
func() {
    ####
    # FUNCTION INFO:
    # 
    # Functions description
    #
    # *Provide a description of any parameters used*
    # @param $1 description
    # @param $2 description
    ####

    ...
}
```

#### Block of code

When describing, say, what the code inside of an if statement does, always begin the comment with two pound signs instead of one. Two of them are used to indicate that you are commenting on a block of code instead of a single line/command. This is also applicable when making a single comment that speaks for several adjacent lines of code. In this case, a blank line should be used to signify that the comment is no longer applicable to the code beyond the line. When commenting on a single line of code, use a single pound sign to signify that you're referring to the following line of code instead of a section of code.

```bash
array=("a" "b" "c" "d" "e" "f" "g" "h")
x=true

## Prints out all items in $array, if x is true.
if [[ $x = true ]]; then
    for i in "${array[@]}"; do
        echo "$i"
    done
fi
```

```bash
## Variables the modify output color.
export _YELLOW=$'\033[1;33m'
export _GREEN=$'\033[0;32m'
export _CYAN=$'\033[0;36m'
export _RED=$'\033[1;31m'
export _NC=$'\033[0m'
export _CLRLN=$'\r\033[K'
export _GREY=$'\033[0;90m'

# Contains the raw url link to this 'README.md'.
export _RAW_URL="https://raw.githubusercontent.com/StrangeRanger/bash-style-guide/master/README.md"
```

### File formatting and commenting

Trying to describe this would take a lot of time and would be hard to explain. So below, I tried to create a detailed example with information about the formatting.

```bash
#!/bin/bash
#
# Script description
#
# Any other information
# 
########################################################################################
#### [ Name of section: Variables ]


# Two spaces between the '[ section name ]' comment and the beginning of the section code
...code here....
# Two spaces between the last line of code and the 'End of [ section name ]' comment


#### End of [ Variables ]
########################################################################################
#### [ Functions ]
#### If information or description about the section or something, it can be placed 
#### here. 


# Two spaces between the '[ section name ]' comment and the beginning of the section code
...code here....
# Two spaces between the last line of code and the 'End of [ section name ]' comment


#### End of [ Functions ]
########################################################################################
#### [ Main ]


# Two spaces between the '[ section name ]' comment and the beginning of the section code
...code here....
# Two spaces between the last line of code and the 'End of [ section name ]' comment


#### End of [ Main ]
########################################################################################
```

For a more detailed and example of how this format is used, take a look at the [example scripts](Examples/).

## Bashisms

This style guide is for bash. This means when given a choice, always prefer
bash builtins or keywords instead of external commands or `sh(1)` syntax.

### `test(1)`

Use `[[ ... ]]` for conditional testing, not `[ .. ]` or `test ...`.

``` bash
# Wrong
test -d /etc

# Also wrong
[ -d /etc ]

# Right
[[ -d /etc ]]
```

See http://mywiki.wooledge.org/BashFAQ/031 for more information.

### Sequences

Use bash builtins for generating sequences.

``` bash
n=10

# Wrong
for f in $(seq 1 5); do
    ...
done

# Wrong
for f in $(seq 1 "$n"); do
    ...
done

# Right
for f in {1..5}; do
    ...
done

# Right
for ((i = 0; i < n; i++)); do
    ...
done
```

### Command Substitution

Use `$(...)` for command substitution.

``` bash
foo=`date`   # Wrong
foo=$(date)  # Right
```

### Math / Integer Manipulation

Use `((...))` and `$((...))`.

``` bash
a=5
b=4

# Wrong
if [[ $a -gt $b ]]; then
    ...
fi

# Right
if ((a > b)); then
    ...
fi
```

Do **not** use the `let` command.

### Parameter Expansion

Always prefer [parameter expansion](http://mywiki.wooledge.org/BashGuide/Parameters#Parameter_Expansion) over external commands like `echo`, `sed`, `awk`, etc.

``` bash
name="bahamas10"

# Wrong
prog=$(basename "$0")
nonumbers=$(echo "$name" | sed -e 's/[0-9]//g')

# Right
prog=${0##*/}
nonumbers=${name//[0-9]/}
```

### Listing Files

Do not [parse ls(1)](http://mywiki.wooledge.org/ParsingLs), instead use bash builtin functions to loop files

``` bash
# very wrong, potentially unsafe
for f in $(ls); do
    ...
done

# Right
for f in *; do
    ...
done
```

### Arrays and lists

Use bash arrays instead of a string separated by spaces (or newlines, tabs, etc.) whenever possible:

``` bash
# Wrong
modules="json httpserver jshint"
for module in $modules; do
    npm install -g "$module"
done

# Right
modules=(json httpserver jshint)
for module in "${modules[@]}"; do
    npm install -g "$module"
done
```

Of course, in this example, it may be better expressed as:

``` bash
npm install -g "${modules[@]}"
```

... if the command supports multiple arguments, and you are not interested in catching individual failures.

### read builtin

Use the bash `read` builtin whenever possible to avoid forking external commands.

Example:

``` bash
fqdn="computer1.daveeddy.com"

IFS=. read -r hostname domain tld <<< "$fqdn"
echo "$hostname is in $domain.$tld"
# => "computer1 is in daveeddy.com"
```

## External Commands

### GNU userland tools

The whole world doesn't run on GNU or Linux; try to avoid GNU-specific options when forking external commands like `awk`, `sed`, `grep`, etc., to be as portable as possible.

When writing bash and using all the powerful tools and builtins bash gives you, you'll find it rare that you need to fork external commands to do simple string manipulation.

### UUOC

Don't use `cat(1)` when you don't need it.  If programs support reading from stdin, pass the data in using bash redirection.

``` bash
# Wrong
cat file | grep foo

# Right
grep foo < file

# also right
grep foo file
```

Prefer the use of the command-line tools built-in method of reading a file instead of passing in stdin.  This is where we make the inference that if a program says it can read a file passed by name, it's probably more performance to do that.

## Style

### Quoting

Use double quotes for strings that require variable expansion, command substitution interpolation, and everything in between.

``` bash
# Right
green=$'\033[0;32m'
foo="Hello World"
bar="You are $USER"
bar="You are \$USER"

# Wrong
foo='Hello World'
bar='You are $USER'
```

Two exceptions:

1. When using commands that by default require/use/recommend using single quotes, such as grep, sed, and trap.
2. When saving output formatting (text color, etc.) in variables (i.e. `green=$'\033[0;32m'`)

All variables should be quoted, whether or not they undergo word-splitting.

``` bash
foo="hello world"

if [[ -n $foo ]]; then  # No quotes needed

    echo "$foo"         # Quotes needed
fi

bar="$foo"              # Quotes needed
```

Two exceptions:

1. The first exception to this rule is if you call a variable within double brackets like shown above.
2. The second exception is if you would like the variable to be ignored if it is empty or does not exist. (Empty or non-existent variables that are quoted leave an empty string where called, while an unquoted variable completely ignores the variable)


### Variable Declaration

Unless exported, all variables should be lowercase. If a variable is being exported, it should be completely uppercase with `_` appended to the beginning of the variable.

Don't use `let` or `readonly` to create variables. `declare` should *only* be used for associative arrays.  `local` should always be used in functions unless the variable is called outside of the function. 

``` bash
# Wrong
declare -i foo=5
let foo++
readonly bar="something"
FOOBAR=baz
export food=5

# Right
i=5
((i++))
bar="something"
foobar=baz
export _FOOD=5  # or MY_FOOD
```

### shebang

Never use `#!/usr/bin/env bash`. This can cause your scripts to behave differently depending on who runs it. For this reason, use this line instead:

``` bash
#!/bin/bash
```

...unless you have a reason to use something else.

## Error Checking

`cd`, for example, doesn't always work.  Make sure to check for any possible errors for `cd` (or commands like it) and exit or break if they are present.

``` bash
# Wrong
cd /some/path  # This could fail
rm file        # If cd fails, where am I? what am I deleting?

# Right
cd /some/path || exit
rm file
```

### `trap`

Trapping is very useful for catching system signals such as SIGTERM, SIGINT, and so on. Make sure to use it where necessary.

### `set -e`

Don't set `errexit`.  Like in C, sometimes you want an error, or you expect something to fail, and that doesn't necessarily mean you want the program to exit.

This is a controversial opinion that I have on the surface, but the link below will show situations where `set -e` can do more harm than good because of its implications.

http://mywiki.wooledge.org/BashFAQ/105

### `eval`

Never.

### Redirecting Errors

When echoing an error message, always redirect the message to stderr:

``` bash
echo "Failed to execute file" >&2
```

## Common Mistakes

### Using {} instead of quotes

Using `${f}` is potentially different than `"$f"` because of how word-splitting is performed.  For example:

``` bash
for f in "1 space" "2  spaces" "3   spaces"; do
    echo ${f}
done
```

yields

```txt
1 space
2 spaces
3 spaces
```

Notice that it loses the number of spaces.  This is because the variable is expanded and undergoes word-splitting because it is unquoted.  This loop results in the three following commands being executed:

``` bash
echo 1 space
echo 2  spaces
echo 3   spaces
```

The extra spaces are effectively ignored here, and only two arguments are passed to the `echo` command in all three invocations.

If the variable was quoted instead:

``` bash
for f in "1 space" "2  spaces" "3   spaces"; do
    echo "$f"
done
```

yields

```txt
1 space
2  spaces
3   spaces
```

The variable `$f` is expanded but doesn't get split by bash, so it is passed as a single string (with spaces) to the `echo` command in all three invocations.

Note that, for the most part, `$f` is the same as `${f}`, and `"$f"` is the same as `"${f}"`.  The curly braces should only be used to ensure the variable name is expanded properly.  For example:

``` bash
$ echo "$HOME is $USERs home directory"
/home/dave is the home directory
$ echo "$HOME is ${USER}s home directory"
/home/dave is daves home directory
```

In this example, the braces were the difference between `$USER` vs. `$USERs` being expanded.

### Abusing for-loops when while would work better

`for` loops are great for iteration over arguments or arrays. Newline separated data is best left to a `while read -r ...` loop.

``` bash
users=$(awk -F: '{print $1}' /etc/passwd)
for user in $users; do
    echo "user is $user"
done
```

This example reads the entire `/etc/passwd` file to extract the usernames into a variable separated by newlines.  The `for` loop is then used to iterate over each entry.

This approach has many issues if used on other files with data that may contain spaces or tabs.

1. This reads *all* usernames into memory instead of processing them in a streaming fashion.
2. If the first field of that file contained spaces or tabs, the for loop would break on that as well as newlines
3. These only works *because* `$users` is unquoted in the `for` loop - if variable expansion only works for your purposes while unquoted, this is a good sign that something isn't implemented correctly.

To rewrite this:

``` bash
while IFS=: read -r user _; do
    echo "$user is user"
done < /etc/passwd
```

This will read the file in a streaming fashion, not pulling it all into memory, and will break on colons extracting the first field and discarding (storing as the variable `_`) the rest - using nothing but bash built-in commands.

## Extra

- http://mywiki.wooledge.org/BashPitfalls

## License

MIT License

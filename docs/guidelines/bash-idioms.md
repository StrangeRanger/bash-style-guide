# Bash Idioms

As a reminder, this style guide is written specifically for Bash scripting. When given a choice, <mark>**_ALWAYS_**</mark> prefer Bash built-ins or keywords over external commands or `sh(1)` syntax.

This section focuses on common Bash idioms: Bash-specific patterns, built-ins, keywords, and expansions that improve script safety, readability, performance, and maintainability.

/// admonition | Why This Matters
    type: tip

- **Safety**: Bash idioms often avoid common shell pitfalls such as word splitting, pathname expansion, unsafe parsing, and accidental reliance on external command behavior.
- **Clarity**: Bash-native constructs make the script's intent easier to see. For example, `[[ ... ]]`, arrays, parameter expansion, and arithmetic contexts communicate what the script is doing more directly than workarounds built from external commands.
- **Performance**: Built-ins, keywords, and expansions avoid unnecessary subprocesses, which is especially helpful inside loops or frequently called functions.
- **Consistency**: Relying on Bash features gives scripts more predictable behavior on systems where Bash is the target shell, rather than depending on differences between external tools or POSIX-compatible fallbacks.
- **Portability Tradeoff**: These patterns may not work in POSIX `sh`. Use them when the script is intentionally written for Bash, and use POSIX-compatible syntax only when `/bin/sh` portability is an explicit requirement.

///

## Conditional Tests

Conditional tests are essential in any programming language because they allow decisions based on evaluated expressions. Bash provides several constructs for these tests, including `[ ... ]`, `[[ ... ]]`, and the `test` command.

/// admonition | Guidelines
    type: info

- **Use `[[ ... ]]`:** <mark>**_ALWAYS_**</mark> use the `[[ ... ]]` construct when performing conditional tests.
- **Avoid Using `[ ... ]` and `test`:** <mark>**_DO NOT_**</mark> use `[ ... ]` and the `test` command for conditional tests.

///

/// admonition | Advantages of `[[ ... ]]`
    type: tip

- **Regex Support**: Enables direct regex matching within conditional expressions, removing the need for external tools like `grep`.
- **String Comparison**: Provides consistent and reliable string comparison, especially when variables contain spaces or special characters.
- **Compound Conditions**: Allows multiple conditions to be combined within a single `[[ ... ]]` block, simplifying logic and improving readability.
- **Safety**: Prevents [word splitting](https://mywiki.wooledge.org/WordSplitting) and [globbing](https://mywiki.wooledge.org/glob) on variables, reducing the risk of unexpected behavior.

///

/// details | Examples
    type: example
//// tab | Basic String Comparison

_Using `[ ... ]`:_

```bash
var="value with spaces"

if [ $var = "value with spaces" ]; then
    echo "The variable matches the value."
else
    echo "This will output because '$var' is treated as multiple arguments."
fi
```

**Potential Issues**: Without proper quoting, `[ ... ]` may cause unexpected behavior due to word splitting, treating the variable as multiple arguments.

---

_Using `[[ ... ]]`:_

```bash
var="value with spaces"

if [[ $var == "value with spaces" ]]; then
    echo "The variable matches the value."
else
    echo "This will output because $var is treated as multiple arguments."
fi
```

**Advantage**: `[[ ... ]]` safely handles variables with spaces, whether or not they are quoted.

////
//// tab | Regex Matching

_Using `grep` for regex matching:_

```bash
email="hunter@hthompson.dev"

if echo "$email" | grep -qE '^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$'; then
    echo "Valid email address."
else
    echo "Invalid email address."
fi
```

**Disadvantage**: `grep` is an external command, which can introduce errors and minor performance costs due to differing implementations across operating systems.

---

_Using `[[ ... ]]` for regex matching:_

```bash
email="hunter@hthompson.dev"

if [[ "$email" =~ ^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$ ]]; then
    echo "Valid email address."
else
    echo "Invalid email address."
fi
```

**Advantage**: `[[ ... ]]` directly supports regex matching, eliminating the need for external commands and improving script portability.

////
//// tab | Compound Conditions

_Using `[ ... ]` for compound conditions:_

```bash
file="example.txt"

touch "$file" && chmod 700 "$file"

if [ -f "$file" ] && [ -r "$file" ] || [ -w "$file" ]; then
    echo "File exists and is readable and/or writable."
else
    echo "File is either missing, unreadable, or unwritable."
fi
```

**Disadvantage**: `[ ... ]` does not support compound conditions directly, requiring additional commands or constructs.

---

_Using `[[ ... ]]` for compound conditions:_

```bash
file="example.txt"

touch "$file" && chmod 700 "$file"

if [[ -f $file && -r $file || -w $file ]]; then
    echo "File exists and is readable and/or writable."
else
    echo "File is either missing, unreadable, or unwritable."
fi
```

**Advantage**: `[[ ... ]]` supports compound conditions, simplifying logic and improving readability.

////
///

## Sequence Iteration

Iterating over sequences is common in Bash when processing elements in a range or list. Bash provides built-in mechanisms for sequence iteration, such as brace expansion and C-style `for` loops.

/// admonition | Guidelines
    type: info

- **Bash Built-ins**: <mark>**_ALWAYS_**</mark> use brace expansion (`{start..end}`) for fixed ranges and C-style `for` loops for variable limits when iterating over sequences.
- **Avoid Using `seq`:** <mark>**_DO NOT_**</mark> use `seq` for sequence iteration.

///

/// admonition | Advantages of Built-in Iteration
    type: tip

- **Simplicity**: Built-in mechanisms like brace expansion and C-style `for` loops are native to Bash and provide a straightforward, efficient way to iterate over sequences.
- **Reduced Dependencies**: Built-in features minimize external dependencies, improving script reliability and maintainability.

///

/// details | Examples
    type: example
//// tab | Iterating Over a Fixed Range

_Using `seq`:_

```bash
for i in $(seq 1 5); do
    echo "Number: $i"
done
```

---

_Using brace expansion:_

```bash
for i in {1..5}; do
    echo "Number: $i"
done
```

////
//// tab | Iterating Over a Variable Range

_Using `seq`:_

```bash
start=1
end=5

for i in $(seq $start $end); do
    echo "Number: $i"
done
```

---

_Using C-style `for` loop:_

```bash
start=1
end=5

for (( i=start; i<=end; i++ )); do
    echo "Number: $i"
done
```

////
//// tab | Iterating with Step Values

_Using `seq`:_

```bash
for i in $(seq 0 2 100); do
    echo "Number: $i"
done
```

---

_Using C-style `for` loop:_

```bash
for ((i=0; i<=100; i+=2)); do
    echo "Number: $i"
done
```

////
///

## Command Substitution

[Command substitution](https://mywiki.wooledge.org/CommandSubstitution) lets you capture the output of a command and use it as part of another command or assignment. Bash provides two syntaxes for command substitution: `$(...)` and backticks (`` `...` ``).


/// admonition | Guidelines
    type: info

- **Use `$(...)`**: <mark>**_ALWAYS_**</mark> use `$(...)` for command substitution instead of backticks.
- **Avoid Using Backticks**: <mark>**_DO NOT_**</mark> use backticks for command substitution.

///

/// admonition | Advantages of `$(...)`
    type: tip

- **Improved Readability**: `$(...)` is visually clearer, making scripts easier to read, especially as commands become more complex.
- **Easier Nesting**: Makes nested commands easier to write and read than backticks.
- **Enhanced Safety**: More robust and less prone to errors, especially in complex command substitutions.

///

/// details | Examples
    type: example
//// tab | Basic Command Substitution

_Using backticks:_

```bash
output=`ls -l`
echo "The output is: $output"
```

---

_Using `$(...)`_

```bash
output=$(ls -l)
echo "The output is: $output"
```

////
//// tab | Nesting Command Substitution

_Using backticks:_

```bash
date_and_users=`echo "Date: \`date\` - Users: \`who | wc -l\`"`
echo $date_and_users
```

**Disadvantage**: Nested commands with backticks can be difficult to read and are often prone to errors.

---

_Using `$(...)`:_

```bash
date_and_users=$(echo "Date: $(date) - Users: $(who | wc -l)")
echo $date_and_users
```

**Advantage**: The `$(...)` syntax simplifies nested commands, improving readability and reducing the likelihood of errors.

////
///

## Arithmetic Operations

Arithmetic operations in Bash support mathematical calculations, comparisons, and other numeric work. Bash provides arithmetic expansion `$((...))`, conditional arithmetic expressions `((...))`, and the `let` command.

/// admonition | Guidelines
    type: info

- **Use `$((...))` and `((...))`**: <mark>**_ALWAYS_**</mark> use `$((...))` for arithmetic expansions and `((...))` for conditional arithmetic expressions.
- **Avoid Using `let`**: <mark>**_DO NOT_**</mark> use `let` for arithmetic operations.

///

/// admonition | Advantages of `$((...))` and `((...))`
    type: tip

- **Clarity**: Both `((...))` and `$((...))` clearly indicate arithmetic operations, making scripts easier to understand.
- **Increased Safety**: Unlike `let`, these methods evaluate arithmetic expressions without risking accidental command execution.

///

/// details | Examples
    type: example
//// tab | Basic Arithmetic

_Using `let`:_

```bash
let result=1+2
echo "Result: $result"
```

---

_Using `$((...))`:_

```bash
result=$(( 1 + 2 ))
echo "Result: $result"
```

////
//// tab | Conditional Arithmetic Expressions

_Using `let`:_

```bash
a=5
b=10

if let "a < b"; then
    echo "a is less than b"
fi
```

---

_Using `((...))`:_

```bash
a=5
b=10

if (( a < b )); then
    echo "a is less than b"
fi
```

---

**Explanation**: `((...))` provides a more concise and readable way to evaluate arithmetic expressions within conditional statements.

////
///

## Parameter Expansion

[Parameter expansion](https://mywiki.wooledge.org/BashGuide/Parameters#Parameter_Expansion) in Bash lets you manipulate variables and perform string operations directly in the shell. It supports options such as substring extraction, string replacement, and transformations.

/// admonition | Guidelines
    type: info

- **Use Parameter Expansion**: Use parameter expansion for tasks such as string manipulation, default value assignment, and pattern matching.
- **Limit Use of External Tools**: Minimize the use of tools like `sed` and `awk` for string manipulation unless advanced text processing is required and Bash cannot handle it cleanly.
    - **Rationale**: While powerful, these tools are often unnecessary for tasks that parameter expansion can handle efficiently.

///

/// admonition | Advantages of Parameter Expansion
    type: tip

- **Streamlined Scripting**: Keeps string manipulation inline and shell-native, simplifying the script's logic.
- **Enhanced Portability**: Improves portability across Unix-like systems by avoiding dependencies on external tools that may not be available or behave consistently.

///

/// details | Examples
    type: example
//// tab | Extracting Substrings

_Using `sed`_

```bash
string="Hello, World!"
substring=$(echo "$string" | sed 's/Hello,//')

echo "$substring"
```

---

_Using Parameter Expansion_

```bash
string="Hello, World!"
substring=${string/Hello,/}

echo "$substring"
```

---

**Explanation**: Parameter expansion offers a more direct and efficient way to extract substrings than external tools like `sed`.

////
//// tab | Removing a Suffix

_Using `awk`_

```bash
filename="document.txt"
basename=$(echo $filename | awk -F. '{print $1}')

echo $basename
```

---

_Using Parameter Expansion_

```bash
filename="document.txt"
basename=${filename%.txt}
echo $basename
```

---

**Explanation**: Parameter expansion simplifies removing suffixes from filenames and is more efficient than external commands like `awk`.

////
//// tab | String Length

_Using `wc`_

```bash
string="Hello, World!"
length=$(echo -n $string | wc -c)
echo $length
```

---

_Using Parameter Expansion_

```bash
string="Hello, World!"
length=${#string}
echo $length
```

---

**Explanation**: Parameter expansion provides a more straightforward and efficient way to determine string length than external commands like `wc`.

////
///

## Avoid Parsing `ls`

Parsing the output of `ls` in Bash scripts can cause errors and unpredictable behavior, especially with filenames that contain spaces or special characters. Use safer and more reliable alternatives for handling files and directories, such as Bash globbing, `find`, and `read`.

/// admonition | Guidelines
    type: info

- **Bash Built-ins**: <mark>**_ALWAYS_**</mark> use Bash globbing patterns (`*`) or commands like `find` for reliable file and directory operations.
- **Avoid Using `ls`**: <mark>**_DO NOT_**</mark> use `ls` in scripts where accurate filename handling is necessary, such as in loops or complex operations.

///

/// admonition | Risks of Parsing `ls`
    type: warning

- **Word Splitting**: Filenames with spaces or special characters may cause unintended word splitting when processed by `ls`, leading to errors.
- **Command Misinterpretation**: Filenames that begin with a hyphen (`-`) may be interpreted as command-line options, causing unintended behavior.
- **Potential Vulnerabilities**: Improper handling of filenames can introduce bugs or security vulnerabilities, especially in more complex scripts.

///

/// admonition | Alternatives to Parsing `ls`
    type: tip

- **Bash Globbing**: Use Bash globbing patterns, such as `*`, to list files and directories. This method is safer and more reliable for filenames with special characters.
- **`find` Command**: Use the `find` command for complex file operations. It provides extensive options for searching and handling files.
- **`read` Command**: Use `read` with the appropriate settings to safely process filenames without relying on external commands like `ls`.
- **`stat` Command**: Use `stat` to obtain detailed file information, such as size, permissions, and modification times.
- **`file` Command**: Use `file` to identify the type of a file based on its content, rather than its extension.

///

/// details | Examples
    type: example
//// tab | Listing Files in a Directory

<!-- TODO: Maybe add "potential issues" or "disadvantages" for each example? -->

_Using `ls` in a loop:_

```bash
for file in $(ls /path/to/dir); do
    echo "Processing $file"
done
```

---

_Using Bash Globbing:_

```bash
for file in /path/to/dir/*; do
    echo "Processing $(basename "$file")"
done
```

---

**Explanation**: Bash globbing uses patterns like `*` to match filenames directly in the specified directory, eliminating the need for external commands like `ls`. The shell expands glob patterns, such as `/path/to/dir/*`, into a list of matching filenames. This makes the method safer and more reliable for filenames with spaces, newlines, or special characters.

////
//// tab | Using `find` for File Operations

<!-- TODO: Maybe add "potential issues" or "disadvantages" for each example? -->

_Using `ls` with wildcards:_

```bash
for file in $(ls /path/to/dir/*.txt); do
    echo "Processing $file"
done
```

---

_Using `find`:_

```bash
find /path/to/dir -type f -name "*.txt" -exec echo "Processing {}" \;
```

---

**Explanation**: The `find` command offers flexible options for file searching and operations, making it more suitable for complex tasks than `ls`. It provides precise control over which files are selected and how they are processed.

////
//// tab | Parsing Filenames with `read`

<!-- TODO: Maybe add "potential issues" or "disadvantages" for each example? -->

_Using `ls` with `while` loop:_

```bash
ls /path/to/dir > filelist.txt
while read -r file; do
    echo "Processing $file"
done < filelist.txt
```

---

_Using `find` and `read`:_

```bash
find /path/to/dir -type f > filelist.txt
while IFS= read -r file; do
    echo "Processing $file"
done < filelist.txt
```

---

**Explanation**: Setting the `IFS` (Internal Field Separator) correctly with `read` ensures that filenames with spaces or special characters are processed safely. This avoids the risks associated with parsing the output of `ls`.

////
//// tab | Detailed File Information

<!-- TODO: Maybe add "potential issues" or "disadvantages" for each example? -->

_Using `ls -l`:_

```bash
ls -l /path/to/file
```

---

_Using `stat`:_

```bash
stat /path/to/file
```

---

**Explanation**: `stat` provides more comprehensive metadata about a file than `ls -l`, such as size, permissions, and modification times.

////
//// tab | Determining File Type

<!-- TODO: Maybe add "potential issues" or "disadvantages" for each example? -->

_Using `ls` with file extension check:_

```bash
if [[ $(ls /path/to/file) == *.txt ]]; then
    echo "This is a text file"
fi
```

---

_Using `file`:_

```bash
file_type=$(file --mime-type -b /path/to/file)
if [[ $file_type == "text/plain" ]]; then
    echo "This is a text file"
fi
```

---

**Explanation**: The `file` command examines the contents of a file to determine its type, offering a more accurate method than relying solely on file extensions.

////
///

## Element Collections

Element collections are a fundamental part of Bash scripting and let you manage groups of items, such as filenames, user inputs, or configuration values. Bash provides three main ways to handle collections: arrays, associative arrays, and space-separated strings.

/// admonition | Guidelines
    type: info

- **Arrays for Collections**: <mark>**_ALWAYS_**</mark> use arrays when managing collections of elements.
- **Avoid Space-Separated Strings**: <mark>**_DO NOT_**</mark> use space-separated strings for handling collections.

///

/// admonition | Advantages of Arrays
    type: tip

- **Clarity and Safety**: Arrays prevent errors caused by word splitting and glob expansion that can occur with space-separated strings.
- **Flexibility**: Arrays make it easy to manipulate and access individual elements and to expand collections in commands that accept multiple arguments.
- **Ease of Maintenance**: Code that uses arrays is generally clearer and easier to maintain, especially as script complexity increases.

///

/// details | Examples
    type: example
//// tab | Iterating Over a Collection

_Using Space-Separated Strings_

```bash
items="apple orange banana"
for item in $items; do
    echo "Item: $item"
done
```

---

_Using Arrays_

```bash
items=("apple" "orange" "banana")
for item in "${items[@]}"; do
    echo "Item: $item"
done
```

**Advantage**: Arrays handle items with spaces or special characters correctly, preventing unintended word splitting.

////
//// tab | Passing Multiple Arguments to a Command

_Using Space-Separated Strings_

```bash
files="file1.txt file2.txt file3.txt"
cp $files /destination/
```

---

_Using Arrays_

```bash
files=("file1.txt" "file2.txt" "file3.txt")
cp "${files[@]}" /destination/
```

**Advantage**: Arrays simplify command syntax and ensure all arguments are passed correctly, even if filenames contain spaces.

////
//// tab | Accessing Individual Elements

_Using Space-Separated Strings_

```bash
string="one two three"
first=$(echo $string | cut -d' ' -f1)
echo "First element: $first"
```

---

_Using Arrays_

```bash
array=("one" "two" "three")
first=${array[0]}
echo "First element: $first"
```

**Advantage**: Arrays allow direct access to individual elements without additional parsing.

////
//// tab | Adding Elements to a Collection

_Using Space-Separated Strings_

```bash
string="apple orange"
string="$string banana"
echo $string
```

---

_Using Arrays_

```bash
array=("apple" "orange")
array+=("banana")
echo "${array[@]}"
```

**Advantage**: Arrays provide straightforward syntax for adding elements, which improves readability and maintainability.

////
///

## Parsing Input into Variables

Parsing input into variables is common in Bash scripting when extracting and processing data from user input, files, or other sources. Bash provides the `read` command for these tasks, offering a more efficient and safer alternative to external commands like `awk`, `sed`, or `cut`.

/// admonition | Guidelines
    type: info

- **Bash Built-ins**: Use `read` to safely and efficiently parse user input and other data directly into variables.
- **Customize with IFS**: Adjust the Internal Field Separator (IFS) as needed when using `read` so input is split according to your requirements.
- **Avoid External Commands**: Minimize the use of external commands like `awk`, `sed`, or `cut` for parsing strings.

///

/// admonition | Advantages of `read`
    type: tip

- **Efficiency**: `read` operates within the shell, eliminating the need for slower external commands.
- **Controlled Parsing**: `read` parses input directly into variables within the shell, reducing the complexity and risks of external input-processing commands.
- **Simplicity**: Provides easy-to-understand syntax for directly parsing complex data structures.
- **Portability**: `read` is a Bash builtin, ensuring consistent behavior across different systems and environments.

///

/// details | Examples
    type: example
//// tab | Parsing a Space-Separated String

_Using `awk`_

```bash
string="one two three"
first=$(echo $string | awk '{print $1}')
second=$(echo $string | awk '{print $2}')
third=$(echo $string | awk '{print $3}')
echo "First: $first, Second: $second, Third: $third"
```

---

_Using `read`_

```bash
string="one two three"
read -r first second third <<< "$string"
echo "First: $first, Second: $second, Third: $third"
```

**Advantage**: `read` provides a simpler and more efficient way to parse space-separated strings directly into variables.

////
//// tab | Parsing User Input

_Using `sed`_

```bash
input="username:password"
username=$(echo $input | sed 's/:.*//')
password=$(echo $input | sed 's/.*://')
echo "Username: $username, Password: $password"
```

---

_Using `read`_

```bash
input="username:password"
IFS=':' read -r username password <<< "$input"
echo "Username: $username, Password: $password"
```

**Advantage**: `read` directly parses the input into variables, reducing complexity and improving readability.

////
///

## Efficient Array Population

The `mapfile` command, also known as `readarray`, is a Bash built-in that populates an array with lines from a file or the output of a command. It simplifies reading and storing multiline data, making it useful for large datasets or input that needs to be processed line by line.

/// admonition | Guidelines
    type: info

- **Use `mapfile` for Multiline Input**: When you need to read and store multiline data in an array, prefer `mapfile` over looping constructs.
- **Specify a Delimiter with `-d`**: Use the `-d` option to specify a custom delimiter if the input data is not newline-separated.
- **Avoid Excessive Loops**: `mapfile` can efficiently replace complex loops that read and store data line by line, improving script readability and performance.

///

/// admonition | Advantages of `mapfile`
    type: tip

- **Efficiency**: `mapfile` reads an entire stream into an array at once, making it faster and more efficient than reading lines individually in a loop.
- **Simplicity**: The command reduces the amount of code required to populate arrays, making scripts cleaner and easier to maintain.
- **Flexibility**: With options like `-t` (to remove trailing newlines) and `-d` (to specify a delimiter), `mapfile` handles a variety of input formats.

///

/// details | Examples
    type: example
//// tab | Reading a File into an Array

_Using a loop:_

```bash
lines=()
while IFS= read -r line; do
    lines+=("$line")
done < input.txt
```

---

_Using `mapfile`:_

```bash
mapfile -t lines < input.txt
```

**Advantage**: `mapfile` simplifies reading a file into an array, reducing code and improving efficiency.

////
//// tab | Reading Command Output into an Array

_Using a loop:_

```bash
declare -a commands
while IFS= read -r command; do
    commands+=("$command")
done < <(ls /usr/bin)
```

---

_Using `mapfile`:_

```bash
mapfile -t commands < <(ls /usr/bin)
```

**Advantage**: `mapfile` reads command output into an array without an explicit loop.

////
//// tab | Using a Custom Delimiter

_Example: Reading data separated by colons (:):_

```bash
input="one:two:three:four"
mapfile -d ':' -t items <<< "$input"
echo "${items[@]}"
```

**Explanation**: The `-d` option allows `mapfile` to split input on a custom delimiter, which is useful for parsing structured data.

////
///

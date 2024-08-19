# Bashisms

As a reminder, this style guide is intended for Bash. When given a choice, <mark>**_ALWAYS_**</mark> prefer Bash built-ins or keywords over external commands or `sh(1)` syntax.

This section covers common Bashisms, which are idiomatic practices and features specific to the Bash shell. By understanding and utilizing these Bash-specific constructs, you can write scripts that are more efficient, readable, and robust.

## Conditional Tests

Conditional tests are fundamental to any programming language, enabling decision-making based on evaluating expressions. Bash provides several constructs for these tests, including `[ ... ]`, `[[ ... ]]`, and the `test` command.

/// admonition | Guidelines
    type: info

- **Use `[[ ... ]]`:** <mark>**_ALWAYS_**</mark> use the `[[ ... ]]` construct for conditional tests.
- **Avoid `[ ... ]` and `test`:** Avoid using `[ ... ]` and the `test` command for conditional tests.

///

/// admonition | Advantages of `[[ ... ]]`
    type: tip

- **Regex Support**: Enables direct regex matching within conditional expressions, eliminating the need for external tools like `grep`.
- **String Comparison**: Offers a more consistent and reliable approach to string comparison, especially when dealing with variables containing spaces or special characters.
- **Compound Conditions**: Allows combining multiple conditions within a single `[[ ... ]]` block, simplifying logic and improving readability.
- **Safety**: Prevents word splitting and globbing on variables, reducing the risk of unexpected behavior or security vulnerabilities.

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

**Potential Issues**: Without proper quoting, `[ ... ]` can cause unexpected behavior due to word splitting, treating the variable as multiple arguments.

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

**Disadvantage**: Since `grep` is an external command, the potential for errors and performance issues increases due to different implementations across systems.

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

**Advantage**: `[[ ... ]]` directly supports regex matching, eliminating the need for external commands and enhancing script portability.

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

**Disadvantage**: `[ ... ]` does not support compound conditions directly, requiring additional commands or constructs to achieve the desired behavior.

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

**Advantage**: `[[ ... ]]` supports compound conditions, simplifying script logic and improving readability.

////
///

## Sequence Iteration

Iterating over sequences is a common task in Bash, allowing you to process elements in a range or list. Bash provides built-in mechanisms for sequence iteration, such as brace expansion and C-style `for` loops.

/// admonition | Guidelines
    type: info

- **Bash Built-ins**: <mark>**_ALWAYS_**</mark> use brace expansion (`{start..end}`) for fixed ranges and the C-style `for` loop for variable limits when iterating over sequences.
- **Avoid Using `seq`:** Avoid using `seq` for sequence iteration, as it is an external command and not a built-in feature of Bash.

///

/// admonition | Advantages of Built-in Iteration
    type: tip

- **Simplicity**: Built-in mechanisms like brace expansion and C-style `for` loops are native to Bash, providing a straightforward and efficient way to iterate over sequences.
- **Reduced Dependencies**: By utilizing built-in features, you minimize external dependencies, which enhances script reliability and maintainability.

///

/// details | Examples
    type: example
//// tab | A Fixed Range

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
//// tab | Variable Range Iteration

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
//// tab | Step Values

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

Command substitution allows you to capture the output of commands and use it as part of another command or assignment. Bash provides two syntaxes for command substitution: `$(...)` and backticks (`` `...` ``).



/// admonition | Guidelines
    type: info

- **Use `$(...)`:** <mark>**_ALWAYS_**</mark> use the `$(...)` syntax for command substitution instead of backticks.
- **Avoid Backticks**: Avoid using backticks for command substitution.

///

/// admonition | Advantages of `$(...)`
    type: tip

- **Improved Readability**: The `$(...)` format is visually clearer, making scripts easier to read and understand, especially as commands become more complex.
- **Easier Nesting**: Facilitates the nesting of multiple commands without the syntactic awkwardness associated with backticks.
- **Enhanced Safety**: The `$(...)` syntax is more robust and less prone to errors, particularly in complex command substitutions.

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

**Explanation**: The `$(...)` syntax is clearer and preferred for command substitution.

////
//// tab | Nesting Command Substitution

_Using backticks:_

```bash
date_and_users=`echo "Date: \`date\` - Users: \`who | wc -l\`"`
echo $date_and_users
```

---

_Using `$(...)`:_

```bash
date_and_users=$(echo "Date: $(date) - Users: $(who | wc -l)")
echo $date_and_users
```

**Explanation**: Nesting commands with `$(...)` is easier to read and less error-prone compared to using backticks.

////
///

## Arithmetic Operations

Arithmetic operations allow for mathematical calculations within Bash, enabling you to perform calculations, comparisons, and other numeric operations. Bash supports arithmetic operations using arithmetic expansions `$((...))`, conditional arithmetic expressions `((...))`, and the `let` command.

/// admonition | Guidelines
    type: info

- **Use `$((...))` and `((...))`**: <mark>**_ALWAYS_**</mark> use `$((...))` for arithmetic expansions and `((...))` for conditional arithmetic expressions.
- **Avoid `let`**: Avoid using `let` for arithmetic.
- **Reasoning**: `$((...))` and `((...))` provide a clearer and more intuitive syntax for arithmetic operations in Bash.

///

/// admonition | Advantages of `$((...))` and `((...))`
    type: tip

- **Clarity**: Both `((...))` and `$((...))` clearly delineate arithmetic expressions within scripts, making them easy to identify and understand.
- **Simplicity**: These constructs are natively supported by Bash, offering a streamlined and intuitive approach to handling arithmetic.
- **Increased Safety**: These methods specifically evaluate arithmetic without the risk of executing other commands, unlike let, which can misinterpret expressions as commands if not carefully handled.

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

**Explanation**: The` $((...))` syntax is clearer and reduces the risk of errors, making it the preferred method for basic arithmetic in Bash.

////
//// tab | Conditional Arithmetic Expressions

_Using `let`:_

```bash
let "a = 5"
let "b = 10"

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

**Explanation**: The `((...))` syntax simplifies conditional arithmetic expressions, enhancing readability and reducing complexity.

////
///

## Parameter Expansion

Parameter expansion is a powerful feature in Bash that allows you to manipulate variables and perform string operations directly within the shell. Bash provides a wide range of parameter expansion options, including substring extraction, string replacement, and length calculation.

/// admonition | Guidelines
    type: info

- **Bash Built-ins**: Utilize parameter expansion for string manipulations whenever possible. This approach is more efficient and reduces script complexity and dependencies.
- **Avoid External Tools**: Avoid using external tools like `sed` and `awk` for string manipulation.
    - **Reason**: While powerful, these tools are often overkill for simple string manipulations that can be efficiently handled by parameter expansion.

///

/// admonition | Advantages of Parameter Expansion
    type: tip

- **Streamlined Scripting**: Keeps string manipulations inline and shell-native, simplifying the script's logic.
- **Enhanced Portability**: Improves script portability across different Unix-like systems by avoiding dependency on external tools, which may vary in availability or functionality.

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

**Explanation**: Parameter expansion provides a simpler and more efficient method for extracting substrings.

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

**Advantage**: Parameter expansion is simpler and more efficient for removing a suffix, reducing the need for external tools.

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

**Advantage**: Parameter expansion can directly compute the length of a string without invoking an external command, making it a more efficient choice.

////
///

## Avoiding Parsing `ls`

While parsing the output of `ls` may seem like a convenient way to list files and directories, this approach is generally discouraged due to the potential for errors and unexpected behavior. Instead, Bash provides more reliable and secure methods for file and directory parsing, such as Bash globbing patterns (`*`).

/// admonition | Guidelines
    type: info

- **Bash Built-ins**: <mark>**_ALWAYS_**</mark> use Bash globbing patterns like `*` or tools like `find` for file and directory parsing.
- **Avoid Using `ls`**: Avoid using `ls` in loops or where accurate filename interpretation is critical.

///

/// admonition | Risks of Parsing `ls`
    type: warning

- **Word Splitting**: Filenames with spaces or special characters can cause word splitting issues when parsing `ls` output.
- **Command Interpretation**: Filenames starting with a hyphen (`-`) can be misinterpreted as options by commands like `ls`, leading to unintended behavior.
- **Potential Vulnerabilities**: Although parsing `ls` output may not directly cause security vulnerabilities like code injection, improper handling of filenames can introduce bugs or unexpected behavior, which may lead to security issues in more complex scenarios.

///

/// admonition | Alternatives to Parsing `ls`
    type: tip

- **Bash Globing**: Use Bash globbing patterns like `*` for listing files and directories, ensuring reliable and secure file parsing.
- **`find` Command**: For more complex file operations, consider using the `find` command, which provides extensive options for file and directory traversal.
- **`read` Command**: When processing files or directories, use the `read` command to safely parse inputs into variables, avoiding the need for external commands like `ls`.
- **`stat` Command**: For detailed file information, use the `stat` command, which provides extensive metadata about files and directories.
- **`file` Command**: To determine file types, use the `file` command, which can identify the type of a file based on its contents.

///

/// details | Examples
    type: example
//// tab | Listing Files in a Directory

_Using `ls` in a loop:_

```bash
for file in $(ls /path/to/dir); do
    echo "Processing $file"
done
```

---

_Using Bash Globing:_

```bash
for file in /path/to/dir/*; do
    echo "Processing $(basename "$file")"
done
```

**Explanation**: Bash globbing handles filenames with spaces or special characters correctly, making it more reliable than parsing `ls` output.

////
//// tab | Using `find` for File Operations

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

**Explanation**: The `find` command is powerful and versatile, allowing you to search for files by various criteria and execute commands on the results, making it suitable for more complex file operations.

////
//// tab | Parsing Filenames with `read`

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

**Explanation**: Using `read` with `IFS` (Internal Field Separator) set to handle filenames correctly ensures that the script processes each file safely, even if the filenames contain spaces or special characters.

////
//// tab | Detailed File Information

_Using `ls -l`:_

```bash
ls -l /path/to/file
```

---

_Using `stat`:_

```bash
stat /path/to/file
```

**Explanation**: The `stat` command provides comprehensive metadata about a file, including size, permissions, and modification times, offering more detailed information than `ls -l`.

////
//// tab | Determining File Type

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

**Explanation**: The `file` command determines the file type based on its content, rather than just its extension, providing a more accurate and reliable method for file type detection.

////
///

## Element Collections

Element collections are a common feature in Bash scripting, allowing you to manage groups of items such as filenames, user inputs, or configuration values. Bash provides three primary methods for handling collections: arrays, associative arrays, and space-separated strings.

/// admonition | Guidelines
    type: info

- **Arrays for Collections**: <mark>**_ALWAYS_**</mark> use arrays when managing collections of elements.
- **Avoid Space-Separated Strings**: Avoid using space-separated strings for collections.

///

/// admonition | Advantages of Arrays
    type: tip

- **Clarity and Safety**: Arrays prevent errors related to word splitting and glob expansion that can occur with space-separated strings.
- **Flexibility**: Arrays allow for straightforward manipulation and access to individual elements, as well as simpler expansion in commands that accept multiple arguments.
- **Ease of Maintenance**: Code utilizing arrays is generally clearer and easier to maintain, particularly as script complexity increases.

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

**Advantage**: Arrays simplify command syntax and ensure all arguments are correctly passed, even if filenames contain spaces.

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
//// Adding Elements to a Collection

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

**Advantage**: Arrays provide straightforward syntax for adding elements, improving readability and maintainability.

////
///

## Parsing Input into Variables

Parsing input into variables is a common task in Bash scripting, allowing you to extract and process data from user inputs, files, or other sources. Bash provides the `read` command for these tasks, offering a more efficient and secure alternative to external commands like `awk`, `sed`, or `cut`.

/// admonition | Guidelines
    type: info

- **Bash Built-ins**: Use `read` to safely and efficiently parse user inputs and other data directly into variables.
- **Customize with IFS**: Adjust the Internal Field Separator (IFS) as needed when using `read` to ensure that inputs are split according to your specific requirements, enhancing the flexibility and accuracy of data parsing.
- **Avoid External Commands**: Minimize the use of external commands like `awk`, `sed`, or `cut` for parsing strings.

///

/// admonition | Advantages of `read`
    type: tip

- **Efficiency**: `read` operates within the shell, eliminating the need for slower, resource-intensive external commands.
- **Controlled Parsing**: `read` allows for controlled and direct parsing of inputs into variables within the shell, reducing the complexity and potential risks associated with using external commands for input processing.
- **Simplicity**: Offers an easy-to-understand syntax for directly parsing complex data structures.
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

The `mapfile` command, also known as `readarray`, is a Bash built-in that provides an efficient way to populate an array with lines from a file or the output of a command. This command simplifies the process of reading and storing multiline data, making it particularly useful when working with large datasets or when you need to process input line by line.

/// admonition | Guidelines
    type: info

- **Use `mapfile` for Multiline Input**: When you need to read and store multiline data into an array, prefer mapfile over looping constructs.
- **Specify a Delimiter with `-d`**: Use the `-d` option to specify a custom delimiter if the input data is not newline-separated.
- **Avoid Excessive Loops**: `mapfile` can efficiently replace complex loops that read and store data line by line, improving script readability and performance.

///

/// admonition | Advantages of `mapfile`
    type: tip

- **Efficiency**: `mapfile` reads an entire stream into an array at once, making it faster and more efficient than reading lines individually in a loop.
- **Simplicity**: The command reduces the amount of code required to populate arrays, leading to cleaner and more maintainable scripts.
- **Flexibility**: With options like `-t` (to remove trailing newlines) and `-d` (to specify a delimiter), mapfile offers flexibility in handling various input formats.

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

**Advantage**: `mapfile` simplifies the process of reading a file into an array, reducing the code and improving efficiency.

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

**Advantage**: `mapfile` efficiently reads the output of a command into an array without the need for an explicit loop.

////
//// tab | Using a Custom Delimiter

_Example: Reading data separated by colons (:):_

```bash
input="one:two:three:four"
mapfile -d ':' -t items <<< "$input"
echo "${items[@]}"
```

**Explanation**: The `-d` option allows `mapfile` to split the input based on a custom delimiter, which can be useful for parsing structured data.

////
///

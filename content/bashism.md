# Bashisms

As a reminder, this style guide is intended for Bash. When given a choice, always use Bash builtins or keywords instead of external commands or `sh(1)` syntax.

## Conditional Test

Conditional tests are a fundamental aspect of Bash scripting, allowing you to make decisions based on the evaluation of expressions. Bash provides multiple constructs for conditional tests, with `[[ ... ]]` being the most modern and robust option.

/// admonition | Guidelines
    type: tip

- **Use `[[ ... ]]`:** Use the `[[ ... ]]` construct for conditional tests, over `[ ... ]` or the `test` command.

///

/// details | Examples
//// tab | Basic String Comparison

_Using `[ ... ]`_

```bash
if [ "$string" = "hello" ]; then
  echo "String is hello"
fi
```

---

_Using `[[ ... ]]`_

```bash
if [[ $string == "hello" ]]; then
  echo "String is hello"
fi
```

**Advantage**: `[[ ... ]]` is more readable and consistent in handling string comparisons.

////
//// tab | Regex Matching

_Using `test`_

```bash
if echo "$name" | grep -q "^A"; then
  echo "Name starts with A"
fi
```

**Disadvantage**: Using external commands like `grep` can be slower and less efficient. Additionally, it may introduce unnecessary complexity and dependencies.

---

_Using `[[ ... ]]`_

```bash
if [[ $name =~ ^A ]]; then
  echo "Name starts with A"
fi
```

**Advantage**: `[[ ... ]]` supports regex directly, simplifying the syntax and improving readability.

////
//// tab | Compound Conditions

_Using `[ ... ]`_

```bash
if [ "$var1" = "value1" ] && [ "$var2" = "value2" ]; then
  echo "Both conditions are true"
fi
```

**Disadvantage**: `[ ... ]` requires separate test commands for each condition, leading to more verbose and less readable code.

---

_Using `[[ ... ]]`_

```bash
if [[ $var1 == "value1" && $var2 == "value2" ]]; then
  echo "Both conditions are true"
fi
```

**Advantage**: `[[ ... ]]` provides a more readable and concise syntax for compound conditions.

////
///

### Benefits of `[[ ... ]]`

- **Enhanced Features**: Supports advanced operations like regex matching and string comparison.
- **Increased Safety**: Eliminates word splitting on variables containing spaces, preventing errors such as unintentional command execution or misinterpreted conditions.
- **Greater Flexibility**: Provides a more readable and manageable syntax, particularly in scripts with intricate conditional logic.

## Sequence Iteration

Iterating over sequences is a common task in Bash scripting, allowing you to process elements in a range or list. Bash provides built-in mechanisms for sequence iteration, such as brace expansion and C-style `for` loops.

/// admonition | Guidelines
    type: tip

- **Prefer Builtins**: Use brace expansion (`{start..end}`) for fixed ranges and the C-style `for` loop for variable limits to iterate over sequences.
- **Avoid Using `seq`:** Due to its external nature, avoid using `seq` for sequence iteration.

///

/// details | Examples
//// tab | A Fixed Range

_Using `seq`_

```bash
for i in $(seq 1 5); do
  echo "Number: $i"
done
```

---

_Using Brace Expansion_

```bash
for i in {1..5}; do
  echo "Number: $i"
done
```

**Advantage**: Brace expansion is built into Bash, making it faster and more efficient.

////
//// tab | Variable Range Iteration

_Using `seq`_

```bash
start=1
end=5
for i in $(seq $start $end); do
  echo "Number: $i"
done
```

---

_Using C-style `for` Loop_

```bash
start=1
end=5
for ((i=start; i<=end; i++)); do
  echo "Number: $i"
done
```

**Advantage**: The C-style `for` loop is more flexible and doesn't require external commands.

////
//// tab | Step Values

_Using `seq`_

```bash
for i in $(seq 1 2 10); do
  echo "Number: $i"
done
```

---

_Using C-style `for` Loop_

```bash
for ((i=1; i<=10; i+=2)); do
  echo "Number: $i"
done
```

**Advantage**: The C-style `for` loop allows for easy manipulation of step values directly within Bash.

////
///

### Advantages of Builtins

- **Enhanced Performance**: Builtins execute faster as they are processed internally by Bash, avoiding the overhead of launching external processes.
- **Greater Portability**: Builtins are universally supported across systems running Bash, ensuring consistent behavior and compatibility.
- **Reduced Complexity**: Scripts become simpler and more readable when using builtins, enhancing both maintainability and readability.

## Command Substitution

Command substitution allows for the capturing of command outputs and use it as part of another command or assignment. Bash provides two syntaxes for command substitution: `$(...)` and backticks (`` `...` ``).

/// admonition | Guidelines
    type: tip

- **Use `$(...)`:** Always use the `$(...)` syntax for command substitution over backticks.
- **Avoid Backticks**: Due to readability and nesting issues, avoid using backticks for command substitution.

///

/// details | Examples
//// tab | Basic Command Substitution

_Using backticks_

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

**Advantage**: The `$(...)` syntax is visually clearer and more readable.

////
//// tab | Nesting Command Substitution

_Using backticks_

```bash
date_and_users=`echo "Date: \`date\` - Users: \`who | wc -l\`"`
echo $date_and_users
```

---

_Using `$(...)`_

```bash
date_and_users=$(echo "Date: $(date) - Users: $(who | wc -l)")
echo $date_and_users
```

**Advantage**: Nesting is more straightforward with `$(...)`, avoiding the awkwardness and readability issues of using multiple backticks.

////
///

### Advantages of `$(...)` Over Backticks

- **Improved Readability**: The `$(...)` format is visually clearer, making scripts easier to read and understand, particularly when commands get complex.
- **Easier Nesting**: Facilitates nesting multiple commands without the syntactic awkwardness associated with backticks.
- **Enhanced Safety**: The `$(...)` syntax is more robust and less prone to errors, especially when dealing with complex command substitutions.

## Arithmetic Operations

Arithmetic operations are essential in Bash scripting for performing mathematical calculations and comparisons. Bash provides multiple constructs for arithmetic operations, including `let`, `$((...))`, and `((...))`.

/// admonition | Guidelines
    type: tip

- **Prefer `$((...))` and `((...))`**: Always use `$((...))` for arithmetic expansions and `((...))` for conditional arithmetic expressions.
- **Avoid Using `let`**: Due to its potential for causing unintended command execution and its less intuitive syntax, avoid using `let`.

///

/// details | Examples
//// tab | Basic Arithmetic

_Using `let`:_

```bash
let result=1+2
echo "Result: $result"
```

---

_Using `$((...))`:_

```bash
result=$((1 + 2))
echo "Result: $result"
```

**Advantage**: `$((...))` is more intuitive and clearly indicates arithmetic operation.

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

**Advantage**: `((...))` offers a cleaner and more readable way to handle conditional arithmetic expressions.

////
///

### Advantages of `((...))` and `$((...))`

- **Clarity**: Both `((...))` and `$((...))` clearly delineate arithmetic expressions within scripts, making them easy to identify and understand.
- **Simplicity**: These constructs are natively supported by Bash, offering a streamlined and intuitive approach to handling arithmetic.
- **Increased Safety**: These methods specifically evaluate arithmetic without the risk of executing other commands, unlike `let`, which can interpret expressions as commands if not carefully handled.

## Parameter Expansion

Parameter expansion is a powerful feature in Bash that allows you to manipulate variables and perform string operations directly within the shell. Bash provides a wide range of parameter expansion options, including substring extraction, string replacement, and length calculation.

/// admonition | Guidelines
    type: tip

- **Prefer Parameter Expansion for String Manipulation**: Utilize parameter expansion to handle string manipulations whenever possible. This approach is not only more efficient but also reduces script complexity and dependencies.
- **Avoid External String Manipulation Tools When Unnecessary**: External tools like `sed` and `awk` are powerful but often overkill for simple string manipulations that can be efficiently handled by parameter expansion.

///

/// details | Examples
//// tab | Extracting Substrings

_Using `sed`_

```bash
string="Hello, World!"
substring=$(echo $string | sed 's/Hello,//')
echo $substring
```

---

_Using Parameter Expansion_

```bash
string="Hello, World!"
substring=${string/Hello,/}
echo $substring
```

**Advantage**: Parameter expansion is more efficient and avoids the overhead of invoking `sed`.

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

**Advantage**: Parameter expansion is simpler and more efficient for removing a suffix.

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

**Advantage**: Parameter expansion can directly compute the length of a string without invoking an external command.

////
///

### Advantages of Parameter Expansion

- **Increased Efficiency**: Performs operations within the shell, reducing the overhead associated with initiating external processes.
- **Streamlined Scripting**: Keeps string manipulations inline and shell-native, simplifying the script's logic.
- **Enhanced Portability**: Improves script portability across different Unix-like systems by avoiding dependency on external tools, which may vary in availability or functionality.

## Avoiding Parsing `ls`

Parsing the output of `ls` is a common practice in Bash scripting to list files and directories. However, using `ls` in scripts can lead to issues related to word splitting, globbing, and security vulnerabilities.

/// admonition | Guidelines
    type: tip

- **Prefer Bash Globing**: Always use Bash globing patterns like `*` to list files within a script.
- **Avoid Using `ls`**: Due to the potential for error and security vulnerabilities, avoid using `ls` in loops or where filename interpretation is critical.

///

/// details | Examples
//// tab | Listing Files in a Directory

_Using `ls` in a loop_

```bash
for file in $(ls /path/to/dir); do
  echo "Processing $file"
done
```

---

_Using Bash Globing_

```bash
for file in /path/to/dir/*; do
  echo "Processing $(basename "$file")"
done
```

**Advantage**: Bash globing is more reliable and handles filenames with spaces and special characters correctly.

////
//// tab | Counting Files

_Using `ls` and `wc`_

```bash
for file in $(ls /path/to/dir); do
  echo "File: $file"
done
```

---

_Using Bash Globing_

```bash
for file in /path/to/dir/*; do
  echo "File: $(basename "$file")"
done
```

**Advantage**: Bash globing handles filenames with spaces correctly, ensuring reliable script execution.

////
///

### Reasons to Use Bash Globing Over `ls`

- **Enhanced Reliability**: Parsing `ls` can fail in unexpected ways if filenames contain special characters or spaces.
- **Improved Security**: Scripts that parse `ls` are susceptible to security risks, especially if filenames are designed maliciously.
- **Simplicity**: Using Bash globing (`*`) simplifies scripts by directly and safely accessing filenames without external commands.

## Element Collections

Element collections are a common feature in Bash scripting, allowing you to manage groups of items such as filenames, user inputs, or configuration values. Bash two primary methods for handling collections: arrays and space-separated strings.

/// admonition | Guidelines
    type: tip

- **Prefer Arrays for Collections**: Always opt for arrays when managing collections of elements. This approach avoids common pitfalls associated with space-separated strings, such as word splitting and unintentional globing.
- **Utilize Arrays for Command Arguments**: Leverage arrays to pass multiple arguments to commands. This method simplifies command syntax and enhances script readability and reliability.

///

/// details | Examples
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

**Advantage**: Arrays provide straightforward syntax for adding elements, improving readability and maintenance.

////
///

### Benefits of Using Arrays

- **Clarity and Safety**: Arrays prevent errors related to word splitting and glob expansion that can occur with space-separated strings.
- **Flexibility**: Arrays allow for straightforward manipulation and access to individual elements, as well as simpler expansion in commands that accept multiple arguments.
- **Ease of Maintenance**: Code utilizing arrays is generally clearer and easier to maintain, particularly as script complexity increases.

## Parsing Strings

Parsing strings is a common task in Bash scripting, allowing you to extract specific elements or values from a string. Bash provides various methods for parsing strings, including `read`, `awk`, and `sed`.

/// admonition | Guidelines
    type: tip

- **Use `read`**: Employ `read` to safely and efficiently parse user inputs and other data directly into variables.
- **Customize with IFS**: Adjust the Internal Field Separator (IFS) as needed when using `read` to ensure that inputs are split according to your specific requirements, enhancing the flexibility and accuracy of data parsing.

///

/// details | Examples
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

**Advantage**: `read` directly parses the input into variables, reducing the complexity and improving readability.

////
///

### Advantages of Using `read`:

- **Efficiency**: `read` operates within the shell, avoiding needing slower, resource-intensive external commands.
- **Security**: By parsing directly into Bash variables, `read` minimizes the risks associated with external command outputs, such as injection attacks.
- **Simplicity**: Offers an easy-to-understand syntax for directly parsing complex data structures.
- **Portability**: `read` is a Bash builtin, ensuring consistent behavior across different systems and environments.

# Bash Style Guide

[![Project Tracker](https://img.shields.io/badge/repo%20status-Project%20Tracker-lightgrey)](https://wiki.hthompson.dev/en/project-tracker)

This style guide outlines how to write bash scripts with a style that makes them safe and predictable. Most of this guide is based on [this wiki](http://mywiki.wooledge.org), more specifically, this page: http://mywiki.wooledge.org/BashGuide/Practices

If anything is not mentioned explicitly in this guide, it defaults to matching whatever is outlined in the wiki.

You can fork this style guide on GitHub: https://github.com/StrangeRanger/bash-style-guide

## Preface

This guide will try to be as objective as possible, providing reasoning for why individual decisions were made. For purely aesthetic choices (and may not be universally agreeable), refer to the `Aesthetics` section below.

## Aesthetics

### Indentation

Consistent indentation is crucial for ensuring the readability and maintainability of code. In Bash scripting, using spaces for indentation is preferred over tabs due to its consistency across various editing environments and platforms.

#### Why Use Spaces?

- **Uniform Appearance**: Spaces ensure that the code looks the same in every editor and environment, avoiding issues where tabs may render differently depending on settings.
- **Avoid Misalignment**: Tabs can lead to misaligned code blocks when viewed in different text editors or IDEs, as the width of a tab can vary, whereas spaces provide a consistent width.

#### Recommended Practice

- **Indent Size**: Use four spaces per level of indentation. This size is generally considered a good balance between readability and conserving horizontal space.
- **Consistency**: Stick to one method of indentation throughout the entire script or project to maintain uniformity.

#### Example

```bash
# Correct usage with spaces.
if [[ var = true ]]; then
    echo "true"
fi
```

### Line Length (COME BACK TO AND EDIT REGARDING NEW LINE IF IS READABLE)

Maintaining an optimal line length in Bash scripts is essential for readability, maintainability, and compatibility across different environments. A maximum line width of 88 characters is recommended, aligning with modern coding standards that balance readability with practical code editing and viewing.

#### Reasons for Limiting Line Length

1. **Readability**: Shorter lines reduce the need for horizontal scrolling, making the code easier to read on various devices and screens.
2. **Maintainability**: It is easier to review and debug code that fits within a standard visual range without navigating back and forth across long lines.
3. **Compatibility**: Ensures that the code displays well in a wide range of development environments and tools, including code review platforms and integrated development environments (IDEs).

#### Exceptions to the 88-Character Rule

While the 88-character limit is generally a good practice, there are situations where exceptions are necessary to maintain the clarity and functionality of the code:

1. **Long Strings or URLs**: When a line contains a long string, URL, or command that loses clarity or functionality if broken into multiple lines, it is permissible to exceed the standard line length.
2. **Complex Expressions**: Complex expressions or commands that would have their logical flow disrupted by splitting across multiple lines should be kept intact even if they exceed the standard width.
3. **Configuration Files**: In scripts dealing with configuration data, sometimes parameters and their values are more understandable when placed on a single line, even if this extends beyond the typical length limit.

#### Example

```bash
# Within 88 characters.
if [[ -n $file && -x $script ]]; then
    echo "File and script are available."
fi

# Exceeding 88 characters, but is continued on the next line,
grep -E "pattern1|pattern2|pattern3|pattern4" \
	      /var/log/very_long_directory_name/filename.log

# Configuration file exception
config_option="ThisIsAReallyLongConfigurationParameter = 'This is a very long value that makes sense to keep on one line'"
```

### Semicolons in Bash Commands

Semicolons in Bash serve as command separators, allowing multiple commands to be executed sequentially on a single line. However, when not required for this purpose, their use should be minimized to maintain code cleanliness and prevent syntactic confusion.

#### Why Avoid Unnecessary Semicolons?

- **Code Clarity**: Removing unnecessary semicolons helps keep the script clean and readable. It makes the script easier to follow by reducing visual clutter.
- **Error Prevention**: Extra semicolons can introduce errors or unexpected behaviors, especially for those new to scripting who might misinterpret their purpose.

#### Example

```bash
# Incorrect usage with unnecessary semicolons.
name="dave";
echo "hello $name";

# Correct usage without semicolons when not needed.
name="dave"
echo "hello $name"
```

### Function Declaration

In Bash scripting, the way functions are declared can significantly affect the readability, maintainability, and scope management of the code. It is recommended to use a consistent syntax for function declarations and to manage variable scope carefully within those functions.

#### Preferred Syntax for Function Declarations

- **Simplicity and Consistency**: Declare functions with the name followed by parentheses and curly braces. This approach is widely recognized and aligns with other programming languages, making it easier for developers to understand and use.

#### Why Avoid the `function` Keyword?

- **Portability**: The `function` keyword is not POSIX compliant, which can lead to compatibility issues in environments that strictly adhere to POSIX standards.
- **Consistency**: Using the simpler `name()` format ensures consistency across different scripts and reduces the complexity of the syntax.

#### Managing Variable Scope

- **Local Variables**: Declare variables within functions as `local` whenever possible. This practice confines the variable's scope to the function, preventing it from affecting the global state or interfering with other parts of the script.

#### Examples:

```bash
# Incorrect usage with the `function` keyword and global variable:
function foo {
    i=foo  # This variable is global, which can lead to unintended side effects.
}

# Correct usage with preferred syntax and local variable:
foo() {
    local i=foo  # This variable is local to 'foo', minimizing side effects.
}
```

### Formatting Block Statements

Proper formatting of control structures such as `if`, `for`, and `while` is crucial for maintaining the readability and clarity of Bash scripts. The placement of `then` and `do` on the same line as their respective control statement and consistently formatting block closures can greatly enhance the understandability of code.

#### Importance of Consistent Block Formatting

- **Readability**: Placing `then` and `do` on the same line as the control statement makes the structure of the code immediately apparent, reducing confusion and increasing readability.
- **Maintainability**: Consistent block formatting helps other developers understand the logic of the script more easily, facilitating quicker debugging and modifications.

#### Formatting Guidelines

1. **Inline `then` and `do`**: Always place `then` for `if` statements and `do` for `for` or `while` loops on the same line as the opening statement.
2. **New Line for Closing Keywords**: Always place `fi` for `if` statements and `done` for loops on a new line to clearly indicate the end of the block.

#### Examples:

```bash
# Incorrect usage:
if true
then
    echo "This is hard to trace."
fi

# Also incorrect:
true && {
    echo "This syntax is less clear."
}

# Correct usage:
if true; then
    echo "This is easy to follow."
fi
```

### Single Line Block Statements

While Bash scripts typically benefit from multi-line formatting for clarity, single-line block statements can be used effectively for simple conditional or loop executions. However, it is crucial to maintain readability and structural integrity by adhering to best practices for closing such statements.

#### Rationale for Careful Use of Single-Line Statements

- **Clarity**: Keeping block statements clear and concise aids in readability, especially for simpler conditional checks or loops.
- **Maintainability**: Properly formatted single-line statements help maintain the script’s structure, making it easier to understand and modify by others or in future revisions.

#### Best Practices for Single-Line Statements

1. **Closing Keywords on New Line**: Even in single-line statements, place the closing keyword (`fi` for `if` statements, and `done` for `while` and `for` loops) on a new line. This maintains a clean separation between the start and end of the block, aiding in visual clarity and preventing structural errors.
2. **Character Limit Considerations**: Ensure that single-line statements do not exceed the preferred line length (e.g., 88 characters), to avoid horizontal scrolling in most editors and maintain overall readability.

#### Examples:

```bash
# Incorrect usage:
if [[ -f $file_name ]]; then echo "$file_name exists!"; fi

# Also incorrect:
if [[ -f $file_name ]]; then echo "$file_name exists!"; echo "Done"
fi

# Correct usage:
if [[ -f $file_name ]]; then echo "$file_name exists!"
fi
```

### Vertical Spacing

Limit the use of consecutive newline characters to no more than three, which translates to allowing no more than two blank lines in a row within your scripts. This spacing rule helps balance separating sections of code for better readability and preventing excessive whitespace that can make the script longer and harder to navigate.

#### Guidelines:

- Use one blank line to separate logical blocks of code or functions for quick visual parsing.
- Use up to two blank lines sparingly to denote new sections or distinct groups of logic within your script.

#### Examples:

```bash
# Correct usage:
echo "Start of the script"

echo "First operation completed"

echo "Second operation completed"


echo "End of the script"
```

### Comments

#### General Comment Guidelines

Comments are a vital part of any script, not only for explaining what the script or specific parts of it do, but also for maintaining its usability and readability over time. Good commenting practices can transform a script from being cryptic and hard-to-follow to being easily understandable by new developers or future maintainers.

##### General Commenting Guidelines

- **Capitalization**: Start each comment with a capital letter unless it begins with a variable that is not capitalized.
- **Punctuation**: End each comment with a period to maintain consistency and readability.
- **Spacing**: Include two spaces between the end of the code and the start of the inline comment. This helps to visually separate the code from comments.

##### Examples of Good Commenting Practices:

```bash
# This line assigns the string "True" to var and demonstrates proper comment format.
var=$(echo "True")  # This prints "True".  (Two spaces between ')' and '#')

# Simple variable assignment with a clear, aligned comment.
box="Box"           # This is a box.       (Aligned with the comment above for better readability)
```

#### Functions

Well-documented functions are crucial for maintaining high code quality and ensuring that scripts are accessible and maintainable. Detailed documentation for each function helps clarify its purpose, usage, and the parameters it accepts, which is essential for both current developers and future maintainers.

##### Guidelines for Documenting Functions

- **Function Name**: Clearly state the function's name at the beginning of the documentation to immediately inform the reader about which function they are reviewing.
- **Purpose**: Provide a brief description of what the function does. This description should inform the reader of the function's overall purpose and its role within the script.
- **Parameters**: List each parameter the function expects, specifying whether it is required or optional. For optional parameters, include the default value.

##### Documentation Format

Ensure that the documentation is easy to read and systematically organized. Use a consistent comment block style to define the function's name, purpose, and parameters.

##### Example of Function Documentation:

```bash
####
# Function name: process_files
# Purpose:
#   This function processes files given as parameters and logs results. It is designed
#   to handle a variable number of input files and demonstrate proper error handling.
#
# Parameters:
#   $1: input_file (Required)
#       Description: Path to the file that needs processing.
#
#   $2: log_file (Required)
#       Description: File where the processing logs are stored.
#
#   $3: debug_mode (Optional, Default: false)
#       Description: If set to true, the function will perform debugging operations.
process_files() {
    local input_file="$1"
    local log_file="$2"
    local debug_mode="${3:-false}"

    ...
}
```

#### Pound Signs in Comments

Using a systematic approach to commenting with pound signs (`#`) in Bash scripts enhances readability and maintainability by clearly delineating the scope and intent of comments. This practice helps both the script’s author and others who may work on the script in the future to quickly understand the purpose and functionality of various code sections.

##### Guidelines for Using Pound Signs

- **Single Pound Sign (`#`)**: Use a single pound sign for line-specific comments that explain the purpose of a particular line or command within the script. This is most commonly used for inline comments or brief explanations.

- **Double Pound Signs (`##`)**: Double pound signs are used for block-specific comments. These comments cover several lines of code, describing the functionality of a code block, such as a loop or a conditional sequence.

- **Triple Pound Signs (`###`)**: Reserved for important structural comments that apply to an entire section or complex block of code, such as an entire function or a large conditional structure. These comments should summarize the overall purpose or behavior of the code section.

##### Examples of Comment Usage:

```bash
# Single Pound Sign for simple explanations:
# Contains the raw URL link to this 'README.md'.
export _RAW_URL="https://raw.githubusercontent.com/StrangeRanger/bash-style-guide/main/README.md"

# Checks if the file exists.
if [[ -f $file_path ]]; then
    echo "File exists."
fi

## Double Pound Signs for describing a block of operations:
## Initialize environment variables for color output.
export _YELLOW="$(printf '\033[1;33m')"
export _GREEN="$(printf '\033[0;32m')"
export _CYAN="$(printf '\033[0;36m')"
export _RED="$(printf '\033[1;31m')"

## Process each file listed.
for file in *.txt; do
    echo "Processing $file"
done

### Triple Pound Signs for major sections:
### Handle user input and provide feedback.
if [[ -z $1 ]]; then
    echo "No argument provided."
elif [[ -f $1 ]]; then
    echo "File found."
else
    echo "Condition not met."
fi
```

## Bashisms

As a reminder, this style guide is for bash. This means when given a choice, always prefer bash builtins or keywords instead of external commands or `sh(1)` syntax.

### Conditional Tests

In Bash scripting, it is recommended to use the `[[ ... ]]` syntax for conditional tests instead of the older `[ ... ]` or `test` syntax. The `[[ ... ]]` construct offers several advantages, such as more flexible string handling and pattern matching capabilities. It also prevents script errors in cases where variables may be unassigned or contain spaces.

#### Benefits of `[[ ... ]]` Over `[ ... ]` and `test`

- **Enhanced Features**: Supports additional string operations and pattern matching.
- **Safety**: Does not split variables on spaces, which can lead to more robust scripts.
- **Flexibility**: Easier to read, especially in complex conditional expressions.

#### Examples:

```bash
# Incorrect usage with 'test':
test -d /etc

# Incorrect usage with single brackets:
[ -d /etc ]

# Correct usage with double brackets:
[[ -d /etc ]]
```

### Sequence Iteration

For iterating over a sequence of numbers in Bash scripts, it is best to utilize Bash builtins like brace expansion `{1..5}` and the C-style `for` loop `for ((i = 0; i < n; i++))`. These methods are preferred over external commands like `seq`, as they are built directly into Bash and do not require forking a new process, leading to better performance and lower resource usage.

#### Advantages of Builtins Over `seq`

- **Performance**: Builtins execute faster as they are processed internally by the shell without launching an external process.
- **Portability**: Builtins work consistently across all platforms that run Bash, whereas `seq` may not be installed by default on some systems.
- **Simplicity**: Using builtins can reduce the complexity of your scripts, making them easier to read and maintain.

#### Examples:

```bash
# Incorrect usage with 'seq':
for f in $(seq 1 5); do
    echo $f
done

# Incorrect usage with 'seq' for variable limits:
for f in $(seq 1 "$n"); do
    echo $f
done

# Correct usage with brace expansion:
for f in {1..5}; do
    echo $f
done

# Correct usage with C-style for loop:
for ((i = 0; i < n; i++)); do
    echo $i
done
```

### Command Substitution

In Bash scripting, command substitution allows you to capture the output of a command and store it in a variable. The recommended syntax for command substitution is `$(...)` rather than the older backticks `` `...` ``. The `$(...)` syntax is preferred for several reasons including improved readability, easier nesting, and better compatibility with other shell features.

#### Advantages of `$(...)` Over Backticks

- **Readability**: The `$(...)` syntax is visually clearer, especially in complex commands or scripts.
- **Nesting**: It simplifies the process of nesting commands within commands, which can be awkward with backticks.
- **Compatibility**: Interacts better with double quotes and other special characters.

#### Examples:

```bash
# Incorrect usage with backticks:
foo=`date`

# Correct usage with $(...):
foo=$(date)
```

### Arithmetic Operations

In Bash scripts, arithmetic operations should ideally be performed using the `((...))` syntax for conditional expressions and `$((...))` for arithmetic expansion. These built-in mechanisms are preferred over the older `let` command for several reasons, including clarity, simplicity, and reduced error potential.

#### Advantages of `((...))` and `$((...))`

- **Clarity**: Both `((...))` and `$((...))` provide clear visibility of arithmetic operations within the script.
- **Simplicity**: These constructs integrate seamlessly with Bash, avoiding the verbosity and syntactic complexity of alternatives like `let`.
- **Safety**: Unlike `let`, which evaluates the expression as a command, `((...))` and `$((...))` treat expressions as arithmetic evaluations, reducing the risk of executing unintended commands.

#### Examples:

```bash
# Incorrect usage with 'let':
a=5
b=4
if [[ $a -gt $b ]]; then
    echo "a is greater than b"
fi

# Correct usage with arithmetic expansion and evaluation:
a=5
b=4
if ((a > b)); then
    echo "a is greater than b"
fi
```

### Parameter Expansion

In Bash scripting, parameter expansion offers a powerful tool for modifying and manipulating string variables directly within the shell, without the need for external commands like `echo`, `sed`, `awk`, etc. This built-in feature provides a more efficient and native way to handle string operations, reducing overhead and improving script performance.

#### Advantages of Parameter Expansion

- **Efficiency**: Executes operations within the shell itself, avoiding the overhead of spawning new processes.
- **Simplicity**: Simplifies scripts by keeping manipulations inline and shell-native without relying on external tools.
- **Portability**: Ensures better script portability across different Unix-like systems where external tools might differ in availability or behavior.

#### Examples:

```bash
# Incorrect usage with external commands:
name="hunter"
prog=$(basename "$0")
no_numbers=$(echo "$name" | sed -e 's/[0-9]//g')

# Correct usage with parameter expansion:
name="hunter"
prog=${0##*/}
no_numbers=${name//[0-9]/}
```

### Avoiding Parsing `ls`

In Bash scripting, directly parsing the output of `ls` to list files can lead to potential issues, especially when filenames contain spaces, newlines, or other special characters. Instead, it is recommended to use Bash's built-in globing feature, which is safer and more robust for looping through files in a directory.

#### Reasons to Avoid Parsing `ls`

- **Reliability**: Parsing `ls` can break in unexpected ways when filenames contain special characters or spaces.
- **Security**: Using `ls` in scripts can lead to security vulnerabilities, especially when filenames are crafted maliciously.
- **Simplicity**: Bash globing (`*`) directly provides a simpler and more direct method of accessing filenames.

#### Examples:

```bash
# Incorrect and potentially unsafe usage with `ls`:
for f in $(ls); do
    echo "Processing $f"
done

# Correct and safe usage with globing:
for f in *; do
    echo "Processing $f"
done
```

### Using Arrays Over Space-Separated Strings

In Bash scripts, using arrays to manage collections of elements is highly recommended over using space-separated strings. Arrays provide a more structured, flexible, and robust method for handling lists of items, especially when elements might contain spaces or special characters.

#### Benefits of Using Arrays:

- **Clarity and Safety**: Arrays prevent errors related to word splitting and globing, which can occur with space-separated strings.
- **Flexibility**: Arrays allow for easier manipulation and access to individual elements, as well as simpler expansion in commands that accept multiple arguments.
- **Maintainability**: Code that utilizes arrays is generally easier to read and maintain, especially as script complexity increases.

#### Examples:

```bash
# Incorrect: Using a space-separated string:
modules="json httpserver jshint"
for module in $modules; do
    npm install -g "$module"
done

# Correct: Using an array:
modules=(json httpserver jshint)
for module in "${modules[@]}"; do
    npm install -g "$module"
done

# More efficient usage if command supports multiple arguments:
npm install -g "${modules[@]}"
```

### Using `read` for Parsing Strings

The `read` command in Bash is a powerful builtin that facilitates direct parsing of strings and user input without needing to spawn external processes. This can lead to scripts that are not only faster but also more secure and easier to maintain.

#### Advantages of Using `read`:

- **Efficiency**: Avoids the overhead of launching external commands, which can be slow and resource-intensive.
- **Security**: Reduces the risk of injection attacks that can occur when improperly handling external command outputs.
- **Simplicity**: Provides a straightforward syntax for parsing complex data structures directly into Bash variables.

#### Example Usage:

```bash
# Here's how to parse a fully qualified domain name (FQDN) using `read`:
fqdn="computer1.daveeddy.com"

# Using IFS (Internal Field Separator) to split the string by periods:
IFS=. read -r hostname domain tld <<< "$fqdn"
echo "$hostname is in $domain.$tld"
# Output: "computer1 is in daveeddy.com"
```

## External Commands

### Portability of Bash Scripts

Bash scripts often rely on external tools such as `awk`, `sed`, and `grep`. However, GNU-specific options for these tools might not be available on all systems, such as on BSD or macOS, which use different versions of these utilities. To ensure maximum portability of your Bash scripts across different environments, avoid using GNU-specific options or features whenever possible.

#### Best Practices:

- **Portability**: Use the most basic and widely supported options in commands to ensure your scripts run on any Unix-like system.
- **Utilizing Builtins**: Bash provides powerful builtins that often negate the need for external commands, especially for tasks like string manipulation or file processing.

### Avoiding Unnecessary Use of Cat (UUOC)

A common misuse of external commands is the unnecessary use of `cat` for piping data into filters like `grep`. This practice is colloquially known as "Useless Use of Cat" (UUOC). Instead, use direct redirection or pass filenames directly to commands that support it to streamline command execution and improve script performance.

#### Examples:

```bash
# Inefficient use of 'cat':
cat file | grep foo  # This unnecessarily invokes 'cat'.

# Efficient alternatives:
grep foo < file      # Redirects file directly into 'grep'.
grep foo file        # Passes the filename directly to 'grep'.
```

## Style

### Guidelines for Using Quotes in Bash

Quoting in Bash scripting is crucial for controlling how strings and variables are handled. Use double quotes in most cases to ensure that variable values and command substitutions are correctly expanded without being split or misinterpreted by the shell.

#### When to Use Double Quotes

- **Variable Expansion**: Double quotes allow for the expansion of variables within the string.
- **Command Substitution**: Use double quotes when your string includes command substitutions to ensure the output is handled correctly.

##### Examples:

```bash
# Correct:
foo="$USER contains your username"
bar="You are a user"
bar="You are $USER"
bar="\$USER = $USER"

# Incorrect:
foo='Hello World'  # Should be in double quotes unless intentional.
```

#### Exceptions to Using Double Quotes

- **No Expansion Desired**: Use single quotes when you want the string to be taken literally, especially with utilities like `sed` or `awk` where the expansion of Bash variables might cause issues.
- **Empty or Non-existent Variables**: If you want the script to ignore empty or undefined variables rather than leaving an empty string, omit the quotes.

##### Examples:

????

#### Handling Variables:

All variables should be quoted to prevent word splitting and globbing, except in the following cases:

- **Within Double Brackets**: Bash handles word splitting automatically in double brackets [[]], so quotes are not necessary.
- **When Ignoring Empty Variables**: Unquoted variables are ignored if they are empty or undefined, which can be useful in conditional expressions.

##### Example

```bash
foo="hello world"

# No quotes needed here:
if [[ -n $foo ]]; then
    echo "Variable foo is not empty."
fi

# Quotes needed to prevent word splitting:
echo "$foo"
```

### Guidelines for Declaring Variables

Variable declaration in Bash should follow specific naming conventions and usage rules to ensure scripts are clear, maintainable, and consistent. These rules differentiate between local variables and those intended for export.

#### Local Variables

- **Naming Conventions**: Use lowercase for local variable names. This helps differentiate them from environment variables which are typically uppercase.
- **Usage**: Use `local` when declaring variables within functions to limit their scope and prevent conflicts with global variables.

#### Exported Variables

- **Naming Conventions**: Exported variables should be in uppercase, with an underscore `_` prefix and underscores separating words. This convention makes them easily identifiable and distinguishes them from local variables.
- **Declaration**: Use `export` directly with the variable declaration to clearly indicate that the variable is intended for the environment outside the script.

#### Do Not Use

- **`let`**: Avoid using `let` for arithmetic operations. Instead, use the `((...))` syntax for clarity and consistency.
- **`readonly`**: Generally, avoid `readonly` unless you need to guarantee that the variable's value cannot be changed.
- **`declare`**: Reserve `declare` primarily for associative arrays. For standard variables, direct assignment is preferred unless scoping issues require `declare`.

#### Examples:

```bash
# Incorrect usage:
declare -i foo=5
let foo++
readonly bar="something"
FOOBAR=baz
export food=5
export food_cart=5

# Correct usage:
i=5
((i++))
bar="something"
foobar=baz
export _FOOD=5
export _FOOD_CART=5
```

### Best Practices for Shebang Lines in Bash Scripts

The shebang line at the start of a Bash script determines which shell executes the script, crucial for ensuring the script runs correctly across different environments. Choosing the correct shebang line depends on the script's intended compatibility and the environments it will run in.

#### General Guidelines

- **Universal Compatibility**: Use `#!/usr/bin/env bash` for scripts intended to run on multiple operating systems, including BSD, macOS, and Linux. This method searches the user's `PATH` to locate and use the first instance of Bash, which accommodates systems where Bash may be installed in a non-standard location.
- **Linux-specific Scripts**: Use `#!/bin/bash` for scripts that are specifically designed for Linux environments where Bash's location is consistent at `/bin/bash`.

#### Why the Difference Matters:

- **BSD and macOS**: On systems like BSD and macOS, Bash is not always in the same location as on Linux. macOS, for example, includes an older version of Bash by default, and many users install an updated version via Homebrew, which does not replace the system default in `/bin/bash`.
- **Path Flexibility**: Using `#!/usr/bin/env bash` provides flexibility, ensuring that scripts use the version of Bash installed in the user's environment, which can be crucial for scripts that rely on features found in newer versions of Bash.

#### Examples:

```bash
# For universal compatibility:
#!/usr/bin/env bash

# Specifically for Linux environments:
#!/bin/bash
```

## Handling Errors Properly

Error checking is crucial in Bash scripting to ensure that your scripts behave as expected even when encountering runtime errors. Proper error handling techniques can prevent scripts from causing unintended effects.

### Checking Command Success

Use conditional checks to verify that commands have executed successfully before proceeding. This is particularly important for commands like `cd`, where failure to change directories could lead to incorrect script behavior.

#### Example:

```bash
# Incorrect: Does not handle potential failure:
cd /some/path
rm file

# Correct: Checks for failure before proceeding:
cd /some/path || exit
rm file
```

### Overview of `trap` in Bash

The `trap` command in Bash is a powerful tool used to specify commands that will execute when the shell receives specific signals or when other specific system events occur. This feature is particularly useful for performing cleanup tasks or handling graceful shutdowns in scripts.

#### Importance of `trap`

- **Resource Management**: Ensures that temporary files, processes, or other system resources are properly cleaned up or released, even if the script exits unexpectedly.
- **Robustness**: Increases the robustness of scripts by allowing them to respond to interrupts and other signals in a controlled manner.

#### Common Signals to Trap

- **SIGINT**: This signal is sent to a process by its controlling terminal when a user wishes to interrupt the process. Commonly invoked via Ctrl+C.
- **SIGTERM**: The default signal sent by the `kill` command, indicating that a process should terminate gracefully.
- **EXIT**: A pseudo-signal that is not a true system signal, but can be trapped to execute a command when the script exits for any reason, whether normally or due to a signal.

#### Example Usage:

```bash
# Setting up a trap for cleanup operations
trap 'rm -f /tmp/my_temp_file; echo "Cleanup complete."' EXIT

# Example of trapping interruptions
trap 'echo "Script interrupted."; exit' SIGINT

# More complex trap handling
trap cleanup SIGINT SIGTERM
cleanup() {
    echo "Performing cleanup tasks..."
    # Insert cleanup commands here
    exit
}
```

### Understanding `set -e` in Bash Scripting

The `set -e` option in Bash scripts causes the script to exit immediately if any command within the script exits with a non-zero status, unless the command that fails is part of an until or while loop, part of an if statement, part of a `&&` or `||` list, or if the command’s return status is being inverted using `!`. This feature is similar to the error handling in programming languages like C, where operations may fail without causing the program to stop, unless explicitly handled.

#### Why Caution with `set -e`?

- **Unintended Exits**: Scripts with `set -e` may exit in places you didn’t anticipate if a command fails, which can be problematic for scripts that handle errors internally or expect possible command failures.
- **Complex Conditions**: In complex scripts, the exact point of failure might be obscured by `set -e`, making debugging more difficult, especially in multi-part commands or pipelines.

#### Example Situations:

```bash
# Example where `set -e` might be problematic:
set -e
command_that_might_fail || handle_failure

# Without `set -e`, you can handle errors more flexibly:
command_that_might_fail || {
    echo "Handling failure explicitly..."
    # Additional commands to
```

### The Risks of Using `eval` in Bash

The `eval` command in Bash scripts executes arguments as a Bash command, combining them into a single command before execution. This functionality poses significant security risks, particularly when the content that `eval` executes is derived from user input or untrusted sources. Due to these risks, it is strongly recommended to avoid using `eval` in Bash scripts.

#### Why Avoid `eval`?

- **Security Vulnerabilities**: `eval` can execute arbitrary code, which makes it a potent vector for code injection attacks. If any part of the `eval` statement can be influenced by an external user, it could potentially lead to malicious code execution.
- **Complexity and Debugging Difficulties**: Scripts using `eval` are often more difficult to read and debug. `eval` can obscure the flow of execution and complicate the tracing of errors because it executes dynamically generated code.

#### Examples of `eval` Usage and Alternatives:

```bash
# Potentially dangerous use of `eval`:
command="rm -f $file"
eval "$command"  # Dangerous if `$file` includes malicious input.

# Safer alternative using direct execution:
rm -f "$file"
```

### Redirecting Errors to stderr in Bash

Proper error handling in Bash scripts includes directing error messages to the standard error stream (stderr). This practice helps differentiate normal output from error messages, making it easier for users and other programs to handle outputs correctly.

#### Importance of Redirecting to stderr

- **Clarity**: Keeps error messages separate from standard output, which is crucial for debugging and logging.
- **Usability**: Allows users to redirect errors independently from standard output, which can be particularly useful in command-line utilities or scripts used in larger pipelines.

#### How to Redirect Errors

To redirect error messages to stderr, use the redirection operator `>&2`. This ensures that the message is sent to the stderr stream, which is typically used for logging errors or diagnostic messages.

#### Example:

```bash
# Redirecting an error message to stderr:
echo "Failed to execute file" >&2
```

## Common Mistakes

### Understanding Quoting and Braces in Bash

In Bash scripting, how you handle variable expansion can significantly affect the behavior of your scripts, particularly in terms of word-splitting and preserving whitespace. Properly using quotes and braces (`{}`) ensures that variables are expanded as intended without unwanted side effects.

#### Importance of Quoting Variables

- **Preserving Whitespace**: Quotes are crucial for ensuring that all characters within a variable, including whitespace, are treated as a single entity.
- **Preventing Word-Splitting**: Without quotes, Bash splits the expanded variable into multiple words based on the internal field separator (IFS), which typically includes spaces.

#### Using Curly Braces

- **Disambiguation**: Curly braces are useful for clarifying where a variable's name ends and other characters begin. This can be critical in concatenating strings or appending characters directly after a variable.

#### Examples and Comparisons:

```bash
# Demonstrates loss of internal whitespace without quotes:
for f in "1 space" "2  spaces" "3   spaces"; do
    echo ${f}
done
# Output:
# 1 space
# 2 spaces
# 3 spaces

# Preserves whitespace with quotes:
for f in "1 space" "2  spaces" "3   spaces"; do
    echo "$f"
done
# Output:
# 1 space
# 2  spaces
# 3   spaces

# Example of using curly braces for disambiguation:
echo "$HOME is $USERs home directory"  # Incorrect: treats 'USERs' as one variable
echo "$HOME is ${USER}s home directory"  # Correct: clearly separates '$USER' from 's'
```

### Understanding When to Use For vs. While Loops

In Bash scripting, the choice between `for` and `while` loops is crucial for writing efficient and correct scripts. `For` loops are well-suited for iterating over lists and arrays where the size of the data set is known and manageable. In contrast, `while` loops are more appropriate for processing data streams or files line by line, especially when the data size is large or unknown.

#### Limitations of For Loops with Newline Separated Data

Using `for` loops to process newline-separated data, such as files or command outputs, can lead to several issues:

- **Memory Usage**: `for` loops read all data into memory at once, which can be inefficient with large data sets.
- **Incorrect Field Splitting**: Data containing spaces or special characters may be split incorrectly, leading to errors in processing.
- **Reliance on Unquoted Variables**: This can introduce bugs related to word splitting and globbing, which can alter the intended behavior of the script.

#### Recommended Approach with While Loops

`While` loops, particularly with `read`, are better suited for reading data line by line directly from a file or a pipe. This approach is more memory-efficient and avoids issues with field splitting.

#### Example Comparison:

```bash
# Inefficient and potentially problematic for loop:
users=$(awk -F: '{print $1}' /etc/passwd)
for user in $users; do
    echo "user is $user"
done

# Efficient and safe while loop with read:
while IFS=: read -r user _; do
    echo "$user is user"
done < /etc/passwd


## Extra

- http://mywiki.wooledge.org/BashPitfalls

## License

MIT License
```

# Bash Style Guide

[![Project Tracker](https://img.shields.io/badge/repo%20status-Project%20Tracker-lightgrey)](https://wiki.hthompson.dev/en/project-tracker)

This style guide outlines how to write bash scripts with a style that makes them safe and predictable. Most of this guide is based on [this wiki](http://mywiki.wooledge.org), more specifically, this page: http://mywiki.wooledge.org/BashGuide/Practices

If anything is not mentioned explicitly in this guide, it defaults to matching whatever is outlined in the wiki.

You can fork this style guide on GitHub: https://github.com/StrangeRanger/bash-style-guide

## Preface

This guide will try to be as objective as possible, providing reasoning for why individual decisions were made. For purely aesthetic choices (and may not be universally agreeable), refer to the `Aesthetics` section below.

## Aesthetics

### Indentation

Proper indentation is essential for maintaining the readability and maintainability of your Bash scripts. Using spaces for indentation is recommended due to their consistency across various editors and platforms.

#### Why Spaces?

- **Consistency Across Environments**: Spaces render uniformly in all editors, preventing misalignment issues that can occur with tabs, whose width might vary depending on the editor settings.

#### Recommended Practice

- **Indent Size**: Use four spaces per indentation level. This balance between readability and space conservation is effective for Bash scripting.
- **Uniformity**: Maintain consistent indentation methods throughout your scripts to ensure code clarity.

#### Example

**Correct Usage with Spaces**:

```bash
if [[ var = true ]]; then
    echo "true"
fi
```

**Incorrect Usage with Tabs (Potential Misalignment)**:

```bash
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

Semicolons are command separators in Bash, allowing the execution of multiple commands on a single line. While useful, their unnecessary use should be minimized to enhance script clarity and prevent errors.

#### Why Minimize Semicolons?

- **Enhanced Readability**: Omitting unnecessary semicolons reduces visual clutter, making scripts easier to follow and understand.
- **Reduced Error Risk**: Superfluous semicolons can lead to syntactic mistakes and unexpected behavior, complicating the debugging process.

#### #### Examples

**Less Optimal: Unnecessary Semicolons**

```bash
name="dave";
echo "hello $name";
```

**Optimal: Cleaner without Semicolons**

```bash
name="dave"
echo "hello $name"
```

### Function Declaration

In Bash scripting, the function declaration style impacts readability, maintainability, and variable scope management. For consistency and simplicity, a specific syntax for declaring functions is recommended.

#### Preferred Syntax for Function Declarations

- **Uniform Style**: Always declare functions using the name followed by parentheses and curly braces (`name() {}`). This syntax is widely recognized and similar to other programming languages, facilitating easier understanding and usage.

#### Why Avoid the `function` Keyword?

- **Portability**: The `function` keyword is not POSIX compliant, which can lead to compatibility issues in environments that strictly adhere to POSIX standards.
- **Consistency**: Using the simpler `name()` format ensures consistency across different scripts and reduces the complexity of the syntax.

#### Managing Variable Scope

- **Use Local Variables**: Always declare variables as `local` within functions. This limits their scope to the function, preventing interference with the global state or other script parts.

#### Examples:

**Less Optimal: Using `function` keyword and global variables**

```bash
function foo {
    i=foo  # This variable is global and can cause side effects.
}
```

**Optimal: Using recommended syntax with local variables**

```bash
foo() {
	# This variable's scope is limited to 'foo', reducing side effects.
    local i=foo
}
```

### Formatting Block Statements

Effective formatting of control structures such as `if`, `for`, and `while` enhances the readability and clarity of Bash scripts. Proper placement of `then` and `do` alongside consistent block closures improves the code's understandability.

#### Importance of Consistent Block Formatting

- **Immediate Structure Recognition**: Placing `then` and `do` on the same line as the control statement clarifies the code's structure, making it easier to follow and reducing potential confusion.
- **Ease of Maintenance**: Uniform block formatting simplifies debugging and code modifications by clarifying the script's logic to other developers.

#### Formatting Guidelines

- **Inline Placement**: Position `then` immediately after `if` statements and `do` immediately after `for` or `while` loops on the same line.
- **New Line for Block Endings**: End `if` statements with `fi` and loops `done` on their own lines to distinctly mark block terminations.

#### Examples:

**Not Recommended: Misaligned Control Structures**

```bash
if true
then
    echo "This is hard to trace."
fi

true && {
    echo "This syntax is less clear."
}
```

**Recommended: Aligned and Clear Control Structures**

```bash
if true; then
    echo "This is easy to follow."
fi
```

### Single Line Block Statements

While Bash scripts typically benefit from multi-line formatting, single-line block statements can effectively handle simple conditional or loop executions. To maintain readability and structural integrity, it's crucial to follow best practices.

#### Why Use Single-Line Statements Carefully

- **Clarity**: Single-line statements can be concise but must remain clear to enhance readability, especially for straightforward conditions or loops.
- **Maintainability**: Well-formatted single-line statements preserve the script’s logical structure, facilitating easier updates and understanding.

#### Best Practices for Single-Line Statements

- **Distinct Closure**: Always place closing keywords (`fi` for `if` statements and `done` for loops) on a new line in single-line statements to delineate block boundaries clearly.
- **Line Length Management**: Keep single-line statements within a reasonable character limit (e.g., 88 characters) to prevent horizontal scrolling in editors and enhance readability.

#### Examples:

**Not Recommended: Cluttered Single-Line Statements**

```bash
if [[ -f $file_name ]]; then echo "$file_name exists!"; fi

if [[ -f $file_name ]]; then echo "$file_name exists!"; echo "Done"
fi
```

**Recommended: Clear Single-Line Statement**

```bash
if [[ -f $file_name ]]; then echo "$file_name exists!"
fi
```

### Vertical Spacing

Proper vertical spacing is key to making Bash scripts easier to read without adding unnecessary length. Limiting excessive use of blank lines helps to segment the code logically while keeping the script compact.

#### Vertical Spacing Guidelines:

- **Single Blank Line**: Use a single blank line to separate logical blocks of code or functions, facilitating quick visual parsing.
- **Double Blank Lines**: Employ up to two blank lines sparingly to highlight new sections or distinct logical groups within the script.

#### Examples (MODIFY EXAMPLE)

**Recommended Use of Vertical Spacing**

```bash
echo "Start of the script"

echo "First operation completed"

echo "Second operation completed"


echo "End of the script"
```

### Comments

#### General Comment Guidelines

Comments are essential for explaining script functionality and enhancing long-term usability and readability. Effective commenting practices ensure scripts are accessible and easily understood by new developers or future maintainers.

##### Commenting Best Practices

- **Capitalization**: Begin each comment with a capital letter, except when starting with an uncapitalized variable.
- **Punctuation**: Conclude comments with a period to enhance readability and provide a consistent visual cue.
- **Spacing**: Maintain two spaces between the code's end and an inline comment's start to visually distinguish code from commentary.

##### Examples

**Recommended Commenting Style**

```bash
# Assigns "True" to var and demonstrates proper comment formatting.
var=$(echo "True")  # This prints "True".  (Note the two spaces before '#')

# Simple variable assignment with a clear, aligned comment.
box="Box"           # This is a box.       (Comments are aligned for improved readability)
```

#### Functions (MAYBE ADD MORE INFO???)

Well-documented functions are pivotal for sustaining high code quality and ensuring scripts are accessible and maintainable. Clear documentation elucidates the function's purpose, usage, and parameters, benefiting current developers and future maintainers.

##### Guidelines for Documenting Functions

- **Function Name**: Start the documentation by stating the function's name to orient the reader immediately.
- **Purpose**: Offer a concise description of the function's role and its operational context within the script.
- **Parameters**: Detail each parameter the function accepts, noting required versus optional parameters and default values for the latter.

##### Documentation Format

Documentation should be systematically organized and easy to read. Maintain a consistent comment block style that outlines the function's name, purpose, and parameters.

##### Example

**Well-Documented Function**

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

Using pound signs systematically in Bash scripts enhances readability and maintainability, clearly delineating the scope and intent of comments. This structured approach aids comprehension for both the script’s author and future contributors.

##### Guidelines for Using Pound Signs

- **Single Pound Sign (`#`)**: Employ a single pound sign for line-specific comments to explain the purpose of a line or command. Ideal for inline comments or brief annotations.
- **Double Pound Signs (`##`)**: Utilize double pound signs for block-specific comments that describe the functionality of a code block, such as a loop or conditional sequence.
- **Triple Pound Signs (`###`)**: Reserve triple pound signs for significant structural comments that apply to a whole section or complex block, summarizing the overarching purpose or behavior.

##### Examples

**Using Pound Signs Effectively**

```bash
# Single Pound Sign for simple explanations:
export _RAW_URL="https://raw.githubusercontent.com/StrangeRanger/bash-style-guide/main/README.md"  # Contains the raw URL.

# Verify file existence:
if [[ -f $file_path ]]; then
    echo "File exists."
fi

## Double Pound Signs for block descriptions:
## Set up color output environment variables.
export _YELLOW="$(printf '\033[1;33m')"
export _GREEN="$(printf '\033[0;32m')"
export _CYAN="$(printf '\033[0;36m')"
export _RED="$(printf '\033[1;31m')"

## Iterate through text files:
for file in *.txt; do
    echo "Processing $file"
done

### Triple Pound Signs for major sections:
### Manage user input and responses.
if [[ -z $1 ]]; then
    echo "No argument provided."
elif [[ -f $1 ]]; then
    echo "File found."
else
    echo "Condition not met."
fi
```

## Bashisms

As a reminder, this style guide is for Bash. When given a choice, always prefer Bash builtins or keywords instead of external commands or `sh(1)` syntax.

### Conditional Tests

In Bash scripting, it's advisable to use the `[[ ... ]]` syntax for conditional tests, which provides several advantages over the older `[ ... ]` or `test` constructs. This modern approach offers improved string handling and pattern matching capabilities and enhances script safety.

#### Benefits of `[[ ... ]]` Over `[ ... ]` and `test`

- **Enhanced Features**: Supports advanced string operations and pattern matching, making it suitable for more complex conditions.
- **Increased Safety**: Prevents word splitting on variables containing spaces, avoiding common script errors.
- **Greater Flexibility**: The syntax is more readable and easier to manage, especially in intricate conditional expressions.

#### Examples

**Not Recommended: Using 'test' or single brackets**

```bash
test -d /etc  # Older 'test' syntax.

[ -d /etc ]   # Older single bracket syntax.
```

**Recommended: Using double brackets**

```bash
[[ -d /etc ]]  # Modern, more robust double brackets.
```

### Sequence Iteration

For iterating over number sequences in Bash scripts, prefer Bash builtins like brace expansion `{1..5}` and the C-style `for` loop `for ((i = 0; i < n; i++))`. Compared to external commands like `seq`, these methods offer better performance, lower resource use, and increased script simplicity.

#### Advantages of Builtins Over `seq`

- **Enhanced Performance**: Builtins, being processed internally by Bash, execute faster as they avoid launching external processes.
- **Greater Portability**: Builtins are universally supported across Bash platforms, unlike `seq`, which might not be present by default on some systems.
- **Reduced Complexity**: Scripts become simpler and more readable when using builtins, enhancing both maintainability and readability.

#### Examples

**Less Optimal: Using 'seq'**

```Bash
# Using 'seq' with a fixed range:
for f in $(seq 1 5); do
    echo $f
done

# Using 'seq' with a variable limit:
for f in $(seq 1 "$n"); do
    echo $f
done
```

**Optimal: Using Bash Builtins**

```bash
# Using brace expansion for a fixed range:
for f in {1..5}; do
    echo $f
done

# Using a C-style for loop for variable limits:
for ((i = 0; i < n; i++)); do
    echo $i
done
```

### Command Substitution

Command substitution in Bash scripting captures the output of a command for use in a variable. The syntax `$(...)` is strongly recommended over the older backticks `` `...` ``, due to its readability, ease of nesting, and compatibility with other shell features.

#### Advantages of `$(...)` Over Backticks

- **Improved Readability**: The `$(...)` format is visually clearer, making scripts easier to read and understand, particularly when commands get complex.
- **Easier Nesting**: Facilitates nesting multiple commands without the syntactic awkwardness associated with backticks.
- **Enhanced Compatibility**: Works better with double quotes and other shell elements, reducing the likelihood of errors.

#### Examples

**Not Recommended: Using Backticks**

```Bash
# Capturing the date with backticks:
foo=`date`
```

**Recommended: Using `$(...)`**

```bash
# Capturing the date with modern syntax:
foo=$(date)
```

### Arithmetic Operations

In Bash scripts, arithmetic operations are best performed using the `((...))` syntax for conditional expressions and `$((...))` for arithmetic expansion. These mechanisms are preferred over the `let` command due to their clarity, simplicity, and inherent safety features.

#### Advantages of `((...))` and `$((...))`

- **Clarity**: Both `((...))` and `$((...))` clearly delineate arithmetic expressions within scripts, making them easy to identify and understand.
- **Simplicity**: These constructs are natively supported by Bash, providing a streamlined approach compared to more verbose alternatives like `let`.
- **Increased Safety**: Unlike `let`, which can execute expressions as commands if not properly handled, `((...))` and `$((...))` specifically evaluate arithmetic, minimizing the risk of accidental command execution.

#### Examples

**Not Recommended: Using `let`**

```bash
# Using 'let' for comparison:
a=5
b=4
let "result = a > b"
if [ $result -ne 0 ]; then
    echo "a is greater than b"
fi
```

**Recommended: Using Arithmetic Syntax**

```bash
# Using `((...))` for direct arithmetic comparison:
a=5
b=4
if ((a > b)); then
    echo "a is greater than b"
fi
```

### Parameter Expansion

Parameter expansion in Bash scripting is a robust tool for directly manipulating string variables within the shell. This built-in feature eliminates the need for external commands like `echo`, `sed`, or `awk`, enhancing both efficiency and script performance.

#### Advantages of Parameter Expansion

- **Increased Efficiency**: Performs operations within the shell, reducing the overhead associated with initiating external processes.
- **Streamlined Scripting**: Keeps string manipulations inline and shell-native, simplifying the script's logic.
- **Enhanced Portability**: Improves script portability across different Unix-like systems by avoiding dependency on external tools, which may vary in availability or functionality.

#### Examples

**Not Recommended: Using External Commands**

```Bash
# Using external commands to manipulate strings:
name="hunter"
prog=$(basename "$0")
no_numbers=$(echo "$name" | sed -e 's/[0-9]//g')
```

**Recommended: Using Parameter Expansion**

```bash
# Using parameter expansion for string manipulation:
name="hunter"
prog=${0##*/}
no_numbers=${name//[0-9]/}
```

### Avoiding Parsing `ls`

Using `ls` to list files in Bash scripting often leads to complications, particularly when filenames include spaces, newlines, or special characters. Instead, utilize Bash's built-in globing feature, which offers a safer and more robust alternative for iterating over files in a directory.

#### Reasons to Use Bash Globing Over `ls`

- **Enhanced Reliability**: Parsing `ls` can fail in unexpected ways if filenames contain special characters or spaces.
- **Improved Security**: Scripts that parse `ls` are susceptible to security risks, especially if filenames are designed maliciously.
- **Simplicity**: Using Bash globing (`*`) simplifies scripts by directly and safely accessing filenames without external commands.

#### Examples

**Not Recommended: Parsing `ls`**

```Bash
# Risky and potentially problematic:
for f in $(ls); do
    echo "Processing $f"
done
```

**Recommended: Using Globing**

```bash
# Safe and straightforward method using globing:
for f in *; do
    echo "Processing $f"
done
```

### Using Arrays Over Space-Separated Strings

In Bash scripts, arrays should be preferred over space-separated strings for managing collections of elements. Arrays offer structured, flexible, and robust handling, particularly when elements contain spaces or special characters.

#### Benefits of Using Arrays:

- **Clarity and Safety**: Arrays eliminate issues like word splitting and unwanted glob expansion that can occur with space-separated strings.
- **Flexibility**: Arrays enable straightforward manipulation and access to individual elements, and simplify commands requiring multiple arguments.
- **Ease of Maintenance**: Scripts using arrays are clearer and easier to maintain, a significant advantage as script complexity grows.

#### Examples

**Not Recommended: Using Space-Separated Strings**

```Bash
# Space-separated string might lead to incorrect processing:
modules="json httpserver jshint"
for module in $modules; do
    npm install -g "$module"
done
```

**Recommended: Using Arrays**

```bash
# Array provides clear and safe element handling:
modules=(json httpserver jshint)
for module in "${modules[@]}"; do
    npm install -g "$module"
done

# Efficient array usage with commands accepting multiple arguments:
npm install -g "${modules[@]}"
```

### Using `read` for Parsing Strings

The `read` command in Bash is a highly effective builtin for directly parsing strings and user input, enhancing script performance and security while simplifying maintenance.

#### Advantages of Using `read`:

- **Efficiency**: `read` operates within the shell, avoiding the need for slower, resource-intensive external commands.
- **Security**: By parsing directly into Bash variables, `read` minimizes the risks associated with external command outputs, such as injection attacks.
- **Simplicity**: Offers an easy-to-understand syntax for directly parsing complex data structures.

#### Example

**Parsing a Fully Qualified Domain Name (FQDN)**

```Bash
# Example FQDN:
fqdn="computer1.daveeddy.com"

# Using IFS (Internal Field Separator) to split the FQDN by periods:
IFS=. read -r hostname domain tld <<< "$fqdn"
echo "$hostname is in $domain.$tld"
# Expected Output: "computer1 is in daveeddy.com"
```

## External Commands

### Portability of Bash Scripts

Bash scripts frequently utilize external tools such as `awk`, `sed`, and `grep`. While these tools are powerful, relying on GNU-specific options can reduce script portability, particularly on systems like BSD or macOS that employ different versions of these utilities.

#### Best Practices for Ensuring Portability

- **Basic Options**: Stick to basic and universally supported options within external commands to maximize compatibility across all Unix-like systems.
- **Leverage Builtins**: Utilize Bash's built-in functionalities, which can often replace external commands for tasks such as string manipulation and file processing, enhancing both script efficiency and portability.

### Avoiding Unnecessary Use of Cat (UUOC)

The "Useless Use of Cat" (UUOC) phenomenon involves the redundant use of `cat` to pipe data into commands like `grep`. This practice can be streamlined by using direct redirection or by passing filenames directly to commands that support it, thereby improving script performance.

#### Examples

**Less Efficient: Using `cat` Unnecessarily**

```bash
# Example of UUOC:
cat file | grep foo  # This unnecessarily invokes 'cat'.
```

**More Efficient Alternatives**

```bash
# Redirects file directly into `grep`:
grep foo < file

# Passes the filename directly to `grep`:
grep foo file

```

## Style

### Guidelines for Using Quotes in Bash

Proper quoting is essential in Bash scripting to control string handling and variable manipulation. Double quotes are typically recommended to ensure that variables and command outputs are expanded correctly and safely.

#### When to Use Double Quotes

- **Variable Expansion**: Double quotes facilitate the expansion of variables, keeping the context intact.
- **Command Substitution**: Ensures that the output from command substitutions is processed accurately.

##### Examples

**Correct Usage of Double Quotes**

```bash
# Proper handling of variables and command substitutions:
foo="$USER contains your username"
bar="You are $USER"
echo "\$USER = $USER"
```

**Less Optimal: Using Single Quotes Incorrectly**

```bash
# This does not allow variable expansion:
foo='Hello World'  # Correct if literal string is intended.
```

#### Exceptions to Using Double Quotes

- **Literal Strings**: Use single quotes when no expansion is desired, which is essential in commands like `sed` or `awk` where unintended variable expansion could lead to errors.
- **Handling Undefined Variables**: If the script should treat empty or undefined variables differently (e.g., omitting them instead of treating as empty strings), omitting quotes may be necessary.

##### Examples

**Using Single Quotes for Literal Strings**

```bash
# Ensures the string is treated exactly as written:
grep 'exact string' filename
```

**Omitting Quotes with Undefined Variables**

```bash
# Example where omitting quotes impacts behavior when using single brackets:
if [ -z $UNSET_VAR ]; then
    echo "Variable is not set or is empty"
fi
# Note: This can lead to unexpected results if $UNSET_VAR expands to a string with spaces.
```

#### Handling Variables

Quoting variables in Bash is crucial to prevent word splitting and globbing. However, there are specific scenarios where quotes can be safely omitted without causing unwanted effects.

##### When Not to Quote Variables

- **Within Double Brackets**: Inside `[[ ... ]]`, Bash does not perform word splitting or pathname expansion, so quotes are generally not necessary.
- **When Ignoring Empty Variables**: Leaving variables unquoted in conditional checks can be useful when you want to test for non-existence or non-assignment.

##### Example

**Correct Variable Quoting**

```bash
foo="hello world"

# No quotes needed in double brackets:
if [[ -n $foo ]]; then
    echo "Variable foo is not empty."
fi

# Quotes necessary to prevent word splitting when echoing:
echo "$foo"

```

### Guidelines for Declaring Variables

Effective variable declaration in Bash is essential for clear, maintainable, and consistent scripts. Proper practices distinguish between local variables and those intended for export, following specific naming conventions and usage rules.

#### Local Variables

- **Naming Conventions**: Use lowercase for local variable names to differentiate them from uppercase environment variables. Each word should be separated by an underscore (`_`).
- **Scope Management**: Use the `local` keyword when declaring variables within functions to confine their scope and prevent potential conflicts with global variables.

#### Exported Variables

- **Naming Conventions**: Exported variables should be uppercase, with an underscore `_` prefix and underscores separating words, making them easily identifiable.
- **Declaration**: Clearly indicate a variable's intent for environmental export by using `export` at the time of declaration.

#### Practices to Avoid

- **Using `let`**: For arithmetic operations, prefer the `((...))` syntax over `let` for better clarity and consistency.
- **Using `readonly`**: Use `readonly` sparingly, only when it's crucial to prevent further modifications to the variable.
- **Using `declare`**: While `declare` is useful for associative arrays, use direct assignment for standard variables unless specific scoping or attribute management is needed.

#### Examples

**Less Optimal Practices**

```bash
# Examples of discouraged practices:
declare -i foo=5
let foo++
readonly bar="something"
FOOBAR=baz  # Looks like an exported variable but is not.
export food=5  # Incorrect naming convention.
export food_cart=5  # Incorrect naming convention.
```

**Recommended Practices**

```bash
# Examples of best practices:
i=5
((i++))
bar="something"  # Not readonly as it's typically unnecessary.
foobar=baz  # Correctly cased for a local variable.
export _FOOD=5
export _FOOD_CART=5
```

### Best Practices for Shebang Lines in Bash Scripts

The shebang line is vital in a Bash script to determine which shell executes the script. It ensures script compatibility across different environments and systems.

#### General Guidelines

- **Universal Compatibility**: Use `#!/usr/bin/env bash` to ensure that the script runs on various operating systems, including BSD, macOS, and Linux. This shebang finds and uses the first instance of Bash in the user’s `PATH`, accommodating non-standard Bash installations.
- **Linux-Specific Scripts**: For scripts that are only intended for Linux environments, where Bash is typically located at `/bin/bash`, use `#!/bin/bash`.

#### Why the Difference Matters:

- **Variability in Bash Locations**: Bash might not be located in the same path on non-Linux systems. For example, macOS often uses an older Bash version installed by default, and many users upgrade Bash using Homebrew, which installs it in a different location.
- **Path Flexibility**: The `#!/usr/bin/env bash` shebang adds flexibility by allowing the script to run with the Bash version found in the user’s environment. This is especially important for scripts requiring features from newer Bash versions.

#### Examples

**For Universal Compatibility**

```bash
#!/usr/bin/env bash
```

**For Linux-Specific Environments**

```bash
#!/bin/bash
```

## Handling Errors Properly

Error checking is crucial in Bash scripting to ensure that your scripts behave as expected, even when encountering runtime errors. Proper error-handling techniques can prevent scripts from causing unintended effects.

### Checking Command Success

It is crucial to verify the success of commands within Bash scripts, particularly for operations where failure could lead to severe consequences. Conditional checks help ensure that scripts only proceed when commands execute successfully.

#### Why Check Command Success

- **Avoid Missteps**: Operations like `cd` can fail for various reasons, such as permission issues or non-existent directories. Failing to handle these errors can lead to incorrect script operations, potentially modifying or deleting the wrong files.
- **Script Robustness**: Scripts that check for command success are more robust and reliable, preventing cascading failures that occur when subsequent commands rely on the success of previous ones.

#### Example

**Not Recommended: No Error Handling**

```bash
# This approach risks continuing in the wrong directory:
cd /some/path
rm file
```

**Recommended: Immediate Error Handling**

```bash
# Ensures the script exits if 'cd' fails, preventing unintended operations:
cd /some/path || exit
rm file
```

### Overview of `trap` in Bash

The `trap` command in Bash scripts is utilized to define actions that should be taken when specific signals or events occur. It's invaluable for managing cleanups and ensuring graceful script terminations.

#### Importance of `trap`

- **Resource Management**: `trap` helps ensure that temporary files and other system resources are properly cleaned up, even if the script exits unexpectedly.
- **Increased Robustness**: By handling signals like interruptions or terminations, scripts can respond to unforeseen events more reliably.

#### Common Signals to Trap

- **SIGINT**: Interrupt signal, typically sent from the keyboard (Ctrl+C), indicating that a process should cease operation.
- **SIGTERM**: Termination signal, commonly sent by the `kill` command to request a graceful shutdown.
- **EXIT**: A pseudo-signal used to execute commands upon script exit, regardless of the exit's nature.

#### Example

**Basic Cleanup on Script Exit**

```bash
# Execute cleanup when the script exits:
trap 'rm -f /tmp/my_temp_file; echo "Cleanup complete."' EXIT
```

**Handling Interruptions**

```bash
# Respond to an interrupt signal:
trap 'echo "Script interrupted."; exit' SIGINT
```

**Complex Signal Handling**

```bash
# Advanced trapping with a function:
trap cleanup SIGINT SIGTERM
cleanup() {
    echo "Performing cleanup tasks..."
    # Add cleanup commands here
    exit
}
```

### Understanding `set -e` in Bash Scripting

The `set -e` option in Bash scripts causes the script to exit immediately if any command exits with a non-zero status under conditions that do not explicitly handle errors. This option enforces stricter error handling that can prevent scripts from running faulty commands.

#### Caution with `set -e`

- **Unintended Exits**: Using `set -e` can lead to unexpected script terminations if a command fails, which might not align with the intended flow, especially in scripts designed to handle errors internally.
- **Debugging Challenges**: In complex scripts, `set -e` can obscure the point of failure, complicating the debugging process, particularly in scripts with extensive command lists or pipelines.

#### Example Situations

**Potential Issues with `set -e`**

```bash
# Setting `set -e` can cause premature exits:
set -e
command_that_might_fail || handle_failure
```

**Flexible Error Handling without `set -e`**

```bash
# Manually handling errors allows for more controlled responses:
command_that_might_fail || {
    echo "Handling failure explicitly..."
    # Additional commands to manage the error
}
```

### The Risks of Using `eval` in Bash

The `eval` command in Bash scripts reassembles its arguments into a single command and executes them, presenting significant security risks, especially when the executed content is derived from user input or untrusted sources. Due to these risks, using `eval` is generally discouraged.

#### Why Avoid `eval`?

- **Security Vulnerabilities**: `eval` can potentially execute arbitrary code, making it a dangerous tool susceptible to code injection attacks. This risk is particularly high if any part of the `eval` statement is influenced by external inputs.
- **Increased Complexity**: Scripts that utilize `eval` tend to be more challenging to read and debug. `eval` complicates the flow of execution and error tracing due to its dynamic nature.

#### Examples of `eval` Usage and Safer Alternatives

**Example of Risky `eval` Usage**

```bash
# This usage is risky if `$file` is derived from untrusted input:
command="rm -f $file"
eval "$command"
```

**Recommended Safer Alternative**

```bash
# Direct command execution avoids the risks associated with `eval`:
rm -f "$file"
```

### Redirecting Errors to stderr in Bash

Proper error handling in Bash scripts involves directing error messages to the standard error stream (stderr). This practice enhances the clarity of script outputs and improves usability by differentiating between normal outputs and errors.

#### Importance of Redirecting to stderr

- **Clarity**: Directing errors to stderr keeps them separate from standard output, facilitating easier debugging and more accurate logging.
- **Usability**: Allows for the independent redirection of errors from standard output, which is beneficial in command-line utilities and scripts integrated into larger pipelines.

#### How to Redirect Errors

Use the redirection operator `>&2` to send error messages to stderr. This operator ensures that error messages are appropriately routed to stderr, which is typically reserved for errors and diagnostic outputs.

#### Example

**Directing an Error Message to stderr**

```bash
# Useful for emphasizing errors by sending them to the stderr stream:
echo "Failed to execute file" >&2
```

## Common Mistakes

### Understanding Quoting and Braces in Bash

Correct handling of variable expansion in Bash scripting is crucial for preserving the intended behavior of scripts, especially concerning whitespace management and word splitting. Using quotes and curly braces (`{}`) ensures variables are expanded accurately without undesired effects.

#### Importance of Quoting Variables

- **Preserving Whitespace**: Quotes ensure that all characters within a variable, including spaces and tabs, are treated as a single entity.
- **Preventing Word-Splitting**: Unquoted variables are split into separate words by Bash based on spaces, tabs, or newlines defined in the internal field separator (IFS).

#### Benefits of Using Curly Braces

- **Disambiguation**: Curly braces help specify the boundary of a variable name when followed by text that could be interpreted as part of the name, aiding in situations where text or characters directly follow a variable.

#### Examples and Comparisons

**Impact of Not Using Quotes**

```bash
# Output loses internal whitespace due to incorrect quoting:
for f in "1 space" "2  spaces" "3   spaces"; do
    echo ${f}
done
# Output:
# 1 space
# 2 spaces
# 3 spaces
```

**Correct Usage of Quotes**

```bash
# Using quotes to preserve whitespace:
for f in "1 space" "2  spaces" "3   spaces"; do
    echo "$f"
done
# Output:
# 1 space
# 2  spaces
# 3   spaces
```

**Using Curly Braces for Clarity**

```bash
# Incorrect variable interpretation without braces:
echo "$HOME is $USERs home directory"
# Correct disambiguation with braces:
echo "$HOME is ${USER}s home directory"
```

### Understanding When to Use For vs. While Loops

Choosing between `for` and `while` loops in Bash scripting depends on the nature of the data being processed. `For` loops are optimal for fixed-size data sets such as lists and arrays, while `while` loops excel in handling streams or large, indefinite data sets.

#### Limitations of For Loops with Newline Separated Data

Using `for` loops to process newline-separated data, such as files or command outputs, can lead to several issues:

- **Memory Usage**: `For` loops load all elements into memory, which can become inefficient with large data sets.
- **Field Splitting Issues**: Incorrectly parsing spaces and special characters can cause errors, as `for` loops split data based on spaces by default.
- **Word Splitting and Globbing**: Unquoted variables in `for` loops can lead to unexpected word splitting and globbing, altering script behavior.

#### Advantages of While Loops

- **Memory Efficiency**: `While` loops process data line by line, which is more efficient for memory usage.
- **Accurate Field Handling**: Using `while` loops with `read` allows precise control over field splitting and can handle complex data patterns more reliably.

#### Example Comparison

**Less Efficient For Loop**

```bash
# Potentially problematic `for` loop processing:
users=$(awk -F: '{print $1}' /etc/passwd)
for user in $users; do
    echo "user is $user"
done
```

**More Efficient While Loop**

```bash
# Memory-efficient and safe `while` loop using `read`:
while IFS=: read -r user _; do
    echo "$user is user"
done < /etc/passwd
```

## Extra

- http://mywiki.wooledge.org/BashPitfalls

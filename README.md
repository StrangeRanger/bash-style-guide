# Bash Style Guide

[![Project Tracker](https://img.shields.io/badge/repo%20status-Project%20Tracker-lightgrey)](https://wiki.hthompson.dev/en/project-tracker)

This Bash Style Guide provides detailed recommendations for writing safe and predictable bash scripts. The guidelines are primarily based on [Wooledge's Bash Practices](http://mywiki.wooledge.org/BashGuide/Practices), which is an excellent resource for understanding the fundamentals of Bash scripting.

If any practices are not explicitly mentioned in this guide, they should be considered to align with those outlined on the aforementioned wiki. You are encouraged to [fork this guide on GitHub](https://github.com/StrangeRanger/bash-style-guide) for your own use and to contribute to its improvement.

## Preface

This guide aims to be as objective as possible by providing well-founded reasons for each recommended practice. It covers both functional and aesthetic scripting choices, although for purely stylistic preferences that may vary, please refer to the `Aesthetics` section discussed below.

## Aesthetics

<!-- TODO: Maybe add information right here? -->

### Indentation

Indentation significantly enhances code readability and maintainability in Bash scripting. It provides a clear structure and improves the logical flow of control structures and functions.

#### Guidelines for Indentation

- **Indent Size**: Use four spaces per indentation level for an optimal balance between readability and space conservation.
- **Uniformity**: Consistently apply these indentation methods throughout your scripts to maintain code clarity and ease of understanding.

#### Why Spaces Over Tabs?

Spaces are preferred because they render uniformly across all editing environments, reducing misalignment issues. Additionally, using spaces can help minimize merge conflicts in version control systems, making it a safer choice for projects involving multiple contributors.

#### Example

**Correct Usage with Spaces**

```bash
if [[ var = true ]]; then
    echo "true"
fi
```

**Incorrect Usage with Tabs (Potential Misalignment)**

```bash
if [[ var = true ]]; then
	echo "true"
fi
```

### Line Length

<!-- TODO: COME BACK TO AND ADD MORE AND ORGANIZE!!! -->

Maintaining an optimal line length in Bash scripts is essential for readability and maintainability. By adhering to a standard line length, you can enhance code clarity and ensure that scripts display correctly across various platforms and tools.

#### Why Limit Line Length

- **Readability**: Shorter lines eliminate the need for horizontal scrolling, making the code easier to read across different devices and screen sizes.
- **Maintainability**: Code that fits within a standard visual range is simpler to review and debug, avoiding the need to scroll horizontally.
- **Compatibility**: A standard line length ensures code displays well in various development tools, including code review platforms and IDEs.

#### Guidelines for Line Length

- **Adhere to the 88-Character Limit**: As a general rule, strive to keep lines within 88 characters to ensure readability and maintainability.
- **Evaluate the Need for Exceptions**: Allow exceptions to the line length rule when dealing with long strings, URLs, complex expressions, or configuration lines that require clarity and are more understandable when not broken into multiple lines.
- **Use Line Continuation Judiciously**: When exceeding the line length is unavoidable, use line continuation techniques thoughtfully to ensure that the extended lines remain clear and maintain logical coherence.

#### Examples

**Example Where Exceeding 88 Characters is Necessary**

```bash
# Configuration file exception
config_option="ThisIsAReallyLongConfigurationParameter = 'This is a very long value that makes sense to keep on one line'"
```

**Explanation**: In cases where, for example, a configuration parameter or URL is too long to fit within the 88-character limit, it is acceptable to exceed this limit to maintain the line's readability and logical coherence.

**Example That Breaks on a New Line**

```bash
# Breaking a command across lines can maintain clarity and enhance readability:
curl -X POST -H "Content-Type: application/json" \
     -d '{"username":"admin","password":"secret"}' \
     http://example.com/api/login
```

**Explanation**: This `curl` command is split across multiple lines for each option, which maintains clarity by separating different components of the command such as headers and data payload. Each segment is logically distinct, making the overall command easier to understand.

#### Guidelines for Formatting Multiline Commands

Formatting multiline commands in Bash scripting is crucial for enhancing readability and maintainability. Here are some effective guidelines that address alignment, indentation, and the placement of operators like `||` and `&&`:

- **Indentation and Alignment**:
  - **Standard Indentation**: Use a standard four-space indentation for each continuation line. This helps visually separate the continued lines from other code blocks.
  - **Align With First Argument**: When breaking a command across multiple lines, align each subsequent line with the command's first argument on the previous line. This alignment creates a clear visual separation between the command and its arguments.
- **Placement of Operators (`||`, `&&`)**:
  - **Beginning of New Line**: Place logical operators like `||` and `&&` at the beginning of the new line, not the end of the line. This approach makes it clear that the command continues and is dependent on the logic of the previous line.
- **Line Breaks**:
  - **Break Before Operators**: When using pipes (`|`) or redirection (`>`, `<`), place these operators at the beginning of the new line to emphasize the flow of data or commands.
  - **Logical Grouping**: Organize your breaks around logical groupings of operations. For example, you might group all options related to a single functionality together.
- **Comments**:
  - **Inline Comments**: Place comments at the end of a line if they are explaining something on that specific line.
  - **Above the Line**: If the comment is more general or explains the rationale for the next few lines, place it above the command it describes.

#### Examples of Multiline Command Formatting

##### Indentation and Alignment

**Example: Aligning with the First Argument**

```bash
# A complex curl command broken into multiple lines:
curl -X POST \
     -H "Content-Type: application/json" \
     -d '{"username": "admin", "password": "secret"}' \
     http://example.com/api/login
```

**Explanation**: Each continuation line starts at the same column as the first argument of the command. This maintains a clean vertical alignment that separates the command from its arguments.

##### Placement of Operators (`||`, `&&`)

**Example: Logical Operators at the Beginning of a New Line**

```bash
# Using logical operators at the start of a new line for clarity:
cd /some/directory \
  && echo "Directory change successful." \
  || echo "Failed to change directories." && exit 1
```

**Explanation**: Placing `&&` and `||` at the beginning of a new line makes it clear that the execution of subsequent commands depends on the outcome of the previous command.

##### Line Breaks

**Example: Emphasizing Flow with Line Breaks**

```bash
# Example using pipes and redirection at the beginning of the new line:
grep "error" log.txt \
  | sort \
  | uniq -c \
  > error_summary.txt
```

**Explanation**: Pipes and redirection symbols are placed at the start of new lines to visually emphasize the data flow from one command to the next.

##### Logical Grouping

**Example: Grouping Related Options Together**

```bash
# Example of a command with options grouped logically:
ffmpeg -i input.mp4 \
       -codec:v libx264 -codec:a aac \
       -map 0:v -map 0:a \
       -profile:v high -level:v 4.0 \
       output.mp4
```

**Explanation**: Related options are grouped together in logical blocks (video options, audio options, mapping), which improves readability and makes the command easier to understand.

##### Comments

**Example: Inline and Above-the-Line Comments**

```bash
# Setting up environment variables for deployment
export ENV="production"  # This environment variable sets the mode to production
export DEBUG_MODE="false"  # Debugging is turned off

# Deploying application
./deploy.sh \
  --user=admin \
  --verbose  # Running deployment in verbose mode for detailed logs
```

**Explanation**: Comments are used both inline to explain specific lines and above command blocks to provide context for what the script or block will do.

### Semicolons in Bash Commands

Semicolons are command separators in Bash, allowing the execution of multiple commands on a single line. While useful, their unnecessary use should be minimized to enhance script clarity and prevent errors.

#### Why Minimize Semicolons?

- **Enhanced Readability**: Omitting unnecessary semicolons reduces visual clutter, making scripts easier to follow and understand.
- **Reduced Error Risk**: Unnecessary semicolons can lead to syntactic mistakes and unexpected behavior, complicating the debugging process.

#### Guidelines for Using Semicolons

- **Necessity Over Convenience**: Use semicolons only when necessary to separate multiple commands on a single line. Avoid using them after the last command in a series or where line breaks can suffice.
- **Clarity and Maintenance**: Consider future script maintenance and readability. Scripts that are easier to read are easier to maintain and debug. Avoid semicolons that might obscure the flow of commands.

#### Examples

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

Properly declaring functions in Bash scripts is essential for maintaining code clarity and structure. Consistent syntax and scope management help ensure that functions are well-defined and easily understood.

#### Guidelines for Function Declarations

- **Syntax**: Always declare functions using the name followed by parentheses and curly braces (`name() {}`).
- **Avoid the `function` Keyword**: The `function` keyword is not POSIX compliant and can lead to compatibility issues. Using `name()` ensures consistency and simplicity across scripts.
- **Scope Management**: Use `local` variables within functions to limit their scope.

#### Why These Guidelines Matter

- **Uniformity and Clarity**: A consistent declaration style across your scripts makes them easier to read and maintain.
- **Portability and Simplicity**: Sticking to POSIX-compliant syntax ensures your scripts are more portable and less complex.
- **Reduced Side Effects**: Managing variable scope with `local` declarations minimizes the likelihood of variables affecting the global state or interfering with other parts of the script.

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

Effective formatting of control structures such as `if`, `for`, and `while` enhances the readability and clarity of Bash scripts. Proper placement of the `then` and `do` keywords alongside consistent block closures, improves the code's structure and maintainability.

#### Guidelines for Formatting Block Statements

- **Inline Placement**: Position `then` immediately after `if` statements and `do` immediately after `for` or `while` loops on the same line.
- **New Line for Block Endings**: End `if` statements with `fi` and loops with `done` on their own lines to distinctly mark block terminations.

#### Importance of Consistent Block Formatting

- **Immediate Structure Recognition**: Placing `then` and `do` on the same line as the control statement clarifies the code's structure, making it easier to follow and reducing potential confusion.

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

While Bash scripts typically benefit from multi-line formatting, single-line block statements can effectively handle simple conditional or loop executions. It's crucial to follow best practices to maintain readability and structural integrity.

#### Guidelines for Single-Line Statements

- **Clarity and Maintainability**: Single-line statements should be concise and clear to enhance readability, especially for straightforward conditions or loops. Always ensure that single-line statements preserve the script’s logical structure, facilitating easier updates and understanding.
- **Distinct Closure**: Place closing keywords (`fi` for `if` statements and `done` for loops) on a new line to delineate block boundaries clearly.
- **Line Length Management**: Keep single-line statements within a reasonable character limit (e.g., 88 characters) to prevent horizontal scrolling in editors and enhance readability.
- **Avoid Clutter**: Refrain from adding multiple commands or complex logic to single-line statements. This helps maintain clarity and prevent code clutter.

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

### Vertical Spacing (MODIFY EXAMPLE)

Proper vertical spacing is key to making Bash scripts easier to read without adding unnecessary length. Limiting excessive use of blank lines helps to segment the code logically while keeping the script compact.

#### Guidelines for Vertical Spacing

- **Single Blank Line**: A single blank line separates logical blocks of code or functions, facilitating quick visual parsing.
- **Double Blank Lines**: Employ up to two blank lines sparingly to highlight new sections or distinct logical groups within the script.

#### Examples

**Recommended Use of Vertical Spacing**

```bash
echo "Start of the script"

echo "First operation completed"

echo "Second operation completed"


echo "End of the script"
```

### Comments

<!-- TODO: Add something about comments that separate each section!!! -->

#### General Comments

Comments are essential for explaining script functionality and enhancing long-term usability and readability. Effective commenting practices ensure scripts are accessible and easily understood by new developers or future maintainers.

##### Guidelines for Commenting

- **Capitalization**: Begin each comment with a capital letter, except when starting with an uncapitalized variable.
- **Punctuation**: Conclude comments with a period to enhance readability and provide a consistent visual cue.
- **Spacing**: Maintain two spaces between the code's end and an inline comment's start to visually distinguish code from commentary.

##### Examples

**Recommended Commenting Style**

```bash
# Assigns "True" to var and demonstrates proper comment formatting.
var=$(echo "True")  # This prints "True". (Note the two spaces before '#')

# Simple variable assignment with a clear, aligned comment.
box="Box"           # This is a box.      (Comments are aligned for improved readability)
```

#### Functions

<!-- TODO: Maybe add more? Describe a specific fashion of commenting like the example. Like it has to look like that? -->

Functions in Bash scripts should be well-documented to clarify their purpose, parameters, and expected behavior. Properly formatted comments enhance script readability and maintainability, making it easier for developers to understand and modify the code.

##### Guidelines for Documenting Functions

- **Function Name**: Start the documentation by stating the function's name to orient the reader immediately.
- **Purpose**: Offer a concise description of the function's role and its operational context within the script.
- **Parameters**: Detail each parameter the function accepts, noting required versus optional parameters and default values for the latter.

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

In Bash scripting, it's advised to use the `[[ ... ]]` syntax for conditional tests, which provides several advantages over the older `[ ... ]` or `test` constructs. This modern approach offers improved string handling and pattern matching capabilities and enhances script safety.

#### Benefits of `[[ ... ]]` Over `[ ... ]` and `test`

- **Enhanced Features**: Supports advanced operations like regex matching and string comparison. For example, `[[ $name =~ ^A.+ ]]` checks if a variable starts with 'A', demonstrating more powerful pattern matching. 
- **Increased Safety**: Eliminates word splitting on variables containing spaces, preventing errors such as unintentional command execution or misinterpreted conditions. 
- **Greater Flexibility**: Provides a more readable and manageable syntax, particularly in scripts with intricate conditional logic.

#### Guidelines for Conditional Tests

- **Always use `[[ ... ]]` for conditional tests in new scripts** to leverage its enhanced capabilities and avoid common pitfalls associated with older syntax.

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

### Sequence Iteration (CONTINUE FROM HERE)

For iterating over number sequences in Bash scripts, prefer Bash builtins like brace expansion `{1..5}` and the C-style `for` loop `for ((i = 0; i < n; i++))`. These methods offer better performance, lower resource use, and increased script simplicity compared to external commands like `seq`.

#### Advantages of Builtins Over `seq`

- **Enhanced Performance**: Builtins execute faster as they are processed internally by Bash, avoiding the overhead of launching external processes.
- **Greater Portability**: Builtins are universally supported across Bash platforms, unlike `seq`, which may not be present by default on some systems.
- **Reduced Complexity**: Scripts become simpler and more readable when using builtins, enhancing both maintainability and readability.

#### Guidelines for Sequence Iteration

- **Prefer Builtins for Known Ranges**: Use brace expansion and C-style loops when the range of numbers is known in advance. This approach is not only more performant but also clearer and more concise.
- **Avoid `seq` for Portability and Efficiency**: Since `seq` is not a builtin and not universally available, using builtins like `{1..5}` or `for ((i = 0; i < n; i++))` avoids potential compatibility issues and reduces script execution time.

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

#### Guidelines for Command Substitution

- **Consistently Use `$(...)`**: Adopt the `$(...)` syntax in all scripts to maintain a consistent, modern approach across your codebase. This not only enhances readability but also simplifies the maintenance of your scripts.
- **Avoid Backticks**: Given their limitations and potential for errors, especially in complex scripts, avoid using backticks for command substitution. The modern `$(...)` syntax is more robust and flexible.

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

In Bash scripts, arithmetic operations should ideally be conducted using the `((...))` syntax for conditional expressions and `$((...))` for arithmetic expansion. These built-in mechanisms are preferred over the `let` command for several reasons, including clarity, simplicity, and reduced error potential.

#### Advantages of `((...))` and `$((...))`

- **Clarity**: Both `((...))` and `$((...))` clearly delineate arithmetic expressions within scripts, making them easy to identify and understand.
- **Simplicity**: These constructs are natively supported by Bash, offering a streamlined and intuitive approach to handling arithmetic.
- **Increased Safety**: These methods specifically evaluate arithmetic without the risk of executing other commands, unlike `let`, which can interpret expressions as commands if not carefully handled.

#### Guidelines for Arithmetic Operations

- **Prefer Built-in Syntax**: Always use `((...))` for conditional expressions and `$((...))` for arithmetic expansions to leverage Bash's native capabilities and ensure clarity in your scripts.
- **Avoid Using `let`**: Due to its potential for causing unintended command execution and its less intuitive syntax, avoid using `let` for arithmetic operations.

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

#### Guidelines for Parameter Expansion

- **Prefer Parameter Expansion for String Manipulation**: Utilize parameter expansion to handle string manipulations whenever possible. This approach is not only more efficient but also reduces script complexity and dependencies.
- **Avoid External String Manipulation Tools When Unnecessary**: External tools like `sed` and `awk` are powerful but often overkill for simple string manipulations that can be efficiently handled by parameter expansion.

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

#### Guidelines for File Iteration

- **Prefer Bash Globing for File Listing**: Always use Bash globing patterns like `*` to list files within a script. This method is not only more reliable but also avoids the complexities and potential security issues associated with parsing `ls`.
- **Avoid Using `ls` for Script File Iterations**: Due to the potential for error and security vulnerabilities, avoid using `ls` in loops or where filename interpretation is critical. Rely on globing to handle file names safely and accurately.

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

In Bash scripts, using arrays to manage collections of elements is highly recommended over using space-separated strings. Arrays provide structured, flexible, and robust handling, especially beneficial when elements might contain spaces or special characters.

#### Benefits of Using Arrays

- **Clarity and Safety**: Arrays prevent errors related to word splitting and glob expansion that can occur with space-separated strings.
- **Flexibility**: Arrays allow for straightforward manipulation and access to individual elements, as well as simpler expansion in commands that accept multiple arguments.
- **Ease of Maintenance**: Code utilizing arrays is generally clearer and easier to maintain, particularly as script complexity increases.

#### Guidelines for Using Arrays

- **Prefer Arrays for Collections**: Always opt for arrays when managing collections of elements. This approach avoids common pitfalls associated with space-separated strings, such as word splitting and unintentional globbing.
- **Utilize Arrays for Command Arguments**: Leverage arrays to pass multiple arguments to commands. This method simplifies command syntax and enhances script readability and reliability.

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

#### Guidelines for Using `read`

- **Use `read` for Safe Input Handling**: Employ `read` to safely and efficiently parse user inputs and other data directly into variables. This minimizes potential security risks and avoids the overhead associated with external parsing commands.
- **Customize with IFS for Specific Parsing Needs**: Adjust the Internal Field Separator (IFS) as needed when using `read` to ensure that inputs are split according to your specific requirements, enhancing the flexibility and accuracy of data parsing.

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

Bash scripts frequently utilize external tools such as `awk`, `sed`, and `grep`. While these tools are indispensable for many complex tasks, their GNU-specific options can reduce script portability, especially on systems like BSD or macOS, which may use different versions of these utilities.

#### Guidelines for Ensuring Portability

- **Use Basic Options**: Opt for the most basic and widely supported options available within external commands to ensure your scripts run smoothly on any Unix-like system. This practice helps avoid compatibility issues that arise from system-specific variations in utility implementations.
- **Leverage Bash Builtins**: Whenever possible, replace external commands with Bash's built-in functionalities. This not only boosts the efficiency of your scripts but also enhances their portability by reducing dependencies on external utilities. Builtins such as parameter expansion, pattern matching, and arithmetic operations often provide sufficient capabilities for tasks typically handled by external commands.

### Avoiding Unnecessary Use of Cat (UUOC)

The "Useless Use of Cat" (UUOC) phenomenon involves the redundant use of `cat` to pipe data into commands like `grep`. This practice can be streamlined by using direct redirection or by passing filenames directly to commands that support it, thereby improving script performance.

#### Guidelines for Avoiding UUOC

- **Use Direct Redirection**: Whenever possible, use direct input redirection (`<`) instead of piping from `cat`. This method is more efficient as it reduces the number of processes created.
- **Pass Filenames Directly**: For commands that accept filenames as arguments (like `grep`, `awk`, `sed`), pass them directly instead of reading them through `cat`. This simplifies the command and enhances performance.

#### Examples of Efficient Command Usage

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

### Using Quotes in Bash

Proper quoting is essential in Bash scripting to control string handling and variable manipulation. Double quotes are typically recommended to ensure that variables and command outputs are expanded correctly and safely.

#### Guidelines for Using Quotes

- **Default to Double Quotes**: Use double quotes for most cases to ensure safe expansion of variables and command outputs. Double quotes prevent word splitting and globbing, preserving the intended input as a single string.
- **Use Single Quotes for Literal Strings**: When you need the string to remain exactly as written, without any expansion or interpretation, use single quotes. This is particularly important in commands like `sed` or `awk` where you might not want Bash to interpret variables or escape sequences.
- **Omit Quotes With Caution**: Quotes can be safely omitted in specific scenarios, such as within `[[ ... ]]` for conditional expressions where Bash does not perform word splitting. Also, omit quotes when you want to handle variables that may be undefined or empty differently than if they were quoted.

#### Examples

**Correct Usage of Double Quotes**

```bash
# Proper handling of variables and command substitutions:
foo="$USER contains your username"
bar="You are $USER"
echo "\$USER = $USER"
```

**Using Single Quotes for Literal Strings**

```bash
# Ensures the string is treated exactly as written:
grep 'exact string' filename
```

**Omitting Quotes in Specific Cases**

```bash
# Omitting quotes within [[ ... ]] for conditional expressions:
if [[ -n $var ]]; then
    echo "Variable is not empty."
fi
```

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

### Declaring Variables

Effective variable declaration in Bash is crucial for clear, maintainable, and consistent scripts. Proper practices distinguish between local variables and those intended for export, following specific naming conventions and usage rules.

#### Guidelines for Declaring Variables

- **Local Variables**:
  - **Naming Conventions**: Always use lowercase for local variable names, with each word separated by an underscore (`_`). This helps differentiate them from uppercase environment variables, which are typically used for exported variables.
  - **Scope Management**: Utilize the `local` keyword when declaring variables within functions to limit their scope. This practice prevents potential conflicts with global variables and enhances script modularity and safety.
- **Exported Variables**:
  - **Naming Conventions**: Exported variables should always be in uppercase to make them stand out as global environment variables. Use an underscore (`_`) prefix and underscores to separate words, ensuring they are easily identifiable in the script.
  - **Declaration**: Always use the `export` declaration at the time of variable assignment to clearly indicate that the variable is intended for environmental export.
- **Practices to Avoid**:
  - **Avoid `let`**: Prefer the `((...))` syntax for arithmetic operations, as it provides clearer and more consistent syntax.
  - **Minimize Use of `readonly`**: Apply `readonly` only when it is crucial to ensure a variable cannot be modified, to avoid unintended restrictions within your scripts.
  - **Selective Use of `declare`**: Employ `declare` specifically for managing advanced variable properties, such as associative arrays, and stick to direct assignment for standard variable declarations unless scoping or attributes dictate otherwise.

#### Examples of Variable Declaration

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

### Shebang Lines in Bash Scripts

The shebang line is vital in a Bash script to determine which shell executes the script, ensuring script compatibility across different environments and systems.

#### Guidelines for Shebang Lines

- **Universal Compatibility**: Use `#!/usr/bin/env bash` for scripts that need to run on various operating systems, including BSD, macOS, and Linux. This shebang is particularly useful because it searches the user’s `PATH` to find and use the first instance of Bash, which helps accommodate non-standard Bash installations.
- **Linux-Specific Scripts**: Opt for `#!/bin/bash` when creating scripts solely for Linux environments. In these cases, Bash is typically located at `/bin/bash`, and this shebang ensures direct and predictable script execution.

#### Why the Choice of Shebang Matters

- **Variability in Bash Locations**: On non-Linux systems like BSD and macOS, Bash might not be located in the same path, or the installed version might be older. Many macOS users, for example, upgrade Bash through Homebrew, which does not alter the system-installed version located at `/bin/bash`.
- **Path Flexibility**: Using `#!/usr/bin/env bash` provides significant flexibility, enabling the script to utilize the Bash version installed in the user’s environment, which is crucial for accessing features available in newer Bash versions.

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

#### Guidelines for Checking Command Success

- **Use Conditional Checks After Critical Commands**: Always follow potentially failing commands, like `cd`, with a conditional check. This check can prevent the script from continuing if the command fails, thus avoiding further unintended consequences.
- **Employ Short-circuiting for Error Handling**: Use logical operators such as `||` (OR) to handle errors immediately. For example, `command || exit` or `command || return` can be used to stop the script or exit a function if a command fails.
- **Incorporate Error Messages for Clarity**: When a command fails, provide clear error messages to help diagnose issues quickly. For instance, `cd /some/path || echo "Failed to change directory" && exit` gives immediate feedback on what went wrong.

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

The `trap` command in Bash scripts defines actions that should be taken when specific signals or events occur. It's invaluable for managing cleanups and ensuring graceful script terminations.

#### Importance of `trap`

- **Resource Management**: `trap` helps ensure that temporary files and other system resources are properly cleaned up, even if the script exits unexpectedly.
- **Increased Robustness**: By handling signals like interruptions or terminations, scripts can respond to unforeseen events more reliably.

#### Guidelines for Using `trap`

- **Implement `trap` for Cleanup and Safety**: Always use `trap` to define cleanup operations or other necessary actions that should occur before a script exits unexpectedly. This is crucial for scripts that create temporary files or make changes to the system that need to be reverted if the script does not complete successfully.
- **Respond to Specific Signals Appropriately**: Customize your `trap` statements to handle different signals as needed. For example, use `trap` with `SIGINT` to handle user interruptions gracefully, ensuring that any necessary cleanup is performed even when a script is terminated prematurely by the user.
- **Use Functions for Complex Trapping Logic**: For more complex signal handling, define a function that performs all necessary actions and then invoke this function in your `trap` command. This approach keeps your trapping logic organized and easier to manage.

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

The `set -e` option in Bash scripts causes the script to exit immediately if any command within the script exits with a non-zero status, unless the command that fails is part of an until or while loop, part of an if statement, part of a `&&` or `||` list, or if the command’s return status is being inverted using `!`. This feature is similar to the error handling in programming languages like C, where operations may fail without causing the program to stop, unless explicitly handled.

#### Caution with `set -e`

- **Unintended Exits**: Scripts with `set -e` may exit in places you didn’t anticipate if a command fails, which can be problematic for scripts that handle errors internally or expect possible command failures.
- **Complex Conditions**: In complex scripts, the exact point of failure might be obscured by `set -e`, making debugging more difficult, especially in multi-part commands or pipelines.

#### Guidelines for Using `set -e`

- **Use Carefully**: Apply `set -e` in scripts where you need strict error handling, and where each command's success is critical to the next command's execution. This can help prevent errors early in a script from going unnoticed and causing more significant issues later.
- **Combine with Explicit Error Handling**: To mitigate the risks associated with `set -e`, combine it with explicit error checks for crucial commands. This approach allows you to manage essential errors directly and still benefit from the protective aspects of `set -e`.
- **Test Thoroughly**: Due to the potential for unexpected behavior, thoroughly test scripts with `set -e` under various conditions to ensure that they behave as expected without premature exits unless justified.

#### Example Situations

**`set -e` in use**

```bash
# Demonstrating `set -e` in a script:
set -e  # Exit immediately on error

# Proceed only if the directory change is successful
cd /some/important/directory

# The following command only runs if the cd succeeds
cp important_data.txt backup_data.txt
```

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

#### Guidelines for Handling `eval`

- **Avoid Using `eval` When Possible**: Always look for alternatives to `eval`, such as using Bash built-ins or other command constructs that do not require combining and executing strings as commands.
- **Sanitize Inputs Rigorously**: If `eval` must be used, ensure that all inputs are rigorously sanitized, and that any variables included in `eval` statements are strictly controlled and escaped appropriately.
- **Limit Scope and Usage**: Restrict the use of `eval` to areas where it is absolutely necessary and cannot be replaced by safer alternatives. Keep its use isolated to minimize potential damage and ensure that it is easy to review and audit for security vulnerabilities.

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

#### Guidelines for Redirecting Errors

- **Consistently Direct Errors to stderr**: Always use `>&2` to redirect error messages to stderr. This standard practice helps ensure that errors are logged appropriately and do not interfere with the output expected by users or other applications.
- **Use Explicit Error Messaging**: Combine error redirection with clear, descriptive messages that explain what went wrong and possibly how to resolve it. This approach enhances the script's usability and aids in troubleshooting.
- **Integrate Error Handling in All Scripts**: Make it a standard practice to handle errors and direct them to stderr in all scripts, especially those intended for production use or distribution. Consistent error handling improves the reliability and maintainability of your scripts.

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

#### Guidelines for Using Quotes and Braces

- **Consistently Use Quotes for Variable Expansion**: Always use quotes around variables when their expansion should preserve whitespace or when their values might include spaces or special characters. This prevents unintended word splitting.
- **Employ Curly Braces for Clarity and Disambiguation**: Use curly braces to clearly define where a variable name ends and additional characters begin, especially useful in concatenations or when appending characters directly after a variable.

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

#### Guidelines for Using For and While Loops

- **Use For Loops for Known, Fixed-Size Data Sets**: Employ `for` loops when the number of iterations is known beforehand, such as iterating over an array or a range of numbers defined by brace expansion.
- **Opt for While Loops for Streaming or Large Data Sets**: Choose `while` loops when dealing with potentially large or undefined data streams, particularly when reading lines from a file or the output of a command. This method prevents the entire data set from being loaded into memory at once.
- **Handle Field and Word Splitting Carefully**: When using `for` loops, ensure variables are properly quoted to prevent word splitting and globbing issues. In `while` loops, use `IFS` (Internal Field Separator) adjustments and `read` to manage how input lines are divided into fields.

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

**Example of a For Loop Handling an Array**

```bash
# Iterating over an array with a for loop:
names=("Alice" "Bob" "Charlie")
for name in "${names[@]}"; do
    echo "Hello, $name!"
done
```

## Extra

- http://mywiki.wooledge.org/Bashism
- http://mywiki.wooledge.org/BashPitfalls

## License

MIT

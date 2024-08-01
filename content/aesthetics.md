# Aesthetics

As mentioned in the [Preface](/#preface), this guide aims to be as objective as possible by providing well-founded reasons for each recommended practice. However, some practices are purely stylistic and may vary based on personal preference or specific project requirements.

This section addresses these aesthetic choices and provides guidelines for maintaining a consistent and visually appealing script style. Where applicable, the guidelines are accompanied by explanations of their benefits over alternative practices. For style guidelines that are less subjective and have a more significant impact on script functionality, refer to the [Style](/content/style/) section.

## Indentations

Indentations come in two primary forms: tabs and spaces. While both can be used to indent code, spaces are generally preferred over tabs.

/// admonition | Guidelines
    type: tip

- **Indent Size**: Use four spaces for each indentation level to balance readability and space efficiency.

///

### Why Spaces Over Tabs?

Spaces are preferred over tabs because they render uniformly across all editing environments, reducing misalignment issues. Additionally, using spaces can help minimize merge conflicts in version control systems, making it safer for projects involving multiple contributors.

## Column Length

The column length is the number of characters in a single line of code. Limiting the column length can help significantly maintain the readability of your code.

/// admonition | General Guidelines
    type: tip

- **88-Character Limit**: Keep lines within 88 characters.
- **Exceptions**: Allow exceptions when dealing with long strings or complex expressions that require clarity and are more understandable when not broken into multiple lines.

///

### Formatting Multi-lined Commands

When breaking a command exceeding the 88-character limit into multiple lines, it is essential to ensure that it remains readable and easy to follow. Below are some effective guidelines for aligning, indenting, and placing operators (such as `||` and `&&`).

/// admonition | Guidelines
    type: tip

//// tab | Indentation and Alignment

- **Indentation**: Use a standard four-space indentation for each continuation line.

///// admonition | Example

_Two separate commands whose continuation lines are indented by four spaces:_

```bash
tar -czvf /backup/archive-$(date +%F).tar.gz /home/user/documents \
    /home/user/photos /home/user/videos

curl -X POST \
    -H "Content-Type: application/json" \
    -d '{"username": "admin", "password": "secret"}' \
    http://example.com/api/login
```

/////
////
//// tab | Logical Operators

- **Placement**: When multiple commands are connected by logical operators (`&&`, `||`), place these operators at the beginning of a new line with a four-space indent.
- **Reason**: This formatting ensures that the logical operators are clearly visible and distinguishable from the commands they connect.

///// admonition | Example

_Using logical operators with proper indentation:_

```bash
cd /some/directory/in/a/universe/far/far/away \
    && echo "Directory change successful." \
    || echo "Failed to change directories."
```

/////
////
//// tab | Redirect and I/O Operators

- **Placement**: When breaking up a long command that includes redirect or I/O operators (such as `>`, `>>`, `<`, `|`), place these operators at the beginning of a new line with a four-space indent.
- **Reason**: This formatting ensures that the operators are clearly visible and distinguishable from the commands they affect.

///// admonition | Example

_Using pipes and redirection operators with proper indentation:_

```bash
grep "some very long pattern" /a/very/long/path/to/some/file/on/the/system \
    | sort \
    | uniq -c > error_summary.txt
```

/////
////
///

### Why Limit Line Length

- **Readability**: Shorter lines eliminate the need for horizontal scrolling, making the code easier to read across different devices and screen sizes.
- **Compatibility**: A standard line length ensures code displays well in various development tools, including code review platforms and IDEs.

## Function Declaration

While not greatly impacting script readability or functionality, there is a commonly preferred syntax for declaring functions in Bash.

/// admonition | Guidelines
    type: tip

- **Syntax**: ***Always*** declare functions using the name followed by parentheses and curly braces (`name() {}`), rather than the `function` keyword.

///

/// details | Examples

_Using the `function` keyword:_

```bash
function foo {
    local i=foo
}
```

---

_Using the recommended syntax:_

```bash
foo() {
    local i=foo
}
```

///

### Why Avoid the `function` Keyword

- **Convention**: The recommended syntax is a more widely accepted and conventional way of declaring functions in Bash, aligning with common Bash practices and community standards.

## Formatting Block Statements (CONTINUE FROM HERE)

Formatting block statements is a simple way of enhancing the readability of your code. With that said, there are a few guidelines to follow under specific circumstances.

/// admonition | Guidelines
    type: tip

//// tab | Standard Block Statement

- **Inline Placement**: Position `then` on the same line as `if` statements, and `do` on the same line as `for` or `while` loops.
- **New Line for Block Endings**: End `if` statements with `fi` and loops with `done` on their own lines to distinctly mark block terminations.

///// details | Example

_Standard block statement formatting for if statements:_

```bash
if [[ -f $file ]]; then
    echo "File exists."
else
    echo "File does not exist."
fi
```

---

_Standard block statement formatting in for loops:_

```bash
for i in {1..5}; do
    echo "Number: $i"
done
```

/////
////
//// tab | Single Line Block Statement

- **Clarity and Maintainability**: Single-line statements should be concise and clear to enhance readability, especially for straightforward conditions or loops. Always ensure that single-line statements preserve the script’s logical structure, facilitating easier updates and understanding.
- **Avoid Clutter**: Refrain from adding multiple commands or complex logic to single-line statements, including else statements. This helps maintain clarity and prevent code clutter.

///// details | Example

_Single line block statement with else clause:_

```bash
if [[ -f "$file" ]]; then echo "File exists."; else echo "File does not exist."; fi
```

**Disadvantage**: Single-line block statements can become cluttered and less readable when handling complex logic or multiple commands.

---

_Single line block statement without else clause:_

```bash
if [[ -f "$file" ]]; then echo "File exists."; fi
```

**Advantage**: Single-line block statements are concise and effective for simple conditions or loops.

/////
////
///

### Why Format Block Statements

- **Convention**: This style aligns with common coding conventions and practices in the Bash scripting community, which can help ensure that your code is consistent with widely accepted standards.
- **Readability**: Placing `then` on the same line as the `if` statement clearly indicates that the subsequent block is conditional on the `if` statement, making the code easier to read and understand at a glance.

## Vertical Spacing Guidelines

Vertical spacing is just as important as horizontal spacing in maintaining a clean and readable script.

/// admonition | Guidelines
    type: tip

- **Single Blank Line**: A single blank line separates logical blocks of code or functions, facilitating quick visual parsing.
- **Double Blank Lines**: Employ up to two blank lines sparingly to highlight new sections or distinct logical groups within the script.

///

/// details | Examples

```bash
### Variable Section
variable="value"
variable2="value2"


### Function Section
function_name() {
    # Function code here.
}


### Main Script Section
# Main script code here.
```

///

### Why Vertical Spacing Matters

- **Readability**: Too many blank lines can make the script harder to read, while too few can make it appear cluttered. Proper vertical spacing helps maintain a balance between readability and visual organization. Requiring a single blank line between logical blocks or functions ensures that the script is easy to navigate and understand.

## Comments

Comments are essential for explaining script functionality and enhancing long-term usability and readability. Effective commenting practices ensure scripts are accessible and easily understood by new developers or future maintainers. Though similar to the previous guidelines, comments have a few additional considerations depending on their context.

/// admonition | General Guidelines
    type: tip

- **Capitalization**: Begin each comment with a capital letter, except when referring to code elements like variables or functions.
- **Punctuation**: Conclude comments with a period to indicate completion and maintain consistency.
- **Spacing**: For inline comments, maintain two spaces between the code and and the comment.

///

/// details | Examples

```bash
# Assigns "True" to var and demonstrates proper comment formatting.
var=$(echo "True")  # This prints "True".

# Simple variable assignment with a clear, aligned comment.
box="Box"  # This is a box.
```

///

### Function Comments

Functions in bash differ in a few ways from those in other programming languages, especially in terms of argument handling. Due to these differences, functions should be thoroughly documented to clarify their purpose, parameters, and expected behavior.

/// admonition | Guidelines
    type: tip

- **Purpose**: Offer a concise description of the function's role and its operational context within the script.
- **Parameters**: Detail each parameter the function accepts, noting required versus optional parameters and any default values. Though not required, it's **_highly_** recommended assign parameter values to local variables for clarity.
- **Return Values**: Specify the function's output or return values, if applicable.

///

/// details | Examples

```bash
####
# This function processes files given as parameters and logs results. It is designed to
# handle a variable number of input files and demonstrate proper ERROR handling.
#
# Parameters:
#   - $1: input_file (Required)
#       - Path to the file that needs processing.
#   - $2: log_file (Required)
#       - File where the processing logs are stored.
#   - $3: debug_mode (Optional, Default: false)
#       - If set to true, the function will perform debugging operations.
process_files() {
    local input_file="$1"
    local log_file="$2"
    local debug_mode="${3:-false}"

    # Function code here.
}
```

///

### Pound Signs in Comments

Traditionally, a single pound sign (`#`) is used to denote comments in Bash scripts. However, the number of pound signs should be used to help differentiate the purpose and scope of the comments.

/// admonition | Guidelines
    type: tip

//// tab | Single Pound Sign (`#`)

- **Usage**: Employ a single pound sign for line-specific comments to explain the purpose of a line or command.

///// details | Example

```bash
src_dir="/path/to/source"  # Source directory to back up.
dest_dir="/path/to/destination"  # Destination directory for the backup.

# Create a backup using rsync.
rsync -av --delete "$src_dir" "$dest_dir"
```

/////
////
//// tab | Double Pound Signs (`##`)

- **Usage**: Utilize double pound signs for comments that describe the functionality of a code block, such as a loop, conditional sequence, or a list a variables. Once a blank line is placed between the code between the commented code block and the comment, the comment should be considered a new block.

///// details | Example

```bash
## Set source and destination directories.
src_dir="/path/to/source"
dest_dir="/path/to/destination"

## Check if source directory exists, exit if not.
if [[ ! -d $src_dir ]]; then
  echo "Source directory does not exist!"
  exit 1
fi

## Check if destination directory exists, create if not.
if [[ ! -d $dest_dir ]]; then
  echo "Destination directory does not exist. Creating now."
  mkdir -p "$dest_dir"
fi
```

/////
////
//// tab | Quadruple Pound Signs (`####`)

- **Usage**: Quadruple pound signs are used to section off entire parts of the script.

_Please refer to the [Quadruple Pound Signs](#quadruple-pound-signs) section for more details._

////
//// tab | Triple Pound Signs (`###`)

- **Usage**: Triple pound signs are a mix between double and quadruple pound signs. They are not limited to a single block of code separated by a blank line, but do not represent an entirely new section. Triple pound signs should be use sparingly and only when necessary, as they don't make it clear when a new "section" is starting or ending. Ideally, you should use double or quadruple pound signs instead.

**NOTE**: The below example is a possible situation in which triple pound signs could be used. Note I'll be using quadruple pound signs, which need a more in depth explanation, which is done in the next section.

///// details | Example

```bash
####[ Variables ]#######################################################################


## Set source and destination directories.
src_dir="/path/to/source"
dest_dir="/path/to/destination"


####[ Main Code ]#######################################################################


###[ Ensure Directories Exist ]###

## Check if source directory exists, exit if not.
if [[ ! -d $src_dir ]]; then
    echo "Source directory does not exist!"
    exit 1
fi

## Check if destination directory exists, create if not.
if [[ ! -d $dest_dir ]]; then
    echo "Destination directory does not exist. Creating now."
    mkdir -p "$dest_dir"
fi

###[ Backup Files and Check Status ]###

cp -r "$src_dir" "$dest_dir"

if [[ $? -eq 0 ]]; then
    echo "Backup successful."
else
    echo "Backup failed."
fi
```

/////
////
///


#### Quadruple Pound Signs

Quadruple pound signs are used to section off entire parts of the script. This can be used to separate functions, variables, or main script logic. Quadruple pound signs can also be used to represent subsections within a section. Quadruple pound signs should be used sparingly and only there is a need to visually separate one section of code from another.

/// admonition | Guidelines
    type: tip

- **Sectioning**: Use quadruple pound signs to separate distinct parts of the script, such as functions, variables, or the main script logic.
- **Subsections**: Quadruple pound signs can also be used to represent subsections within a section, providing a clear visual separation between different parts of the script.
- **Sparingly**: Quadruple pound signs should be used sparingly and only when necessary to visually separate one section of code from another.
- **Formatting**: While not required, there is a recommended way to ensure that they stand out from the rest of the comments. After the quadruple pound signs, `[ Section Name ]` should be appended. Next, enter any number of `#` characters the span the total 88 character limit of a single ling. Finally, above and below this line, there must be two blank lines, unless a subsection is created below, to which two blank lines should be used under the subsection.

///

/// details | Example

```bash
####[ Variables ]#######################################################################
####[[ Modifiable Variables ]]##########################################################


MODIFY_ME="value"


####[[ General Variables ]]#############################################################


general_var="value"


####[ Functions ]#######################################################################


####
# Function description here...
function_name() {
  # Function code here.
}


####[ Main Code ]#######################################################################
#### Main code description here, if necessary...


# Main code here.
```

///

#### Why the Number of Pound Signs Matters

- **Clarity**: Using different numbers of pound signs helps distinguish between comment types, making it easier to identify the comment's scope and purpose.
- **Documentation**: Properly structured comments improve script documentation, making it easier for developers to understand the code's logic and functionality.

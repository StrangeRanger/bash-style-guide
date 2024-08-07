# Aesthetics

As mentioned in the [Preface](../README.md#preface), this guide strives to be as objective as possible by providing well-founded reasons for each recommended practice. However, some practices are purely stylistic and may vary based on personal preference or specific project requirements.

This section addresses these aesthetic choices and offers guidelines for maintaining a consistent and visually appealing script. Where applicable, the guidelines include explanations of their advantages over alternative practices.

For style guidelines that are less subjective and have a greater impact on the script's functionality, please refer to the [Style](style.md) section of this guide.

## Indentations

Indentations primarily come in two forms: tabs and spaces. While both can be used to indent code, spaces are generally preferred.

/// admonition | Guidelines
    type: info

- **Indent Size**: <mark>**_ALWAYS_**</mark> use four spaces per indentation level.
- **Reason**: Four spaces are the standard indentation size in Bash scripting and are widely accepted across the community. It allows a balance between readability and space efficiency.

///

### Why Spaces Over Tabs?

Spaces are favored over tabs because they render uniformly across all editing environments, reducing misalignment issues. Additionally, using spaces can help minimize merge conflicts in version control systems, making them a safer choice for projects with multiple contributors.

## Column Length

The column length refers to the number of characters in a single line of code. Limiting column length significantly enhances the readability of your code.

/// admonition | General Guidelines
    type: info

- **88-Character Limit**: Keep lines within 88 characters. This means breaking long lines into multiple lines when necessary.
    - **Reason**: An 88-character limit ensures that code remains readable on various screen sizes and devices, including terminals, text editors, and code review tools.
- **Exceptions**: Allow exceptions for long strings or complex expressions that require clarity and are more understandable when not broken into multiple lines.

///

### Formatting Multi-lined Commands

When breaking a command that exceeds the 88-character limit into multiple lines, ensuring that it remains readable and easy to follow is crucial. Below are effective guidelines for aligning, indenting, and placing operators (such as `||` and `&&`) in multi-lined commands.

/// admonition | Guidelines
    type: info
//// tab | Indentation and Alignment

- **Indentation**: Use the standard four-space indentation for each continuation line.

///// admonition | Example
    type: example

_Two separate commands with continuation lines indented by four spaces:_

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

- **Placement**: When multiple commands are connected by logical operators (`&&`, `||`), place these operators at the beginning of a new continuation line.
- **Reason**: This formatting ensures that the start of a new command is clearly visible, distinguishing it from the previous command.

///// admonition | Example
    type: example

_Using logical operators with proper indentation:_

```bash
cd /some/directory/in/a/universe/far/far/away \
    && echo "Directory change successful." \
    || echo "Failed to change directories."
```

/////
////
//// tab | Redirect and I/O Operators

- **Placement**: When breaking up a long command that includes redirect or I/O operators (such as `>`, `>>`, `<`, `|`), place these operators at the beginning of a new continuation line.
- **Reason**: This formatting ensures that the redirection or I/O operation is clearly visible, distinguishing it from the previous command.

///// admonition | Example
    type: example

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

- **Readability**: Shorter lines enhance the code's legibility, eliminating the need for horizontal scrolling and making it easier to read on various devices and screen sizes.
- **Compatibility**: A standard line length ensures code displays well in various development tools, including code review platforms and IDEs.

## Function Declaration

While not significantly impacting script readability or functionality, there is a commonly preferred syntax for declaring functions in Bash.

/// admonition | Guidelines
    type: info

- **Syntax**: <mark>**_ALWAYS_**</mark> declare functions using their name followed by parentheses and curly braces (`name() {}`).
- **Avoid `function`**: Avoid using the `function` keyword to declare functions.
- **Reason**: The recommended syntax is more widely accepted and aligns with common Bash practices and community standards.

///

/// details | Example
    type: example

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

## Formatting Block Statements

Blocks statements in Bash, such as `if` statements and loops, can be formatted in multiple ways. Depending on the context, you can choose between standard block statements or single-line block statements. With each approach, maintaining consistency and readability is key.

/// admonition | Guidelines
    type: info

//// tab | Standard Block Statement

- **Inline Placement**: Place `then` on the same line as `if` statements, and `do` on the same line as `for` or `while` loops.
    - **Reason**: Inline placement of block keywords enhances readability and reduces visual clutter, by cutting down on unnecessary line breaks.
- **New Line for Block Endings**: End `if` statements with `fi` and loops with `done` on their own lines.
    - **Reason**: Ending blocks on new lines helps distinguish block statements from the surrounding code and clearly marks the of a control statement.

///// details | Example
    type: example

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

- **Clarity and Maintainability**: Single-line statements should be clear and concise. Always preserve the script’s logical structure to facilitate easier updates and understanding.
- **Avoid Clutter**: Avoid adding multiple commands or complex logic to single-line statements.
    - **Reason**: Single-line block statements are best suited for simple conditions or loops that can be expressed succinctly. Complex logic or multiple commands can make single-line statements less readable.

///// details | Example
    type: example

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

**Advantage**: Single-line block statements are concise and practical for simple conditions or loops.

/////
////
///

## Vertical Spacing Guidelines

Vertical spacing is just as important as horizontal spacing in maintaining a clean and readable script.

/// admonition | Guidelines
    type: info

- **Single Blank Line**: Use a single blank line to separate logical blocks of code or functions, facilitating quick visual parsing.
- **Double Blank Lines**: Use double blank lines sparingly to highlight new sections or distinct logical groups within the script.
    - **Reason**: Double blank lines help visually separate sections of the script that differ significantly in purpose or functionality from the surrounding code.

///

/// details | Example
    type: example

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

- **Readability**: Too many blank lines can make the script harder to read, while too few can make it appear cluttered. Proper vertical spacing strikes a balance between readability and visual organization.

## Comments

Comments are essential for explaining script functionality and enhancing long-term usability and readability. Effective commenting practices ensure scripts are accessible and easily understood by new developers or future maintainers. While similar to previous guidelines, comments have a few additional considerations depending on their context.

/// admonition | General Guidelines
    type: info

- **Capitalization**: Begin each comment with a capital letter, except for code elements like variables or functions.
- **Punctuation**: Conclude comments with a period to indicate completion.
- **Spacing**: For inline comments, maintain two spaces between the code and the comment.
    - **Reason**: While single spaces are sufficient, two spaces provide a clearer visual separation between the code and the comment.

///

/// details | Example
    type: example

```bash
# Assigns "True" to var and demonstrates proper comment formatting.
var=$(echo "True")  # This prints "True".

# Simple variable assignment with a clear, aligned comment.
box="Box"  # This is a box.
```

///

### Function Comments

Functions in Bash differ in a few ways from those in other programming languages, especially regarding argument handling. Due to these differences, functions should be thoroughly documented to clarify their purpose, parameters, and expected behavior.

/// admonition | Guidelines
    type: info

- **Purpose**: Offer a concise description of the function's role and operational context within the script.
- **Parameters**: Detail each parameter the function accepts, noting required versus optional parameters and any default values.
    - **Value Assignment**: <mark>**_CONSIDER_**</mark> assigning parameter values to local variables.
- **Return Values**: Specify the function's output or return values, if applicable.

///

/// details | Example
    type: example

```bash
####
# This function processes files given as parameters and logs results. It is designed to
# handle a variable number of input files and demonstrate proper error handling.
#
# Parameters:
#   - $1: input_file (Required)
#       - Path to the file that needs processing.
#   - $2: log_file (Required)
#       - File where the processing logs are stored.
#   - $3: debug_mode (Optional, Default: false)
#       - If set to true, the function will perform debugging operations.
#
# Returns:
#   - Doesn't directly output information to the console, but rather to a log file...
process_files() {
    local input_file="$1"
    local log_file="$2"
    local debug_mode="${3:-false}"

    # Function code here.
}
```

///

### Pound Signs in Comments

Traditionally, a single pound sign (`#`) is used to denote a comment in Bash scripts. However, using a different number of pound signs can help differentiate the comments' purpose and scope.

/// admonition | Guidelines
    type: info

//// tab | Single Pound Sign (`#`)

- **Usage**: Employ a single pound sign for line-specific comments to explain the purpose of a command or a line of code.

///// details | Example
    type: example

```bash
src_dir="/path/to/source"  # Source directory to back up.
dest_dir="/path/to/destination"  # Destination directory for the backup.

# Create a backup using rsync.
rsync -av --delete "$src_dir" "$dest_dir"
```

/////
////
//// tab | Double Pound Signs (`##`)

- **Usage**: Use double-pound signs (`##`) for comments that describe the functionality of a block of code, such as a loop, conditional sequence, or a list of variables. Ensure that these comments are directly above the relevant code block. If you place a blank line between the comment and the block of code it refers to, the comment is considered to no longer apply to that code, allowing for clear separation between different sections or concepts.

///// details | Example
    type: example

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
//// tab | Triple Pound Signs (`###`)

- **Usage**: Triple pound signs, within the context of this guide, do not have a specific use case. You may choose to use them for a specific purpose in your scripts, but they are not part of the standard commenting practices recommended here.

////
//// tab | Quadruple Pound Signs (`####`)

- **Usage**: Quadruple pound signs are used to section off entire parts of the script.

_Please refer to the [Quadruple Pound Signs](#quadruple-pound-signs) section below for more details._

////
///


#### Quadruple Pound Signs

As mentioned in the previous section, quadruple pound signs are used to section off entire parts of the script.

/// admonition | Guidelines
    type: info

- **Sectioning**: Use quadruple pound signs to separate distinct parts of the script, such as functions, variables, or the main script logic.
- **Subsections**: Quadruple pound signs can also be used to represent subsections within a section, providing a clear visual separation between different parts of the script.
- **Sparingly**: Quadruple pound signs should be used sparingly and only when necessary to visually separate one section of code from another. For example, you can omit them if the script is short and does not require extensive sectioning.
- **Formatting**: While not required, there is a recommended format to ensure that they stand out from the rest of the comments.
  - **Section Naming**: After the quadruple pound signs, append `[ Section Name ]`. Within the brackets (`[]`), include the name that describes the section's content or purpose
  - **Filler Characters**: After the section name, add enough `#` characters to reach the 88-character limit of a single line.
  - **Section Comments**: If necessary, add comments to describe the section's content or purpose, by starting the comment directly below the quadruple pound signs, prefixed with four `#` characters.
  - **Subsection Format**: Subsections should be formatted exactly the same, with the exception of the number of brackets (`[]`) and the section name. In each subsection, the number of brackets should match the subsection's depth within the script.

///

/// details | Example

In the example, note the usage of two blank lines before and after the quadruple pound signs to provide additional visual separation between the sections. This aligns with the [vertical spacing guidelines](#vertical-spacing-guidelines) discussed earlier.

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

- **Clarity**: Using a different number of pound signs helps distinguish between comment types, making it easier to identify the comment's scope and purpose.

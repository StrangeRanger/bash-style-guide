# Aesthetics

As mentioned in the [Preface](../README.md#preface), this guide strives to be as objective as possible by providing well-founded reasons for each recommended practice. However, some practices are purely stylistic and may vary based on personal preference or specific project requirements.

This section addresses these aesthetic choices and offers guidelines for maintaining a consistent and visually appealing script. Where applicable, the guidelines will include explanations of their advantages over alternative practices.

For guidelines that are less subjective and have a greater impact on the script's functionality, please refer to this guide's [Style](style.md) section.

## Indentations

Indentations primarily come in two forms: tabs and spaces. While both can be used to indent code, spaces are generally preferred over tabs.

/// admonition | Guidelines
    type: info

- **Indent Size**: <mark>**_ALWAYS_**</mark> use four spaces per indentation level.
- **Reason**: Four spaces are the standard indentation size in Bash scripting and are widely accepted across the community. It allows a balance between readability and space efficiency.

///

/// admonition | Why Use Spaces Over Tabs?
    type: tip

- **Consistency**: Spaces render uniformly across different editors and environments, preventing misalignment issues.
- **Merge Conflicts**: Spaces help minimize merge conflicts in version control systems, making them a safer choice for collaborative projects.

///

## Column Length

The column length refers to the number of characters in a single line of code. Limiting the column length can significantly enhance the readability of your code.

/// admonition | General Guidelines
    type: info

- **88-Character Limit**: Keep the column length within 88 characters. This means breaking lines exceeding the limit into multiple lines when necessary.
- **Exceptions**: Allow exceptions for long strings or complex expressions that are more understandable when not broken into multiple lines.

///

/// admonition | Why Limit Column Length?
    type: tip

- **Readability**: Shorter lines enhance the code's legibility, eliminating the need for horizontal scrolling and making it easier to read on various devices and screen sizes.
- **Compatibility**: A standard line length ensures code displays well in various development tools, including code review platforms and IDEs.

///

### Formatting Multi-lined Commands

When breaking a command that exceeds the 88-character limit into multiple lines, it is crucial to ensure that it remains readable and easy to follow. Below are practical guidelines for aligning, indenting, and placing operators, such as `||` and `&&`, in multi-lined commands.

/// admonition | Guidelines
    type: info
//// tab | Indentation and Alignment

- **Indentation**: Use the standard four-space indentation for each continuation line.

///// admonition | Example
    type: example

```bash
rsync -avz /source/directory/with/a/very/long/path/ \
    /destination/directory/with/another/long/path/
```

/////
////
//// tab | Logical Operators

- **Placement**: When multiple commands are connected by logical operators (`&&`, `||`), place these operators at the beginning of a new continuation line.
- **Reason**: This formatting ensures that the start of a new command is clearly visible, distinguishing it from the previous command.

///// details | Example
    type: example

**NOTE**: Either of the following examples are valid, thought the second one is generally preferred for its readability and ease to follow.

---

_Example 1:_

```bash
mkdir /path/to/backup && rsync -av --delete /path/to/source/ /path/to/backup/ \
    || echo "Backup failed" >> error.log
```

---

_Example 2:_

```bash
mkdir /path/to/backup \
    && rsync -av --delete /path/to/source/ /path/to/backup/ \
    || echo "Backup failed" >> error.log
```

/////
////
//// tab | Redirect and I/O Operators

- **Placement**: When breaking up a long command that includes redirect or I/O operators (`>`, `>>`, `<`, `|`), place these operators at the beginning of a new continuation line.
- **Reason**: This formatting ensures that the redirection or I/O operation is clearly visible, distinguishing it from the previous command.

///// details | Examples
    type: example

**NOTE**: Either of the following examples are valid, though the second or third ones generally preferred for their readability and ease to follow.

---

_Example 1:_

```bash
grep -r "TODO" /path/to/project | grep -v "DONE" | awk '{print $1, ":", $2}' | sort \
    | uniq > todo_list.txt
```

---

_Example 2:_

```bash
grep -r "TODO" /path/to/project \
    | grep -v "DONE" \
    | awk '{print $1, ":", $2}' \
    | sort \
    | uniq \
    > todo_list.txt
```

---

_Example 3:_

```bash
grep -r "TODO" /path/to/project \
    | grep -v "DONE" \
    | awk '{print $1, ":", $2}' \
    | sort \
    | uniq > todo_list.txt
```

/////
////
///

## Function Declaration

While not significantly impacting script readability or functionality, there is a commonly preferred syntax for declaring functions in Bash.

/// admonition | Guidelines
    type: info

- **Syntax**: <mark>**_ALWAYS_**</mark> declare functions using their name followed by parentheses and curly braces (i.e., `name() {}`).
- **Avoid `function`**: Avoid using the `function` keyword to declare functions.
- **Reason**: The recommended syntax is more widely accepted and aligns with standard Bash practices.

///

/// details | Example
    type: example

_Preferred syntax for declaring functions:_

```bash
greet() {
    echo "Hello, $1!"
}

greet "World"
```

---

_Using the `function` keyword:_

```bash
function greet() {
    echo "Hello, $1!"
}

greet "World"
```

///

## Formatting Block Statements

<!-- TODO: Edit the wording of this section and the examples. -->

Block statements in Bash, such as `if` statements and loops, can be formatted in multiple ways. Depending on the context, you can choose between standard block statements or single-line block statements. With each approach, maintaining consistency and readability is key. In addition, continuation lines are slightly different for block statements compared to regular commands.

/// admonition | Guidelines
    type: info
//// tab | Standard Block Statement

- **Inline Placement**: Place `then` on the same line as `if` statements, and `do` on the same line as `for` or `while` loops.
- **New Line for Block Endings**: End `if` statements with `fi` and loops with `done` on their own lines.
- **Reason**: These guidelines follow standard Bash practices and enhance script readability.

///// details | Example
    type: example

```bash
if [[ $1 -eq 1 ]]; then
    echo "You entered one."
else
    echo "You entered something else."
fi

for file in *.txt; do
    echo "Processing $file"
done
```

/////
////
//// tab | Continuation Lines

- **Indentation**: <mark>**_ALWAYS_**</mark> use eight spaces for each continuation line of the block statement.
- **Reason**: Using eight spaces for continuation lines creates a clear visual distinction between the logical conditions and the code within the block. If a standard four-space indentation were used, continuation lines will align with the block's code, making it harder to distinguish between them. While the guideline of placing operators at the beginning of continuation lines already aids readability, the additional indentation further enhances the clarity and separation between the logic and the block's content.

///// details | Example
    type: example

```bash
if [[ $exit_code == "1" && $display_message == "true" ]]; then
    echo "${C_RED}==>${C_NC} A fatal error occurred."
elif [[ ($exit_code == "130" ||  $exit_code == "143") \
        && $display_message == "true" ]]; then
    echo ""
    echo "${C_YELLOW}==>${C_NC} User interruption detected."
fi
```

/////
////
//// tab | Single Line Block Statement

- **Clarity and Maintainability**: Single-line statements should be clear and concise. Always preserve the script’s logical structure to facilitate easier updates and understanding.
- **Avoid Clutter**: Avoid adding multiple commands or complex logic to single-line statements.
    - **Reason**: Single-line block statements are best suited for simple conditions or loops that can be expressed succinctly. Complex logic or multiple commands can make single-line statements less readable.

///// details | Example
    type: example

_Example of a single-line block statement:_

```bash
[[ $1 -eq 1 ]] && echo "You entered one."

for file in *.txt; do echo "Processing $file"; done
```

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

/// admonition | Why Vertical Spacing Matters?
    type: tip

- **Readability**: Too many blank lines can make the script harder to read, while too few can make it appear cluttered. Proper vertical spacing strikes a balance between readability and visual organization.

///

/// details | Example
    type: example

```bash
add() {
    local sum=$(( $1 + $2 ))
    echo "Sum: $sum"
}

subtract() {
    local difference=$(( $1 - $2 ))
    echo "Difference: $difference"
}


echo "Starting the script..."
add 5 3
subtract 10 4
echo "Script finished."
```

///

## Comments

Comments are essential for explaining script functionality and enhancing long-term usability and readability. Effective commenting practices ensure scripts are accessible and easily understood by new developers or future maintainers. While similar to previous guidelines, comments have a few additional considerations depending on their context.

/// admonition | General Guidelines
    type: info

- **Capitalization**: Begin each comment with a capital letter, except for code elements like variables or functions.
- **Punctuation**: Conclude comments with a period to indicate a complete thought.
- **Spacing**: For inline comments, maintain two spaces between the code and the comment.
    - **Reason**: While single spaces are sufficient, two spaces provide a clearer visual separation between the code and the comment.

///

/// details | Example
    type: example

```bash
# This is a comment explaining the next line of code.
variable="value"  # Inline comment with two spaces before it.
```

///

### Function Comments

Functions in Bash differ in a few ways from those in other programming languages, especially regarding argument handling. Due to these differences, functions should be thoroughly documented to clarify their purpose, parameters, and expected behavior.

/// admonition | Guidelines
    type: info

- **Purpose**: Describe the function's role and purpose within the script.
- **Parameters**: Detail each parameter the function accepts, noting required and optional parameters and any default values.
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
# Output:
#   - Logs the processing results to the specified log file.
process_files() {
    local input_file="$1"
    local log_file="$2"
    local debug_mode="${3:-false}"

    if [ "$debug_mode" = true ]; then
        echo "Debug mode enabled. Processing $input_file..." >> "$log_file"
    fi

    echo "Processing completed for $input_file" >> "$log_file"
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
# This command lists all files in the directory.
ls -la
```

/////
////
//// tab | Double Pound Signs (`##`)

<!-- TODO: Potentially reword the following guidelines. -->

- **Usage**: Use double-pound signs for comments that describe the functionality of a block of code, such as a loop, conditional sequence, or a list of variables. Ensure that these comments are directly above the relevant code block. If you place a blank line between the comment and the block of code it refers to, the comment is considered to no longer apply to that code, allowing for clear separation between different sections or concepts.

///// details | Example
    type: example

```bash
## Loop through all .txt files in the directory.
for file in *.txt; do
    # Process each file.
    echo "Processing $file"
done
```

/////
////
//// tab | Quadruple Pound Signs (`####`)

<!-- TODO: Potentially reword the following guidelines. -->

- **Usage**: Use quadruple pound signs to separate distinct parts of the script, such as functions, variables, or the main script logic.
- **Subsections**: Quadruple pound signs can also represent subsections within a section of the script.
- **Sparingly**: Quadruple pound signs should be used sparingly and only when necessary to visually separate one section of code from another. For example, you can omit them if the script is short and does not require extensive sectioning.
- **Formatting**: Quadruple pound signs should be formatted to make it stand out from the rest of the comments. Below are the suggested formatting guidelines:
    - **Section Naming**: Append `[ Section Name ]` after the quadruple pound signs. Within the brackets (`[]`), include the name that describes the section's content or purpose.
    - **Filler Characters**: After the section name, add enough `#` characters to reach the 88-character limit of a single line.
    - **Section Comments**: If necessary, add comments describing the section's content or purpose by starting the comment below the quadruple pound signs, prefixed with four `#` characters.
    - **Spacing**: As mentioned in the [vertical spacing guidelines](#vertical-spacing-guidelines), two blank lines before and after the quadruple pound signs should be provided to provide additional visual separation between the sections.
    - **Subsection Format**: Subsections should be formatted the same, except for the number of brackets (`[]`) and the section name. The number of brackets in each subsection should match the subsection's depth within the script.

///// details | Example
    type: example

```bash
####[ Variables ]#######################################################################
####[[ Modifiable Variables ]]##########################################################


MODIFY_ME="value"


####[[ General Variables ]]#############################################################


general_var="value"


####[ Functions ]#######################################################################


####
# Function description...
process_files() {
    # Function logic here...
}


####[ Main Code ]#######################################################################
#### Main code description here, if necessary...


# Main execution starts here.
echo "Starting script execution..."

# Call a function to process files.
process_files "input.txt" "log.txt"
```
/////
////
//// tab | Triple Pound Signs (`###`)

- **Description**: Tipple pound signs can be thought of as a middle ground between double and quadruple pound signs. While they aren't limited to a single block of code seperated by a blank line, they do not represent an entirely new section of the script.
- **Usage**: Use triple pound signs were blocks or lines of code are different enough or require some kind of distinction, but do not require a completely new section.
- **Formatting**: Similar to quadruple pound signs, triple pound signs should be formatted in a way that makes them stand out from the rest of the comments. Below are the suggested formatting guidelines:
    - **Section Naming**: After the triple pound signs, append `[ Section Name ]`. Within the brackets (`[]`), include the name that describes the section's content or purpose
    - **Filler Characters**: Directly above and below the section name, There should exist 3 `#` characters to provide a clear visual separation between the sections.
    - **Spacing**: Above and below the filler characters, there should be a single blank line that separates the previous command, triple pounds sign comments, and the next command.
    - **Section Comments**: If necessary, add comments to describe the section's content or purpose, by starting the comment directly below the triple pound signs, prefixed with three `#` characters.

///// details | Example
    type: example

```bash
####[ Global Variables ]################################################################


background_jobs=()

###
### [ Configurable Variables ]
### The following variables can be modified to suit your needs.
###

# The maximum number of concurrent pings to run.
C_MAX_CONCURRENT_PINGS=255

###
### [ Constants ]
###

## Variables to colorize the output.
C_YELLOW="$(printf '\033[1;33m')"
C_GREEN="$(printf '\033[0;32m')"
C_BLUE="$(printf '\033[0;34m')"
C_CYAN="$(printf '\033[0;36m')"
C_RED="$(printf '\033[1;31m')"
C_NC="$(printf '\033[0m')"
C_CLRLN="$(printf '\r\033[K')"
readonly C_YELLOW C_GREEN C_BLUE C_CYAN C_RED C_NC C_CLRLN


####[ Functions ]#######################################################################

........
```

/////
////
///


#### Why the Number of Pound Signs Matters

- **Clarity**: Using a different number of pound signs helps distinguish between comment types, making it easier to identify the comment's scope and purpose.

# Aesthetics

As mentioned in the [Preface](../index.md#preface), this guide aims to be as objective as possible by providing well-founded reasons for each recommended practice. However, some practices are purely stylistic and may vary based on personal preference or specific project requirements.

This section addresses these aesthetic choices and offers guidelines for maintaining a consistent and visually appealing script. Where applicable, the guidelines will include explanations of their advantages over alternative practices.

For guidelines style guidelines that have a significant impact on the script's functionality, please refer to this guide's [Style](style.md) section.

## Indentations

Indentation in Bash scripts can be achieved using either tabs or spaces. However, spaces are generally preferred over tabs for consistent code formatting.

/// admonition | Guidelines
    type: info

- **Indent Size**: <mark>**_ALWAYS_**</mark> use **four spaces** per indentation level.
    - **Reason**: Four spaces is the standard indentation size in Bash scripting, widely accepted across the community. This size strikes a balance between readability and space efficiency.

///

/// admonition | Why Use Spaces Over Tabs?
    type: tip

- **Consistency**: Spaces render uniformly across different editors and environments, ensuring consistent alignment and preventing issues caused by varying tab widths.
- **Merge Conflicts**: Spaces help minimize merge conflicts in version control systems, making them a safer choice for collaborative projects where multiple contributors might use different editors or settings.

///

## Column Length

Column length refers to the number of characters in a single line of code. Limiting the column length significantly improves the readability of your code.

/// admonition | General Guidelines
    type: info

- **88-Character Limit**: Keep the column length within 88 characters. Break lines that exceed this limit into multiple lines when necessary.
- **Exceptions**: Allow exceptions for long strings or complex expressions that are more understandable when kept on a single line.

///

/// admonition | Why Limit Column Length?
    type: tip

- **Readability**: Shorter lines enhance readability by reducing the need for horizontal scrolling, especially when viewing code on smaller screens or in terminal windows.
- **Compatibility**: A consistent column length ensures that code displays well across various development tools, including code review platforms and IDEs.

///

### Formatting Multi-lined Commands

When breaking a command that exceeds the 88-character limit into multiple lines, it is crucial to ensure that it remains readable and easy to follow. Below are practical guidelines for aligning, indenting, and placing operators, such as `||` and `&&`, in multi-lined commands.

/// admonition | Guidelines
    type: info
//// tab | Indentation and Alignment

<!-- TODO: Maybe remove the reason or explanation? -->

- **Indentation**: Use the standard four-space indentation for each continuation line.
    - **Reason**: Consistent indentations help maintain readability by visually separating the continuation lines from the preceding lines.

///// admonition | Example
    type: example

```bash
rsync -avz /source/directory/with/a/very/long/path/ \
    /destination/directory/with/another/long/path/
```

---

**Explanation**: The second line is indented by four spaces, clearly indicating that it is a continuation of the previous line.

/////
////
//// tab | Logical Operators

- **Placement**: When **three or more** commands are connected by logical operators (`&&`, `||`), place each operator at the beginning of a new continuation line. This applies even if the total line length is within the 88-character limit.
    - **Reason**: Placing logical operators at the beginning of a new line makes the start of each command clear, enhancing readability and reducing the risk of errors.
- **Exception 1**: When two commands are connected by a single logical operator (`&&`, `||`) and the combined line length **does not** exceed the 88-character limit, it is acceptable to keep them on the same line.
    - **Reason**: In this case, keeping the two commands on the same line maintains clarity and simplicity without compromising readability.
- **Exception 2**: If a command is followed by a single `||`, and the subsequent operations related to the `||` span multiple lines, it is acceptable to place the `||` at the end of the initial command, followed by `{` on the same line. The closing `}` should be on a new line, with the operations for the `||` between the braces on their own lines.
    - **Reason**: This exception allows for a simpler and more concise structure when the `||` operation handles a single failure condition. In these cases, alternative formatting might add unnecessary complexity without improving clarity, making this approach more readable and straightforward.
    - **Example of acceptable formatting**:
        ```bash
        cp /some/config/file /some/config/file.bak || {
            echo "Failed to back up 'file'" >&2
            echo "Please create a backup of the original 'file' before continuing"
            exit 1
        }
        ```
        **Explanation**: Here, the `||` operator is directly followed by `{`, which starts a block of commands. This structure is acceptable as it maintains clarity even with the multi-line continuation.
    - **Example of formatting that is not acceptable**:
        ```bash
        rm /some/config/file.bak && cp /some/config/file /some/config/file.bak || {
            echo "Failed to overwrite backup of 'file'" >&2
            exit 1
        }
        ```
        **Explanation**: In this scenario, the `&&` and `||` operators are on the same line, making it difficult to distinguish between commands. The preferred formatting for this scenario is as follows:
        ```bash
        rm /some/config/file.bak \
            && cp /some/config/file /some/config/file.bak \
            || {
                echo "Failed to overwrite backup of 'file'" >&2
                exit 1
            }
        ```
        **Explanation**: This formatting places each logical operator at the beginning of a new line, ensuring that the sequence of operations is clear and easy to follow.

///// details | General Examples
    type: example

_Example 1:_

```bash
mkdir /path/to/backup && echo "Backup successful"
```

**Explanation**: This example shows two commands connected by a single logical operator (`&&`). Since the combined line length does not exceed the 88-character limit, it is acceptable to keep them on the same line.

---

_Example 2:_

```bash
mkdir /path/to/backup \
    && rsync -av --delete /path/to/source/ /path/to/backup/ \
    || echo "Backup failed" >> error.log
```

**Explanation**: Here, three commands are connected by logical operators (`&&` and `||`). The operators are placed at the beginning of new lines to enhance readability and ensure the sequence of operations is easy to follow.

/////
////
//// tab | Redirect and I/O Operators

- **Placement**: When breaking up a long command that includes redirect or I/O operators (`>`, `>>`, `<`, `|`), place these operators at the beginning of a new continuation line.
    - **Reason**: Placing redirect or I/O operators at the beginning of a new line makes them clearly visible, distinguishing them from the preceding command and improving readability.
- **Single-Line Placement**:  the command(s) following the redirect or I/O operator are short and can fit on the same line without exceeding the 88-character limit, it is acceptable to keep them on the same line. However, this should be done with care, as it *can* reduce readability.
    - **Reason**: In many cases, keeping the command and the redirect or I/O operator on the same line won't drastically impact readability, especially when the subsequent command is short. However, this practice should be used judiciously to maintain clarity.

///// details | Examples
    type: example

**NOTE**: The second and third examples enhance readability by separating each command and its associated operators, making the code easier to follow at a glance. The first example, while valid, may be less clear due to the compact placement of multiple operators on a single line.

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

---

**Explanation**: The second and third examples clearly separate each command and its associated operators, making the code easier to understand at a glance.

/////
////
///

## Function Declaration

While function declaration syntax in Bash may not significantly impact script readability or functionality, there is a preferred style that aligns with best practices for clarity and consistency.

/// admonition | Guidelines
    type: info

- **Syntax**: <mark>**_ALWAYS_**</mark> declare functions using their name followed by parentheses and curly braces (`name() {}`).
    - **Reason**: This syntax is concise, widely accepted within the Bash community, and aligns with established best practices in Bash scripting.
- **Avoid `function`**: Avoid using the `function` keyword to declare functions.
    - **Reason**: The `function` keyword is redundant in Bash, as the name() {} syntax is sufficient for declaring functions. Using name() {} keeps the code cleaner and avoids unnecessary verbosity.


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

**Explanation**: This example uses the recommended `name() {}` syntax, which is straightforward and efficient.

---

_Using the `function` keyword:_

```bash
function greet() {
    echo "Hello, $1!"
}

greet "World"
```

**Explanation**: While this syntax is valid in Bash, it adds unnecessary complexity without providing additional benefits.

///

## Formatting Block Statements

Block statements in Bash, such as `if` statements and loops, can be formatted in multiple ways. Depending on the context, you can choose between a standard or single-line block statement. In either case, maintaining consistency and readability is key.


/// admonition | Guidelines
    type: info
//// tab | Standard Block Statement

- **Inline Placement**: Place `then` on the same line as `if` statements, and `do` on the same line as `for` or `while` loops.
    - **Reason**: This format is consistent with standard Bash practices, ensuring that the structure of the block statement is clear and easy to follow.
- **New Line for Block Endings**: End `if` statements with `fi` and loops with `done` on their own lines.
    - **Reason**: Placing `fi` and `done` on their own lines makes the end of the block statement clear, enhancing readability and making the code easier to maintain.
- **Indentation**: <mark>**_ALWAYS_**</mark> use eight spaces for each continuation line of a block statement.
    - **Reason**: Using eight spaces for continuation lines creates a clear visual distinction between the logical conditions and the code within the block. This practice ensures that the logical structure of the block is easy to differentiate from its contents, improving overall readability.

///// details | Example
    type: example

_Standard block statement formatting:_

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

---

_Eight-space indentation for continuation lines:_

```bash
if [[ $exit_code == "1" && $display_message == "true" ]]; then
    echo "${C_RED}==>${C_NC} A fatal error occurred."
elif [[ ($exit_code == "130" ||  $exit_code == "143") \
        && $display_message == "true" ]]; then
    echo ""
    echo "${C_YELLOW}==>${C_NC} User interruption detected."
fi
```

**Explanation**: The eight-space indentation makes the conditions and their corresponding actions easy to distinguish, especially in complex logical structures.

/////
////
//// tab | Single Line Block Statement

- **Clarity and Maintainability**: Single-line block statements should be clear and concise.
    - **Reason**: Single-line block statements are best suited for simple conditions or loops that can be expressed succinctly. This prevents clutter and ensures the code remains readable.
- **Avoid Clutter**: Avoid adding multiple commands or complex logic to single-line statements.
    - **Reason**: Complex logic or multiple commands in a single-line block can reduce readability and make the code harder to maintain.
- **Block Ending Placement**: Place the `fi` or `done` on the same line as the single-line block statement.
    - **Reason**: Keeping the block's ending (`fi` or `done`) on the same line as the command makes it clear that the block is simple and self-contained.
- **When to Avoid**: Avoid using single-line block statements if they cannot fit within the 88-character limit.
    - **Reason**: If a single-line block statement exceeds the 88-character limit, it compromises readability. In such cases, the block should be expanded into multiple lines.

///// details | Example
    type: example

_Example of a single-line block statement:_

```bash
[[ $1 -eq 1 ]] && echo "You entered one."

for file in *.txt; do echo "Processing $file"; done
```

**Explanation**: These examples show how to use single-line block statements for straightforward logic, keeping the code clean and easy to read.

/////
////
///

## Vertical Spacing

Vertical spacing is just as important as horizontal spacing in maintaining a clean and readable script. Proper use of vertical spacing helps to visually organize code, making it easier to follow and maintain.

/// admonition | Guidelines
    type: info

- **Single Blank Line**: Use a single blank line to separate logical blocks of code or functions.
    - **Reason**: A single blank line helps to visually separate related pieces of code, making the flow of logic clear without wasting space.
- **Double Blank Lines**: Use double blank lines sparingly to highlight new sections or distinct logical groups within the script.
    - **Reason**: Double blank lines create a more pronounced visual separation, which is useful for distinguishing sections that serve different purposes or contain unrelated functionality. This can be particularly helpful in larger scripts to prevent sections from blending together.

///

/// admonition | Why Vertical Spacing Matters?
    type: tip

- **Readability**: Proper vertical spacing is key to balancing readability and organization. Too many blank lines can make the script appear disjointed, while too few can make it look cluttered. Striking the right balance ensures that the script is easy to read and logically organized.

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

**Explanation**: In this example, a single blank line is used to separate the add and subtract functions. Double blank lines are used before the `echo "Starting the script..."` line, signifying a transition from function definitions to the main execution block of the script. This approach keeps the script well-organized and easy to follow.

///

## Comments

Comments are essential for explaining script functionality and enhancing long-term usability. Effective commenting practices ensure scripts are accessible and easily understood by new developers or future maintainers. While similar to previous guidelines, comments have a few additional considerations depending on their context.

/// admonition | General Guidelines
    type: info

- **Capitalization**: Begin each comment with a capital letter, except for code elements like variables or functions.
- **Punctuation**: Conclude comments with a period to indicate a complete thought.
- **Spacing**: For inline comments, maintain two spaces between the code and the comment.
    - **Reason**: While single spaces are sufficient, two spaces provide a clearer visual separation between the code and the comment, enhancing readability.

///

/// details | Example
    type: example

```bash
# This is a comment explaining the next line of code.
variable="value"  # Inline comment with two spaces before it.
```

///

### Function Comments

Functions in Bash differ from those in other languages, especially regarding argument handling. Due to these differences, functions should be thoroughly documented to clarify their purpose, parameters, and expected behavior.

/// admonition | Guidelines
    type: info

<!-- TODO: Re-review these guidelines for clarity and consistency. -->

- **Purpose**: Clearly describe the function's role and purpose within the script.
    - **Reason**: Documenting the purpose helps others (and yourself) quickly understand what the function is intended to do, making the script easier to maintain and debug.
- **Global Variables**: If the function uses global variables, mention them in the comments.
    - **Reason**: Explicitly stating the global variables the function depends on improves transparency and reduces the risk of unintended side effects when modifying the script.
    - **Note**: You do not need to explain how the global variables are used, only that they are used.
- **Parameters**: Detail each parameter the function accepts, noting whether they are required or optional, and specify any default values.
    - **Reason**: Clearly defining the function's parameters helps users understand how to interact with the function and what values are expected.
    - **Value Assignment**: Assign parameter values to local variables.
        - **Reason**: Assigning parameters to local variables helps prevent accidental changes to the original values and enhances readability by making the function’s logic clearer.
- **Output and Return**: Specify the function's output and return values.
    - **Reason**: Describing the function's output and return values helps users understand what to expect when calling the function and how to handle the results.
///

/// details | Example
    type: example

<!-- TODO: Modify the example to include all the guidelines. -->

```bash
####
# This function processes files given as parameters and logs results. It is designed to
# handle a variable number of input files and demonstrate proper error handling.
#
# PARAMETERS:
#   - $1: input_file (Required)
#       - Path to the file that needs processing.
#   - $2: log_file (Required)
#       - File where the processing logs are stored.
#   - $3: debug_mode (Optional, Default: false)
#       - If set to true, the function will perform debugging operations.
#       - Accepted values:
#           - true: Enable debug mode.
#           - false: Disable debug mode.
#
# OUTPUTS:
#   Logs the processing results to the specified log file.
process_files() {
    local input_file="$1"
    local log_file="$2"
    local debug_mode="${3:-false}"

    if [[ $debug_mode == true ]]; then
        echo "Debug mode enabled" >> "$log_file"
    elif [[ $debug_mode == false ]]; then
        echo "Debug mode disabled" >> "$log_file"
    else
        echo "Invalid debug mode value. Defaulting to disabled." >> "$log_file"
        debug_mode=false
    }

    echo "Processing '$input_file'..." >> "$log_file"
    echo "Processing completed for '$input_file'" >> "$log_file"
}
```

///

### Pound Signs in Comments

<!-- TODO: Consider adding explanations below each code example in this sub-section. -->

Traditionally, a single pound sign (`#`) is used to denote a comment in Bash scripts. However, using different numbers of pound signs can help differentiate the comments' purpose and scope.

/// admonition | Guidelines
    type: info
//// tab | Single Pound Sign (`#`)

- **Usage**: Use a single pound sign for line-specific comments to explain the purpose of a command or a line of code.

///// admonition | Example
    type: example

```bash
# This command lists all files in the directory.
ls -la
```

/////
////
//// tab | Double Pound Signs (`##`)

- **Usage**: Use double pound signs for comments that describe the functionality of a block of code, such as a loop, conditional sequence, or a group of related variable declarations. These comments should be placed directly above the relevant code block.
    - **Blank Lines**: If a blank line is placed between the comment and the block of code it refers to, the comment is considered to no longer apply to that code, allowing for clear separation between different logical units or groups of code.

///// admonition | Examples
    type: example

```bash
## Set variables for the script.
var_one="value_one"
var_two="value_two"
var_three="value_three"

## Loop through all .txt files in the directory.
for file in *.txt; do
    # Process each file.
    echo "Processing $file"
done
```

/////
////
//// tab | Quadruple Pound Signs (`####`)

- **Usage**: Use quadruple pound signs to separate distinct parts of the script, such as functions, variable declarations, or main script logic.
- **Sparingly**: Quadruple pound signs should be used sparingly, primarily when it's necessary to visually distinguish one major section of code from another. In shorter scripts, extensive use of quadruple pound signs for sectioning may be unnecessary.
- **Subsections**: Quadruple pound signs can also be used to indicate subsections within a larger section of the script. However, this should be done even more sparingly. Consider using triple pound signs before resorting to quadruple pound signs for subsections.
- **Formatting**: Quadruple pound signs should be formatted to stand out from other comments. Below are the suggested formatting guidelines:
    - **Section Naming**: Append `[ Section Name ]` after the quadruple pound signs. The section name should describe the content or purpose of that section.
    - **Filler Characters**: After the section name, add enough `#` characters to reach the 88-character line limit.
    - **Section Comments**: If additional explanation is needed, add comments describing the section's content or purpose below the quadruple pound signs, prefixed with four `#` characters.
    - **Spacing**: As recommended in the [vertical spacing guidelines](#vertical-spacing), provide two blank lines before and after the quadruple pound signs to enhance visual separation between sections.
    - **Subsection Format**: Subsections should follow the same format, with a varying number of brackets (`[]`) to represent the depth within the script. Use filler characters to maintain a consistent 88-character width.

///// details | Example
    type: example

```bash
####[ Variables ]#######################################################################
####[[ Modifiable Variables ]]##########################################################


C_MODIFY_ME="value"


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

- **Description**: Triple pound signs serve as a middle ground between double and quadruple pound signs. They are used when blocks or lines of code require some distinction but do not necessitate a completely new section.
- **Usage**: Use triple pound signs where the code is different enough to warrant distinction but not significant enough to be placed in an entirely new section.
- **Formatting**: Triple pound signs should be formatted to stand out from other comments. Below are the suggested formatting guidelines:
    - **Section Naming**: Append `[ Section Name ]` after the quadruple pound signs. The section name should describe the content or purpose of that section.
    - **Filler Characters**: Place three `#` characters directly above and below the section name to provide clear visual separation.
    - **Spacing**: Include a single blank line above and below the filler characters, separating the previous command, the triple pound sign comment, and the next command.
    - **Section Comments**: If necessary, add comments to describe the section's content or purpose, prefixed with three `#` characters.

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

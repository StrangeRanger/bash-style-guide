# Aesthetics

As mentioned in the [Preface](../index.md#preface), this guide aims to be as objective as possible by providing well-founded reasons for each recommended practice. However, some practices are purely stylistic and may vary based on personal preference or specific project requirements.

This section addresses these aesthetic choices and offers guidelines for maintaining a consistent and visually appealing script. Where applicable, the guidelines will include explanations of their advantages over alternative practices.

For stylistic guidelines that significantly impact a script's functionality, please refer to this guide's [Style](style.md) section.

## Indentations

Indentations can be achieved by using either tabs or spaces. In most languages, including Bash, spaces are the preferred method for ensuring consistent formatting.

/// admonition | Guidelines
    type: info

- **Indent Size**: <mark>**_ALWAYS_**</mark> use four spaces per indentation level.
    - **Reason**: Four spaces are the standard indentation size in Bash scripting and is widely accepted across the community. This size strikes a balance between readability and an efficient use of space.

///

/// admonition | Why Use Spaces Over Tabs?
    type: tip

- **Consistency**: Spaces render uniformly across different editors and environments, ensuring consistent alignment and preventing issues caused by differing tab widths.
- **Merge Conflicts**: Spaces help minimize merge conflicts in version control systems, making them ideal for collaborative projects where multiple contributors might use different editors or settings.

///

## Characters Per Line

Historically speaking, the maximum number of Characters Per Line (CPL) was set at 80 due to the constraints of older terminals. While modern terminals and editors can display more characters per line, adhering to a reasonable limit remains beneficial for maintaining readability, consistency, and ease of maintenance across different environments and tools.

/// admonition | General Guidelines
    type: info

- **88-Character Limit**: The maximum CPL for Bash scripts is 88 characters.
    - **Reason**: The 88-character limit offers a slight increase over the traditional 80-character limit, allowing for more descriptive variable names, comments, or longer strings without sacrificing readability.
- **Exceptions**: The CPL limit may be exceeded in specific cases where breaking a line greatly reduces readability or negatively impacts the script's structure. These exceptions should be rare and well-justified. (1)
    { .annotate }

    1. **Exception Note**: There will also be cases where the 88-character limit is not feasible due to the nature of the code or the complexity of the logic. In such instances, it is essential to prioritize functionality and maintainability over strict adherence to the CPL limit.

///

/// admonition | Why Does A CPL Limit Matter?
    type: tip

- **Readability**: Shorter lines improve readability by reducing the need for horizontal scrolling and minimizing word wrapping issues. This also facilitates easier code comparison when using split-screen editors or working with multiple files simultaneously.
- **Tool Compatibility**: Maintaining consistent line lengths ensures that code is displayed uniformly across different development environments, such as code review platforms, IDEs, and terminals, preventing layout issues and preserving the intended structure.

///

### Formatting Multi-lined Commands

Maintaining a clear and readable format is important when breaking up commands that exceed the CPL limit. Below are some practical guidelines for aligning continuation lines and placing operators like `||` and `&&` in multi-lined commands.

/// admonition | Guidelines
    type: info
//// tab | Indentation and Alignment

- **Indentation**: Use the [standard four-space indentation](#indentations) for each continuation line.
    - **Reason**: Consistent indentations enhance readability by ensuring predictable alignment and structure in multi-lined commands.

///// admonition | Example
    type: example

```bash
rsync -avz /source/directory/with/a/very/long/path/ \
    /destination/directory/with/another/long/path/
```

/////
////
//// tab | Logical Operators

- **Two or More Operators**: When two or more logical operators (`&&`, `||`) join a sequence of commands, place each operator at the beginning of a new continuation line. This applies even if the CPL limit has yet to be reached.
    - **Reason**: Too many operators on a single line can make each operation harder to distinguish. Placing each logical operator at the beginning of a continuation line makes each command easier to identify. This practice ensures that the sequence of operations is easy to follow, even in complex command chains.
- **Single Operator**: When a single logical operator (`&&` or `||`) connects two separate commands and the combined length doesn't exceed the CPL limit, both commands **MAY** remain on the same line.
    - **Reason**: This guideline can enhance readability and conciseness, especially when the commands are short, and no other operators are involved. Additionally, separating the two commands can often introduce unnecessary complexity with few benefits.
- **Exception**: When a command is followed by a single `||` operator and the subsequent operations span multiple lines, it is acceptable to place `|| {` at the end of the initial command. The closing bracket (`}`) should be on a new line, with the operations for `||` placed between the braces.
    - **Reason**: This format keeps the structure concise when handling a single failure condition with `||`. Other formatting options could introduce unnecessary complexity without enhancing clarity, making this approach more straightforward and readable.
    - **Example of acceptable formatting**:
        ```bash
        cp /some/config/file /some/config/file.bak || {
            echo "Failed to back up 'file'" >&2
            echo "Please create a backup of the original 'file' before continuing"
            exit 1
        }
        ```
        **Explanation**:  In this example, the `||` operator is immediately followed by `{`, which indicates the start of a block of commands to be performed if the initial `cp` command fails. The closing `}` is placed on a new line. This structure maintains clarity even when the operations within the block span multiple lines.
      - **Example of formatting that is not acceptable**:
        ```bash
        rm /some/config/file.bak && cp /some/config/file /some/config/file.bak || {
            echo "Failed to overwrite backup of 'file'" >&2
            exit 1
        }
        ```
        **Explanation**: In this scenario, the `&&` and `||` operators are placed on the same line, which violates the "Two or More Operators" guideline. This formatting can make the sequence of operations harder to follow or slightly disjointed. The preferred formatting for this situation is:
        ```bash
        rm /some/config/file.bak \
            && cp /some/config/file /some/config/file.bak \
            || {
                echo "Failed to overwrite backup of 'file'" >&2
                exit 1
            }
        ```
        **Explanation**: This formatting places each logical operator at the beginning of a continuation line, making the sequence of operations more visible.

///// details | General Examples
    type: example

<!-- TODO: Maybe add an explanation to the examples? -->

_Example 1:_

```bash
[[ -f /some/location/of/file.txt ]] && rm -f /some/location/of/file.txt
```

---

_Example 2:_

```bash
mkdir -p /path/to/backup \
    && rsync -av --delete /path/to/source/ /path/to/backup/ \
    || echo "Backup failed" >> error.log
```

/////
////
//// tab | Redirect and I/O Operators

<!-- TODO: Come Back to and rework. -->

- **Placement**: When breaking up a long sequence of commands separated by redirect or I/O operators (`>`, `>>`, `<`, `|`), place these operators on a new continuation line.
    - **Reason**: Placing the operators on a new line makes them more visible and separates them from the preceding command, improving overall readability.
- **Single-Line Placement**: If the sequence of commands fit within the 88-character limit, keeping them on the same line is acceptable. However, this should be done carefully, as it can affect readability.
    - **Reason**: Keeping everything on a single line is often fine when the sequence is short, but this approach should be used sparingly to ensure the code remains easy to follow.

///// details | Examples
    type: example

**NOTE**: The second and third examples enhance readability by separating each command and operator onto its own line, making the code easier to follow. The first example, while valid, is more compact and may be less clear due to the placement of multiple commands on a single line.

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

While the syntax for function declarations may not significantly impact script readability or functionality, there is a preferred style that aligns with best practices for clarity and consistency.

/// admonition | Guidelines
    type: info

- **Syntax**: <mark>**_ALWAYS_**</mark> declare functions using their name followed by parentheses and curly braces (`name() {}`).
    - **Reason**: This syntax is concise and widely accepted within the Bash community.
- **Avoid `function`**: Avoid using the `function` keyword to declare functions.
    - **Reason**: While the `function` keyword is valid in Bash, it adds unnecessary verbosity without providing additional benefits.
///

## Formatting Control Structures

Control structures in Bash, such as `if` statements and `for` or `while` loops, can be formatted in multiple ways. Depending on the context, you can choose between a standard or single-line control structure. In either case, maintaining consistency and readability is key.

/// admonition | Guidelines
    type: info
//// tab | Standard Control Structure

- **Opening Keyword Placement**: Place `then` on the same line as the `if` statement and `do` on the same line as the `for` or `while` loop.
- **Closing Keyword Placement**: End `if` statements with `fi` and loops with `done` on their own lines.
- **Continuation Line**: If the condition or loop statement exceeds the CPL limit, <mark>**_ALWAYS_**</mark> use eight spaces for each continuation line rather than the standard four spaces.
    - **Reason**: Using eight spaces for continuation lines creates a clear visual distinction between the conditions and the code within the control structure.

///// details | Example
    type: example

_Standard control structure formatting:_

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
    echo "==> A fatal error occurred."
elif [[ ($exit_code == "130" ||  $exit_code == "143") \
        && $display_message == "true" ]]; then
    echo ""
    echo "==> User interruption detected."
fi
```

<!-- TODO: Review the explanation and everything below within this section... Got too tired... -->

**Explanation**: The eight-space indentation makes the conditions and their corresponding actions easy to distinguish, especially in complex logical structures.

/////
////
//// tab | Single-Line Control Structure

- **Clarity and Maintainability**: Single-line control structures should be clear and concise.
    - **Reason**: Single-line control structures are best suited for simple conditions or loops that can be expressed succinctly. This prevents clutter and ensures the code remains readable.
- **Avoid Complex Logic**: Avoid adding multiple commands or complex logic to single-line control structures.
    - **Reason**: Complex logic or multiple commands in a single-line control structure can reduce readability and make the code harder to maintain.
- **When to Avoid**: Avoid using single-line control structures if they cannot fit within the 88-character limit.
    - **Reason**: If a single-line control structure exceeds the 88-character limit, it compromises readability. In such cases, the structure should be expanded into multiple lines.

///// details | Example
    type: example

_Example of a single-line control structure:_

```bash
[[ $1 -eq 1 ]] && echo "You entered one."

for file in *.txt; do echo "Processing $file"; done
```

**Explanation**: These examples show how to use single-line control structures for straightforward logic, keeping the code clean and easy to read.

/////
////
///

## Vertical Spacing

Vertical spacing is just as important as horizontal spacing in maintaining a clean and readable script. Proper use of vertical spacing helps to visually organize code, making it easier to follow.

/// admonition | Guidelines
    type: info

- **Single Blank Line**: Use a single blank line to separate logical blocks of code or functions.
- **Double Blank Lines**: Use double-blank lines sparingly to highlight new sections or distinct logical groups within the script.

///

/// admonition | Why Vertical Spacing Matters?
    type: tip

- **Readability**: Proper vertical spacing balances readability and organization. Too many blank lines can make the script appear disjointed, while too few can make it look cluttered. Striking the right balance ensures the script is easily read and logically organized.

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

**Explanation**: In this example, a single blank line separates the add and subtract functions. Double blank lines are used before the `echo "Starting the script..."` line, signifying a transition from function definitions to the main execution block of the script. This approach keeps the script well-organized and easy to follow.

///

## Comments

Comments are essential for explaining script functionality and enhancing long-term usability. Effective commenting practices ensure scripts are accessible and easily understood by new developers or future maintainers. While similar to previous guidelines, comments have a few additional considerations depending on their context.

/// admonition | General Guidelines
    type: info

- **Capitalization**: Begin each comment with a capital letter, except for code elements like variables or functions.
- **Punctuation**: Conclude comments with a period to indicate a complete thought.
- **Spacing**: For inline comments, maintain two spaces between the code and the comment.
    - **Reason**: While a single space is often sufficient, two spaces provide a clearer visual separation between the code and the comment, enhancing readability.

///

/// admonition | Example
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

- **Purpose**: Provide a clear and comprehensive description of the function's role within the script. This description should go beyond merely repeating the function's name; it should offer context and explain its intended use.
    - **Reason**: Clearly documenting a function's purpose allows developers, including yourself, to quickly grasp what the function is meant to achieve. Even if the function name is descriptive, a brief explanation adds valuable context, facilitating a better understanding of its role.
    - **Exception**: If the function is short **AND** its name provides enough description, the purpose may be left blank, or a short and simple description may be used instead. This exception should be used sparingly and only when the function's purpose is immediately evident from its name.
- **Global Variables**: If the function relies on any global variables, specify which ones are used. Indicate whether the function modifies these variables, how they are used, and any potential side effects.
    - **Reason**: Identifying how the function interacts with global variables helps developers (including yourself) understand the function's impact on the script's state. This is especially important in larger scripts where tracking variable scope can be challenging.
- **Parameters**: Detail each parameter the function accepts, noting whether they are required or optional, and specify any default values.
    - **Reason**: Documenting the parameters clarifies how the function should be called and what inputs it requires to operate correctly. This reduces the likelihood of errors or misuse and ensures that the function is used consistently with its intended design.
    - **Value Assignment**: Assign parameter values to local variables within the function.
        - **Reason**: Assigning parameter values to local variables enhances readability and ensures the function's logic is self-contained. This practice also prevents accidental modification of the original parameters.
- **Output and Return**: Specify the function's output and return values.
    - **Reason**: Describing the function's output and return values helps users understand what to expect when calling the function and how to handle the results. This makes the code more predictable and easier to debug and maintain.
- **General Reasoning**: Besides the reasons for each guideline, documenting functions allows for quick reference and understanding of the script's structure and logic. This is especially beneficial when revisiting the script or collaborating with other developers after an extended period.
- **Format/Structure Example**: Below is the recommended format and structure to be followed when documenting functions in Bash scripts. This format provides a clear outline for documenting functions effectively. Please pay attention to the annotations within the example. They provide additional context and explanations for specific parts of the function documentation.

    ```{ .bash .annotate }
    ####
    # Function description...
    #
    # NOTES:
    #   Additional notes or considerations. This section may be omitted if there are no
    #   extra notes.
    #
    # GLOBALS:
    #   - global_var : Brief description of usage. (1)
    #       - Detailed description of the global variable's role in the function. (2)
    #
    # NEW GLOBALS:
    #   - new_global_var : Brief description of usage (i.e. "Initialized for external use.")
    #       - Detailed description of the new global variable's role in or outside the function. (3)
    #
    # PARAMETERS:
    #   - $1: parameter_name (Required or Optional, Default: default_value) (4)
    #       - Detailed description of the parameter, including its purpose, expected values,
    #         and any constraints. (5)
    #
    # OUTPUTS: (6)
    #   Description of the data output by the function, such as data written to the console
    #   or a file.
    #
    # RETURNS: (7)
    #   Description of any values the function returns for use elsewhere in the script.
    function_name() {
        # Function logic here...
    }
    ```

    1. **Short Description for GLOBALS**: Provide a concise overview indicating whether the global variable is initialized, modified, read, used in a conditional, etc.
    2. **Detailed Explanation for GLOBALS**: Include a thorough description of the global variable's role within the function. Explain how the variable is used and why it is necessary, and mention any side effects that may result from modifying it. If the short description is sufficient, this section can be omitted, but only if it provides a clear understanding of the variable's role.
    3. **Detailed Explanation for NEW GLOBALS**: The description of the new global variables should be similar to the GLOBALS section. Though, it should primarily describe its use outside of the function.
    4. **Parameter Requirement**: Specify whether the parameter is "Required" or "Optional." If the parameter is optional, include the default value in the format `(Optional, Default: default_value)`.
    5. **Parameters Description**: Provide a detailed description of the parameter, explaining its purpose, expected values, and any constraints or limitations. This information helps other developers and yourself understand how to use the function correctly.
    6. **OUTPUTS Section**: Document any data the function outputs, such as data written to the console or a file. Clearly describe the format, purpose, and destination of the output.
    7. **RETURNS Section**: Describe any values the function returns for use elsewhere in the script.

///

/// admonition | Example
    type: example
//// tab | Example 1

```bash
####
# Convert a given IP address into an integer.
#
# NOTE:
#   This allows for easier IP address comparison and calculation. Specifically, the
#   integer is used to calculate the range of IP addresses to scan, among other things.
#
# PARAMETERS:
#   - $1: ip (Required)
#       - The IP address to convert to an integer.
ip_to_int() {
    local ip="$1"
    local IFS='.'

    read -r octet1 octet2 octet3 octet4 <<< "$ip"
    echo "$(( (octet1 << 24) + (octet2 << 16) + (octet3 << 8) + octet4 ))"
}
```

////
//// tab | Example 2

```bash
####
# Verify if the provided IP address is valid, based on a regular expression pattern.
#
# GLOBALS:
#   - C_RED : Set text color to red.
#   - C_NC : Reset text color.
#
# PARAMETERS:
#   - $1: ip (Required)
#       - The IP address to verify.
verify_valid_ip() {
    local ip="$1"
    local valid_ip_regex="^((25[0-5]|2[0-4][0-9]|1?[0-9][0-9]?)\.){3}(25[0-5]|2[0-4][0-9]|1?[0-9][0-9]?)$"

    if [[ ! $ip =~ $valid_ip_regex ]]; then
        echo -e "${C_RED}ERROR:${C_NC} Invalid IP address: $ip" >&2
        clean_exit "1" "" "false"
    fi
}
```

////
//// tab | Example 3

```{ .bash .annotate }
####
# Handles the cleanup process when the script exits normally or due to an error.
#
# NOTE:
#   This function is designed to be triggered on normal exits, errors, or interruptions.
#
# GLOBALS:
#   - background_jobs : Iterate over to kill all background jobs.
#   - C_TMP_FILE : Temporary file to remove.
#   - C_RED : Set text color to red.
#   - C_NC : Reset text color.
#   - C_YELLOW : Set text color to yellow.
#
# PARAMETERS:
#   - $1: exit_code (Required)
#       - The type of exit that occurred.
#       - Acceptable values: (1)
#           - 0: Normal exit. The script completed its task successfully.
#           - 1: Exiting due to an error. An error occurred during the script execution.
#           - 130: User interruption. The user interrupted the script using Ctrl+C.
#           - 143: User interruption. The user interrupted the script using kill.
#   - $2: clean_up (Optional, Default: "true")
#       - Whether to perform cleanup operations.
#       - Acceptable values:
#           - "true": Perform cleanup operations.
#           - "false": Skip cleanup operations.
#   - $3: display_message (Optional, Default: "true")
#       - Whether to display fatal error or user interruption messages.
#       - Acceptable values:
#           - "true": Display the message.
#           - "false": Skip displaying the message.
clean_exit() {
    local exit_code="$1"
    local clean_up="${2:-true}"
    local display_message="${3:-true}"

    if [[ $exit_code == "1" && $display_message == "true" ]]; then
        echo "${C_RED}==>${C_NC} A fatal error occurred." >&2
    elif [[ ($exit_code == "130" ||  $exit_code == "143") \
            && $display_message == "true" ]]; then
        echo ""
        echo "${C_YELLOW}==>${C_NC} User interruption detected."
    fi

    if [[ $clean_up == "true" ]]; then
        echo "${C_CYAN}==>${C_NC} Cleaning up..."

        for job in "${background_jobs[@]}"; do
            kill -9 "$job" > /dev/null 2>&1
        done

        [[ -f "$C_TMP_FILE" ]] && rm "$C_TMP_FILE"
    fi

    exit "$exit_code"
}
```

1. **Acceptable Values**: As mentioned in the guidelines, if a parameter has a specific set of acceptable values, list them. This is an example of how to document such values.

////
//// tab | Example 4

```{ .bash .annotate }
####
# Given two IP addresses, determine the lower and upper bounds, and store them in the in
# the global variables $C_LOWER_BOUND and $C_UPPER_BOUND.
#
# GLOBALS:
#   - C_RED : Set text color to red.
#   - C_NC : Reset text color.
#
# NEW GLOBALS:
#   - C_LOWER_BOUND : Initialized for external use.
#       - Indicates the *start* of the IP range to be scanned. (1)
#   - C_UPPER_BOUND : Initialized for external use.
#       - Indicates the *end* of the IP range to be scanned.
#
# PARAMETERS:
#   - $1: bound_one (Required)
#       - The first IP address to compare.
#   - $2: bound_two (Required)
#       - The second IP address to compare.
check_lower_upper_bounds() {
    local bound_one="$1"
    local bound_two="$2"

    if (( $(ip_to_int "$bound_one") > $(ip_to_int "$bound_two") )); then
        C_LOWER_BOUND="$bound_two"
        C_UPPER_BOUND="$bound_one"
    elif (( $(ip_to_int "$bound_one") < $(ip_to_int "$bound_two") )); then
        C_LOWER_BOUND="$bound_one"
        C_UPPER_BOUND="$bound_two"
    else
        echo -e "${C_RED}ERROR:${C_NC} The lower and upper bounds are the same." >&2
        clean_exit "1" "" "false"
    fi
}
```

1. **New Globals**: In situations like these, this is where you'd want to provide a detailed explanation of the new global variables' roles and how they are used within or outside the function.

////
///

### Pound Signs in Comments


Traditionally, a single pound sign (`#`) is used to denote a comment in Bash scripts. However, using a different number of pound signs can help differentiate the comments' purpose and scope.

/// admonition | Guidelines
    type: info
//// tab | Single Pound Sign (`#`)

- **Usage**: For single-line comments, start the line with a single `#`.

///// admonition | Example
    type: example

```bash
# This command lists all files in the directory.
ls -la
```

/////
////
//// tab | Double Pound Signs (`##`)

- **Usage**: Use two `##` for comments that describe the functionality of a block of code, such as a loop, conditional sequence, control structure, or a group of related variable declarations. Place these comments directly above the relevant code block.
- **Blank Lines**: If a blank line is placed between the comment and the block of code it refers to, the comment is considered to no longer apply to that code, allowing for a clear separation between different logical units or groups of code.

///// admonition | Examples
    type: example

```bash
## Set variables for the script.
var_one="value_one"
var_two="value_two"
var_three="value_three"

## Loop through all ".txt" files in the directory.
for file in *.txt; do
    echo "Processing $file"
done
```

/////
////
//// tab | Quadruple Pound Signs (`####`)

- **Usage**: Use quadruple-pound signs to separate distinct parts of the script, such as functions, variable declarations, or main script logic.
- **Sparingly**: Use quadruple-pound signs sparingly, primarily when it’s necessary to visually distinguish major sections of code. Extensive use of quadruple-pound signs for sectioning may be unnecessary in shorter scripts and can often be omitted.
- **Subsections**: Quadruple-pound signs can also be used to indicate subsections within a larger section of the script. However, this should be done even more sparingly. Consider using [triple-pound signs](#__tabbed_4_4) (`###`) before resorting to quadruple-pound signs for subsections.
- **Formatting**: Quadruple pound signs should be formatted to stand out from other comments. Below are the suggested formatting guidelines:
    - **Section Naming**: Append `[ Section Name ]` after the quadruple pound signs. The section name should describe the content or purpose of that section.
    - **Filler Characters**: After the section name, add enough `#` characters to reach the 88-character line limit.
    - **Section Comments**: If additional explanation is needed, add comments describing the section's content or purpose below the quadruple pound signs, prefixed with four `#` characters.
    - **Spacing**: As mentioned in the [vertical spacing guidelines](#vertical-spacing), provide two blank lines before and after the quadruple pound signs to enhance visual separation between sections.
    - **Subsection Format**: Subsections should follow the same format, with the number of brackets (`[]`) indicating the depth of the subsection within the script. The deeper the subsection, the more brackets are used. Use filler characters to maintain a consistent 88-character width.

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
    - **Section Naming**: Append `[ Section Name ]` after the triple pound signs. The section name should describe the content or purpose of that section.
    - **Filler Characters**: Include a single blank line above and below the filler characters to separate the previous command(s), the triple pound sign comment, and the next command(s).
    - **Spacing**: Include a single blank line above and below the filler characters to separate the previous command(s), the triple pound sign comment, and the next command(s).
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

# Aesthetics

As mentioned in the [Preface](../index.md#preface), this guide aims to be as objective as possible by providing well-founded reasons for each recommended practice. However, some practices are purely stylistic and may vary based on personal preference or specific project requirements.

This section addresses those aesthetic choices and offers guidelines for keeping scripts consistent and readable. Where applicable, the guidelines include explanations of their advantages over alternative practices.

For stylistic guidelines that impact a script's functionality, please refer to this guide's [Style](style.md) section.

## Indentation

Indentation helps structure code and improve readability by visually grouping related statements. The indentation style can affect how easy a script is to understand and maintain.

/// admonition | Guidelines
    type: info

- **Type**: <mark>**_ALWAYS_**</mark> use <u>spaces</u> for indentation.
    - **Reason**: Spaces are universally supported and render consistently across different editors and environments. This keeps the script's structure intact regardless of where it is viewed or edited.
- **Size**: <mark>**_ALWAYS_**</mark> use <u>four spaces</u> per indentation level.
    - **Reason**: Using four spaces is considered the standard and is widely accepted within the Bash community.

///

## Characters Per Line

In the past, the maximum number of characters per line (CPL) was limited to 80 because of older terminal constraints. Modern terminals and editors can display more, but a reasonable limit still helps keep code readable and maintainable across different environments and tools.

/// admonition | General Guidelines
    type: info

- **CPL Limit**: Set a maximum CPL of <u>92 characters</u>.
    - **Reason**: A 92-character limit balances the historical 80-character standard with the need for more descriptive code. It allows longer variable names, comments, and strings without sacrificing readability.
- **Exceptions**: The CPL limit may be exceeded if line splitting would _significantly_ reduce readability or harm the script's structure.

///

/// admonition | Importance of CPL Limits
    type: tip

- **Readability**: Shorter lines reduce horizontal scrolling and minimize word wrapping issues, especially when working with multiple windows at once.
- **Tool Compatibility**: A shorter CPL ensures code is displayed consistently across development environments, including code review platforms, IDEs, and terminals. This prevents layout issues and preserves the intended structure.

///

### Formatting Multi-lined Commands

With a CPL limit in place, some commands will span multiple lines. Format these multi-line commands so their structure remains easy to understand and follow.

/// admonition | Guidelines
    type: info
//// tab | Indentation and Alignment

- **Indentation**: Use the [standard four-space indentation](#indentation) for each continuation line.

///// admonition | Example
    type: example

```bash
rsync -avz /source/directory/with/a/very/long/path/ \
    /destination/directory/with/another/long/path/
```

/////
////
//// tab | Logical Operators

- **Two or More Operators**: If two or more logical operators are used in a command sequence, place each operator at the start of a new line, even if the CPL limit has not been reached.
    - **Reason**: Multiple commands on a single line can obscure what is happening. Placing them on separate lines improves readability by making the sequence more transparent. (1)
        { .annotate }

        1. **Logical Operator Placement**<br />I have no objective reasoning for placing logical operators at the beginning of a new line. It's just a personal preference. I find it easier to read and understand the command sequence when each operator is at the beginning of a new line rather than at the end of the previous line.

- **Single Operator**: If a single logical operator connects two commands and the full line remains within the CPL limit, both commands may stay on the same line.
    - **Reason**: Keeping a single operator on the same line can be concise and easy to read when the command sequence is short. Splitting it unnecessarily may add complexity without improving clarity.
- **Special Case**: Sometimes a single `||` operator follows a command, and the failure-handling block spans multiple lines. In these cases, placing `|| {` at the end of the initial command is acceptable. The closing brace (`}`) should start a new line, with the operations enclosed between the braces.
    - **Reason**: This format keeps the structure concise and easy to follow when handling a single failure condition with `||`. Other formats can add complexity without improving clarity.
    - **Example of acceptable formatting**:
        ```bash
        cp /some/config/file /some/config/file.bak || {
            echo "Failed to back up 'file'" >&2
            echo "Please create a backup of the original 'file' before continuing"
            exit 1
        }
        ```
      - **Example of formatting that is not acceptable**:
        ```bash
        rm /some/config/file.bak && cp /some/config/file /some/config/file.bak || {
            echo "Failed to overwrite backup of 'file'" >&2
            exit 1
        }
        ```
        **Explanation**: Here, `&&` and `||` are on the same line, which violates the "Two or More Operators" guideline and can make the sequence harder to understand. The preferred format is:
        ```bash
        rm -rf /some/config/backup_dir \
            && mv -fT /some/other/config/backup_dir /some/config/backup_dir \
            || {
                echo "Failed to overwrite backup directory" >&2
                exit 1
            }
        ```

///// details | General Examples
    type: example

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
//// tab | Piping Operators

- **Placement**: Similar to logical operators, when a piped command sequence exceeds the CPL limit, place each pipe (`|`) at the start of a new line before the next command (`| command`).
- **Single-Line Placement**: If piped commands fit within the 92-character limit, they may remain on the same line. Use this sparingly to avoid creating complex or hard-to-read sequences.

///// details | Examples
    type: example

_Piped command sequence on a single line:_

```bash
grep -r "TODO" /path/to/project | grep -v "DONE" | sort | uniq > todo_list.txt
```

---

_Multi-line command sequence:_

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

## Formatting Control Structures

Control structures in Bash, such as `if` statements and `for` or `while` loops, can be formatted in several ways. Depending on the context, you can use either a standard or single-line control structure. In both cases, consistency and readability are key.

/// admonition | Guidelines
    type: info
//// tab | Standard Control Structure

- **Keyword Placement**:
    - **Opening Keyword**: Place `then` on the same line as the `if` statement and `do` on the same line as the `for` or `while` loop.
    - **Closing Keyword**: End `if` statements with `fi` and loops with `done` on their own lines.
- **Continuation Line**: If the condition or loop statement exceeds the CPL limit, you have two options for formatting continuation lines:
    - **Option 1 (Recommended)**: Place the opening keyword on the next line rather than at the end of the condition or loop statement.
    - **Option 2**: Use eight spaces for each continuation line rather than the standard four spaces.
    - **Reason**: Both options help separate the condition or loop statement from the commands inside the control structure. This improves readability and preserves a clear structure. Option 1 is recommended, but Option 2 is acceptable if it is used consistently within the script.

///// details | Examples
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

_Continuation lines — Option 1:_

```bash
if (! hash dotnet \
    || ! hash redis-server \
    || ! hash python3 \
    || ! "$ccze_installed" \
    || ! "$yt_dlp_installed" \
    || [[ ${dotnet_version:-false} != "$C_REQ_DOTNET_VERSION" ]]) &>/dev/null
then
    opt_one_dis=true
    opt_one_text="${E_GREY}${opt_one_text}${dis_option}${E_NC}"
fi
```

---

_Continuation lines — Option 2:_

```bash
if (! hash dotnet \
        || ! hash redis-server \
        || ! hash python3 \
        || ! "$ccze_installed" \
        || ! "$yt_dlp_installed" \
        || [[ ${dotnet_version:-false} != "$C_REQ_DOTNET_VERSION" ]]) &>/dev/null; then
    opt_one_dis=true
    opt_one_text="${E_GREY}${opt_one_text}${dis_option}${E_NC}"
fi
```

/////
////
//// tab | Single-Line Control Structure

- **Clarity and Maintainability**: Single-line control structures must be clear and concise.
    - **Reason**: This format is best suited for simple conditions or loops that can be expressed succinctly.
- **Avoid Complex Logic**: Avoid adding multiple commands or complex logic to single-line structures.
    - **Reason**: Including complex logic or multiple commands on a single line can reduce readability and make the code harder to maintain.
- **Character Limit**: <mark>**_DO NOT_**</mark> use a single-line control structure if it exceeds the 92-character limit.

///// details | Example
    type: example

_Two single-line control structures:_

```bash
[[ $1 -eq 1 ]] && echo "You entered one."

for file in *.txt; do echo "Processing $file"; done
```

/////
////
///

## Vertical Spacing

Vertical spacing is easy to overlook, but it is important for readability and for keeping a script clean and organized.

/// admonition | Guidelines
    type: info

- **Single Blank Line**: Use a single blank line to separate logical blocks of code or functions.
- **Double Blank Lines**: Use double blank lines sparingly to highlight new sections or distinct logical groups.

///

/// admonition | Why Vertical Spacing Matters
    type: tip

- **Readability**: Proper vertical spacing balances readability and organization. Too many blank lines can make a script feel disjointed, while too few can make it look cluttered. A consistent approach keeps the script easier to read and follow.

///

/// admonition | Example
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

**Explanation**: In this example, a single blank line separates the add and subtract functions. Two blank lines appear before `echo "Starting the script..."`, marking the transition from function definitions to the main script logic. This spacing improves readability and visually separates the script's distinct sections.

///

## Comments

Comments explain script behavior and improve long-term maintainability. Effective comments help new developers and future maintainers understand the script. While similar to previous guidelines, comments have a few additional considerations depending on their context.

/// admonition | General Guidelines
    type: info

- **Capitalization**: Begin each comment with a capital letter, except for code elements like variables or functions.
- **Punctuation**: End comments with a period to indicate a complete thought.
- **Spacing**: For inline comments, maintain two spaces between the code and the comment.
    - **Reason**: While a single space is often sufficient, two spaces provide a clearer visual separation between the code and the comment.

///

/// admonition | Example
    type: example

```bash
# This is a comment explaining the next line of code.
variable="value"  # Inline comment with two spaces before it.
```

///

### Function Comments

Functions in Bash differ from those in other languages, especially in argument handling, global state, printed output, and exit statuses. Because of these differences, functions should be documented according to their complexity, side effects, and importance within the script.

/// admonition | Guidelines
    type: info

- **Scale Detail With Complexity**: The amount of detail in a function comment should scale with the function's complexity, side effects, and risk. It should also account for the complexity of the script as a whole.
    - **Simple Functions**: Short functions with descriptive names, obvious behavior, and no surprising side effects may use a brief one-line comment. Optional sections such as `PARAMETERS`, `RETURNS`, or `EXITS` may be omitted if they do not add useful information.
    - **Complex Functions**: Functions that accept multiple parameters, modify global state, produce output that callers rely on, perform cleanup, validate input, parse data, call external commands, or control script flow should use a more detailed comment block.
    - **Larger Scripts**: In larger or more complex scripts, document function behavior more explicitly because readers must track more global state, control flow, and interactions between functions.
    - **Reason**: Over-documenting simple functions can make scripts harder to scan, while under-documenting complex functions forces future readers to infer behavior from the implementation.
- **Purpose**: Describe what the function does and why it exists when the function name alone is not sufficient. This description should go beyond repeating the function's name in sentence form.
    - **Reason**: Clearly documenting a function's purpose helps developers, including yourself, quickly understand the function's role within the script.
    - **Exception**: If the function is short, self-contained, and clearly named, a brief description may be enough.
- **Global Variables**: Document any global variables the function creates, modifies, or depends on when that interaction is not obvious.
    - **Reason**: Identifying how the function interacts with global variables helps developers, including yourself, understand the function's impact on the script's state. This is especially important in larger scripts, where tracking variable scope can be difficult.
- **Parameters**: Document positional parameters when their purpose, required status, default value, or accepted values are not immediately clear.
    - **Value Assignment**: Assign parameter values to `local` variables within the function.
    - **Reason**: While positional parameters are common in Bash, they can be less intuitive than named parameters in other languages. Documenting them helps clarify their purpose and usage.
- **Output**: Document stdout or stderr output when callers rely on it, when the output is parsed elsewhere, or when the distinction between display output and return status matters.
    - **Reason**: Bash functions often communicate through printed output, so callers need to know whether output is informational or part of the function's contract.
- **Exit and Return**: Document meaningful return values and any conditions where the function exits the script. Specify return and exit values separately when both are possible.
    - **Reason**: A function that returns a status code is different from one that terminates the script. This distinction should be clear before the function is called.
- **Important Notes**: Include notes for non-obvious behavior, assumptions, edge cases, dependencies, or risks.
    - **Reason**: Notes are useful for information that does not fit cleanly into purpose, parameters, globals, output, returns, or exits.
- **Omit Empty Sections**: Do not include sections that are not applicable or do not add useful information.
    - **Reason**: Empty or obvious sections make comments longer without making the function easier to understand.
- **Format/Structure Example**: Below is the recommended structure for documenting functions in Bash scripts. It provides a clear outline, but it should be adapted to the function being documented.

    ```{ .bash .annotate }
    ####
    # Function description...
    #
    # NOTES:
    #   Additional notes or considerations. This section may be omitted if there is no
    #   additional information to provide.
    #
    # NEW GLOBALS:
    #   - global_variable_one : Description of global_variable_one.
    #
    # MODIFIED GLOBALS:
    #   - global_variable_two : Description of how global_variable_two is modified.
    #
    # PARAMETERS:
    #   - $1: parameter_name (Required) (1)
    #       - Description of the parameter. (2)
    #   - $2: parameter_name (Optional, Default: default_value)
    #       - Description of the parameter.
    #       - Acceptable values: (3)
    #           - value_one: Description of value_one.
    #           - value_two: Description of value_two.
    #
    # RETURNS:
    #   return value: Description of when the return value is provided and its significance.
    #
    # EXITS:
    #   exit value: Description of when the exit occurs and the reason for the exit.
    function_name() {
        # Function logic here...
    }
    ```

    1. **Parameter Requirement**: Specify whether the parameter is "Required" or "Optional." If the parameter is optional, include the default value in the format `(Optional, Default: default_value)`.
    2. **Parameter Description**: May be omitted if the parameter's purpose is immediately clear from its name or the function's logic.
    3. **Acceptable Values**: If a parameter has specific values it accepts/expects, list them. This ensures that the function is used correctly and helps prevent errors. If the value's purpose is not immediately clear, provide a brief description.

///

/// admonition | Examples
    type: example
//// tab | Simple Function

```bash
####
# Print a formatted error message.
print_error() {
    local message="$1"

    echo "${C_RED}ERROR:${C_NC} $message" >&2
}
```

This function only needs a brief description because its purpose, input, and output are clear from the name and implementation. The local variable gives `$1` a clear name, and the function does not modify global state or affect script control flow. If callers depended on the exact output format, an `OUTPUTS` section could be added.

////
//// tab | Modified Global

```bash
####
# Add a package to the required package list if it is not already present.
#
# MODIFIED GLOBALS:
#   - required_pkgs: Appends the package name when it is not already present.
require_pkg() {
    local required_pkg="$1"

    for pkg in "${required_pkgs[@]}"; do
        [[ $pkg == "$required_pkg" ]] && return
    done

    required_pkgs+=("$required_pkg")
}
```

This function is still small, but its main behavior is modifying a global array. Documenting that side effect is more useful than documenting the positional parameter, which is already clear from the function name and `local required_pkg="$1"` assignment. This kind of detail becomes more important in larger scripts.

////
//// tab | Output Contract

```bash
####
# Convert an IPv4 address into an integer for numeric comparison.
#
# PARAMETERS:
#   - $1: ip (Required)
#       - The IPv4 address to convert.
#
# OUTPUTS:
#   stdout: The integer representation of the IPv4 address.
ip_to_int() {
    local ip="$1"
    local IFS='.'

    read -r octet1 octet2 octet3 octet4 <<< "$ip"
    echo "$(( (octet1 << 24) + (octet2 << 16) + (octet3 << 8) + octet4 ))"
}
```

This function writes a computed value to stdout so callers can capture it with command substitution or compare it in arithmetic expressions. Because stdout is how the function returns useful data, the `OUTPUTS` section fits better than a `RETURNS` section.

////
//// tab | Return Status

```bash
####
# Check whether the provided value is a valid IPv4 address.
#
# PARAMETERS:
#   - $1: ip (Required)
#       - The value to validate.
#
# RETURNS:
#   0: The value is a valid IPv4 address.
#   1: The value is not a valid IPv4 address.
is_valid_ip() {
    local ip="$1"
    local valid_ip_regex="^((25[0-5]|2[0-4][0-9]|1?[0-9][0-9]?)\.){3}(25[0-5]|2[0-4][0-9]|1?[0-9][0-9]?)$"

    [[ $ip =~ $valid_ip_regex ]] && return 0
    return 1
}
```

This function does not print output or exit the script. Callers use it directly in conditionals, so the return status is the function's contract. The `RETURNS` section therefore documents the meaningful behavior without adding unnecessary `OUTPUTS` or `EXITS` sections.

////
//// tab | Complex Function

```bash
####
# Perform cleanup operations and terminate the script with the provided exit code.
#
# NOTES:
#   This function is intended to be called from normal error handling and signal traps.
#
# MODIFIED GLOBALS:
#   - background_jobs: Cleared after tracked processes are terminated during cleanup.
#
# PARAMETERS:
#   - $1: exit_code (Required)
#       - The exit code to use when terminating the script.
#       - Acceptable values:
#           - 0: Normal exit.
#           - 1: Error exit.
#           - 130: User interruption.
#           - 143: Termination signal.
#   - $2: clean_up (Optional, Default: true)
#       - Whether cleanup operations should be performed.
#       - Acceptable values: true, false
#   - $3: display_message (Optional, Default: true)
#       - Whether an exit message should be printed.
#       - Acceptable values: true, false
#
# EXITS:
#   exit_code: Always exits the script after cleanup is complete.
clean_exit() {
    local exit_code="$1"
    local clean_up="${2:-true}"
    local display_message="${3:-true}"

    if [[ $exit_code == "1" && $display_message == "true" ]]; then
        echo "${C_RED}ERROR:${C_NC} A fatal error occurred." >&2
    elif [[ ($exit_code == "130" || $exit_code == "143")
            && $display_message == "true" ]]; then
        echo ""
        echo "${C_YELLOW}WARNING:${C_NC} User interruption detected."
    fi

    if [[ $clean_up == "true" ]]; then
        echo "${C_CYAN}INFO:${C_NC} Cleaning up..."

        for job in "${background_jobs[@]}"; do
            kill -9 "$job" > /dev/null 2>&1
        done
        background_jobs=()

        [[ -f $C_TMP_FILE ]] && rm "$C_TMP_FILE"
    fi

    exit "$exit_code"
}
```

This function uses a full comment block because it has multiple parameters, modifies global state, performs cleanup, and always exits the script.

////
///

### Pound Signs in Comments

Traditionally, a single pound sign (`#`) denotes a comment in Bash scripts. Using multiple pound signs can help distinguish a comment's purpose and scope.

/// admonition | Guidelines
    type: info
//// tab | Single Pound Sign (`#`)

- **Usage**: Use a single pound sign for general comments that explain a single line of code or provide context for a specific command.

///// admonition | Example
    type: example

```bash
# This command lists all files in the directory.
ls -la
```

/////
////
//// tab | Double Pound Signs (`##`)

- **Usage**: Use two pound signs for comments that describe the functionality of a block of code, such as a loop, conditional sequence, control structure, or a group of related variable declarations. Place these comments directly above the relevant code block.
- **Blank Lines**: These comments apply directly to the block of code immediately following them. If a blank line precedes the commented code block, it indicates that the comment does not apply to the later code.
    - **Exception**: If a double pound sign comment is placed at the top of a control structure or loop, and a blank line is introduced within the structure, the comment still applies to the entire structure.

///// admonition | Examples
    type: example

```{ .bash .annotate }
## Set variables for the script.
var_one="value_one"
var_two="value_two"
var_three="value_three"

## Loop through all ".txt" files in the directory. (1)
for file in *.txt; do
    echo "Processing $file"

    echo "Done"
done
```

1. **Blank Line in Structure**: While there is a blank line between the two `echo` commands, this comment still applies to the entire loop.

/////
////
//// tab | Quadruple Pound Signs (`####`)

- **Usage**: Use four pound signs to separate distinct parts of the script, such as functions, variable declarations, or main script logic.
- **Sparingly**: Use four pound signs sparingly, primarily when it’s necessary to visually distinguish major sections of code. Excessive use can clutter the script and make it harder to read.
- **Omitting `####`**: Quadruple-pound signs can be omitted if the script is relatively short, well-structured, and easy to navigate without additional sectioning.
- **Subsections**: Quadruple-pound signs can also indicate subsections within a larger script section. However, this should be done **VERY** sparingly. Consider using [triple-pound signs](#__tabbed_4_4) (`###`) before resorting to quadruple-pound signs for subsections.
- **Formatting**: Quadruple-pound sign comments should make the transition between sections clear and visually distinct. Below are the recommended formatting guidelines:
    - **Section Naming**: Append `[ section_name ]` to the end of `####`, replacing `section_name` with a descriptive title. The section name should clearly indicate the content or purpose of that section.
    - **Filler Characters**: After the section name, append a series of `#` characters to fill the remaining space up to the 92-character limit.
    - **Section Comments**: If necessary, add comments directly below the initial `####` line, prefixing them with four `#` characters.
    - **Spacing**: As mentioned in the [vertical spacing guidelines](#vertical-spacing), provide two blank lines before and after the quadruple-pound signs to improve visual separation between sections.
    - **Subsection Format**: Subsections should follow the same format, with the number of brackets (`[]`) indicating the depth of the subsection within the script. The deeper the subsection, the more brackets should be used. Use filler characters to maintain a consistent 92-character width.

///// details | Example
    type: example

```{ .bash .annotate }
####[ Global Variables ]################################################################
####[[ Modifiable Variables ]]########################################################## (1)


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

1. **Triple Pound Signs**: As mentioned in the guidelines, consider using triple-pound signs (`###`) before resorting to quadruple-pound signs for subsections.

/////
////
//// tab | Triple Pound Signs (`###`)

- **Description**: Triple-pound signs serve as a middle ground between double and quadruple-pound signs. They are used when blocks or lines of code need some distinction but do not require a completely new section.
- **Usage**: Use three pound signs where the code is different enough to warrant distinction but not significant enough to be placed in an entirely new section.
- **Formatting**: Triple-pound sign comments should make the transition between different blocks of code clear and visually distinct. Below are the recommended formatting guidelines:
    - **Section Naming**: Append `[ section_name ]` to the end of `###`, replacing `section_name` with a descriptive title for that section. The section name should clearly indicate the content or purpose of the code below it.
    - **Filler Characters**: After the section name, append a series of `#` characters to fill the remaining space up to the 92-character limit. Additionally, place three `#` characters above and below the section name line.
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
readonly C_MAX_CONCURRENT_PINGS=255

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


## Functions to perform various tasks...
```

/////
////
///

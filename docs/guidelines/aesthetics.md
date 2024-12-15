# Aesthetics

As mentioned in the [Preface](../index.md#preface), this guide aims to be as objective as possible by providing well-founded reasons for each recommended practice. However, some practices are purely stylistic and may vary based on personal preference or specific project requirements.

This section addresses these aesthetic choices and offers guidelines for maintaining a consistent and visually appealing script. Where applicable, the guidelines will include explanations of their advantages over alternative practices.

For stylistic guidelines that impact a script's functionality, please refer to this guide's [Style](style.md) section.

## Indentations

Indentations help structure code and improve readability by visually grouping related statements. The indentation method can greatly affect how well a script can be understood and maintained.

/// admonition | Guidelines
    type: info

- **Type**: <mark>**_ALWAYS_**</mark> use <u>spaces</u> for indentation.
    - **Reason**: Spaces are universally supported and render consistently across different editors and environments. This ensures that the script's structure remains intact regardless of where it is viewed or edited.
- **Size**: <mark>**_ALWAYS_**</mark> use <u>four spaces</u> per indentation level.
    - **Reason**: Four spaces per indentation level is considered the standard and is widely accepted within the Bash community. This size strikes a balance between readability and an efficient use of space.

///

## Characters Per Line

In the past, the maximum CPL was limited to 80-characters because of the constraints of older terminals. Even though modern terminals and editors can display more characters per line, it's still beneficial to stick to a reasonable limit to maintain readability, consistency, and ease of maintenance across different environments and tools.

/// admonition | General Guidelines
    type: info

- **CPL Limit**: Set a maximum CPL of <u>88 characters</u>.
    - **Reason**: An 88-character limit offers a balance between the historical 80-character standard and the need for more descriptive code. It accommodates longer variable names, comments, and strings without sacrificing readability.
- **Exceptions**: The CPL limit may be exceeded if breaking a line _significantly_ reduces readability or negatively impacts the script's structure.

///

/// admonition | Importance of CPL Limits
    type: tip

- **Readability**: Shorter lines reduce the need for horizontal scrolling and minimize word wrapping issues, which can make all the difference when working with multiple windows simultaneously.
- **Tool Compatibility**: Maintaining a consistent CPL ensures that code is displayed uniformly across different development environments, such as code review platforms, IDEs, and terminals. This prevents layout issues and preserves the intended structure.

///

### Formatting Multi-lined Commands

With a CPL limit in place, commands will inevitably span multiple lines. As such, it's essential to format these multi-lined commands in a way that maintains an easy-to-understand and follow structure.

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

- **Two or More Operators**: If two or more logical operators are used in a command sequence, place each operator at the start of a new line, even if the CPL limit hasn't been reached.
    - **Reason**: Multiple commands on a single line can obscure operations. Separating them into new lines improves readability by making the sequence more transparent. Beginning each continuation line with an operator also clarifies how commands are related.
- **Single Operator**: If a single logical operator connects two commands and the length is within the CPL limit, both commands may remain on the same line.
    - **Reason**: By keeping commands on the same line, we can enhance readability and conciseness, especially when dealing with short commands. Splitting them unnecessarily may introduce complexity without added clarity.
- **Exception**: There are times when a single `||` operator follows a command, and the subsequent operations span multiple lines. In such cases, placing `|| {` at the end of the command is acceptable. The closing bracket (`}`) should start a new line, with operations enclosed between the braces.
    - **Reason**: This format keeps the structure concise for handling a single failure condition with `||`. Other formats can add complexity without improving clarity, making this approach more readable.
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
        **Explanation**: Here, `&&` and `||` are on the same line, violating the "Two or More Operators" guideline and making the sequence potentially harder to follow. The preferred format is:
        ```bash
        rm /some/config/file.bak \
            && cp /some/config/file /some/config/file.bak \
            || {
                echo "Failed to overwrite backup of 'file'" >&2
                exit 1
            }
        ```
        **Explanation**: Placing each operator at the beginning of a new line improves readability by clearly separating the operations.

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
//// tab | Piping Operators

- **Placement**: When a piped command sequence exceeds the CPL limit, place each pipe (`|`) at the start of a new line with the preceding command (`| command`).
    - **Reason**: Starting each pipe on a new line improves readability by making the pipeline stages clear and highlighting the data flow between commands.
- **Single-Line Placement**: If piped commands fit within the 88-character limit, they may remain on the same line. Use this sparingly to avoid creating complex or hard-to-read sequences.
    - **Reason**: Single-line commands are more concise and quicker to write, but overuse can reduce readability and maintainability. Balance brevity with clarity.

///// details | Examples
    type: example

<!-- TODO: Maybe add an explanation to the examples? -->

_Sequence of piped commands on a single line:_

```bash
grep -r "TODO" /path/to/project | grep -v "DONE" | sort | uniq > todo_list.txt
```

---

_Breaking up a sequence of piped commands, exceeding the CPL limit:_

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

Control structures in Bash, such as `if` statements and `for` or `while` loops, can be formatted in multiple ways. Depending on the context, you can choose between a standard or single-line control structure. In either case, maintaining consistency and readability is key.

/// admonition | Guidelines
    type: info
//// tab | Standard Control Structure

- **Keyword Placement**:
    - **Opening Keyword**: Place `then` on the same line as the `if` statement and `do` on the same line as the `for` or `while` loop.
    - **Closing Keyword**: End `if` statements with `fi` and loops with `done` on their own lines.
- **Continuation Line**: If the condition or loop statement exceeds the CPL limit, you have two options for formatting continuation lines:
    - **Option 1 (Recommended)**: Place the opening keyword on the next blank line, rather than at the end of the condition or loop statement.
    - **Option 2**: Use eight spaces for each continuation line rather than the standard four spaces.
    - **Reason**: Both options help differentiate the condition or loop statement from the actions or commands within the control structure. This separation enhances readability and maintains a clear structure. While Option 1 is the recommended approach, Option 2 is acceptable if you prefer a different style. However, it's essential to maintain consistency within the script.

///// details | Example
    type: example

<!-- TODO: Maybe add an explanation to the examples? -->

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

_Continuation lines option 1:_

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

_Continuation lines option 2:_

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
    - **Reason**: This format is best suited for simple conditions or loops that can be expressed succinctly, preventing clutter and maintaining readability.
- **Avoid Complex Logic**: Avoid adding multiple commands or complex logic to single-line structures.
    - **Reason**: Including complex logic or multiple commands on a single line can reduce readability and make the code harder to maintain.
- **Character Limit**: Do not use single-line control structures if they exceed the 88-character limit.
    - **Reason**: Structures that are too long compromise readability. In such cases, they should be expanded into multiple lines.

///// details | Example
    type: example

<!-- TODO: Maybe add an explanation to the examples? -->

_Example of two single-line control structure:_

```bash
[[ $1 -eq 1 ]] && echo "You entered one."

for file in *.txt; do echo "Processing $file"; done
```

/////
////
///

## Vertical Spacing

Vertical spacing is often overlooked when formatting Bash scripts. However, it is crucial for enhancing readability and maintaining a clean and organized script structure.

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

**Explanation**: In this example, a single blank line separates the add and subtract functions. Two blank lines are used before the `echo "Starting the script..."` line, signifying a transition from function definitions to the main execution block of the script. This approach keeps the script well-organized and easy to follow.

///

## Comments

Comments are essential for explaining script functionality and enhancing long-term usability. Effective commenting practices ensure scripts are easily understood by new developers or future maintainers. While similar to previous guidelines, comments have a few additional considerations depending on their context.

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
    - **Exception**: If the function is short **AND** its name is descriptive, the purpose may be left blank, or a short and simple description may be used instead. This exception should be used sparingly and only when the function's purpose is immediately evident from its name.
- **Global Variables**: If the function introduces new global variables or modifies existing ones, document these variables, their purpose, or how they are modified.
    - **Reason**: Identifying how the function interacts with global variables helps developers (including yourself) understand the function's impact on the script's state. This is especially important in larger scripts where tracking variable scope can be challenging.
- **Parameters**: Detail each parameter the function accepts, noting whether they are required or optional, and specify any default values.
    - **Reason**: Documenting the parameters clarifies how the function should be called and what inputs it requires to operate correctly. This reduces the likelihood of errors or misuse and ensures that the function is used consistently with its intended design.
    - **Value Assignment**: Assign parameter values to local variables within the function.
        - **Reason**: Assigning parameter values to local variables enhances understanding by give the parameters meaningful names within the function's scope. This practice also prevents accidental modification of the original parameters.
- **Output and Return**: Specify the function's output and return values separately. Describe any data the function outputs and the values it returns for use elsewhere in the script.
    - **Reason**: Describing the function's output and return values helps developers understand what to expect when calling the function and how to handle the results.
- **General Reasoning**: Besides the reasons previously mentioned, documenting functions allows for quick reference and understanding of the script's structure and logic. This is especially beneficial when revisiting the script or collaborating with other developers after an extended period.
- **Format/Structure Example**: Below is the recommended format and structure for documenting functions in Bash scripts. This format provides a clear outline for documenting functions effectively. Please pay attention to the annotations within the example. They provide additional context and explanations for the documentation.

    /// admonition | Important Note

    While all of the fields are highly recommended, some may be omitted if they are not applicable to the function, or if they do not provide additional value. However, it is recommended to include as much information as possible to ensure the function is well-documented. Additionally, if information in one of the fields is already provided elsewhere or is self-evident from the function's name or logic, it may be omitted. For example, say you already provide information regarding the output within the function description, you may omit the OUTPUTS section.

    ///

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
    # OUTPUTS: (4)
    #   Description of the data output by the function, such as data written to the console
    #   or a file.
    #
    # RETURNS: (5)
    #   Description of any values the function returns for use elsewhere in the script.
    function_name() {
        # Function logic here...
    }
    ```

    1. **Parameter Requirement**: Specify whether the parameter is "Required" or "Optional." If the parameter is optional, include the default value in the format `(Optional, Default: default_value)`.
    2. **Parameters Description**: Provide a detailed description of the parameter, explaining its purpose, expected values, and any constraints or limitations. This information helps other developers and yourself understand how to use the function correctly.
    3. **Acceptable Values**: If a parameter has specific acceptable values, list them. This ensures that the function is used correctly and helps prevent errors.
    4. **OUTPUTS Section**: Document any data the function outputs, such as data written to the console or a file. Clearly describe the format, purpose, and destination of the output.
    5. **RETURNS Section**: Describe any values the function returns for use elsewhere in the script.

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
# Verify that the provided IP address is valid, based on a regular expression pattern.
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
# Perform cleanup operations when the script exits.
#
# NOTE:
#   This function is designed to be triggered on normal exits, errors, or interruptions.
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
    elif [[ ($exit_code == "130" ||  $exit_code == "143")
            && $display_message == "true" ]]; then
        echo ""
        echo "${C_YELLOW}==>${C_NC} User interruption detected."
    fi

    if [[ $clean_up == "true" ]]; then
        echo "${C_CYAN}==>${C_NC} Cleaning up..."

        for job in "${background_jobs[@]}"; do
            kill -9 "$job" > /dev/null 2>&1
        done

        [[ -f $C_TMP_FILE ]] && rm "$C_TMP_FILE"
    fi

    exit "$exit_code"
}
```

1. **Acceptable Values**: As mentioned in the guidelines, if a parameter has a specific set of acceptable values, list them. This is an example of how to document such values.

////
//// tab | Example 4

```{ .bash .annotate }
####
# Given two IP addresses, determine the lower and upper bounds, and store them in the
# global in two new global variables.
#
# NEW GLOBALS:
#   - C_LOWER_BOUND: Indicates the *start* of the IP range to be scanned. (1)
#   - C_UPPER_BOUND: Indicates the *end* of the IP range to be scanned.
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

1. **New Globals**: In situations like these, where you are defining a new global variable, you want to explain the new global variables' roles and how they are used within or outside the function.

////
///

### Pound Signs in Comments


Traditionally, a single pound sign (`#`) is used to denote a comment in Bash scripts. However, using a different number of pound signs can help differentiate the comments' purpose and scope.

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

- **Usage**: Use two-pound signs for comments that describe the functionality of a block of code, such as a loop, conditional sequence, control structure, or a group of related variable declarations. Place these comments directly above the relevant code block.
- **Blank Lines**:  comment applies only to the block of code immediately following it. If a blank line is inserted between a comment and a block of code, it indicates that the comment does not apply to that code block, allowing for a clear separation between different logical units or groups of code.

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
- **Sparingly**: Use quadruple-pound signs sparingly, primarily when it’s necessary to visually distinguish major sections of code. Excessive use of quadruple-pound signs can make the script harder to read.
- **Omitting `####`**: Quadruple-pound signs can be omitted if the script is relatively short, well-structured, and easy to navigate without additional sectioning.
- **Subsections**: Quadruple-pound signs can also be used to indicate subsections within a larger section of the script. However, this should be done very sparingly. Consider using [triple-pound signs](#__tabbed_4_4) (`###`) before resorting to quadruple-pound signs for subsections.
- **Formatting**: Quadruple pound sign comments should be formatted to ensure the transition between sections is clear and visually distinct. Below are the recommended formatting guidelines:
    - **Section Naming**: Append `[ section_name ]` to the end of `####`, replacing `section_name` with a descriptive title for that section. The section name should clearly indicate the content or purpose of that section.
    - **Filler Characters**: After the section name, append a series of `#` characters to fill the remaining space up to the 88-character limit.
    - **Section Comments**: If necessary, you may add comments directly below the initial `####` line, prefixing them with four `#` characters.
    - **Spacing**: As mentioned in the [vertical spacing guidelines](#vertical-spacing), provide two blank lines before and after the quadruple pound signs to enhance the visual separation between sections.
    - **Subsection Format**: Subsections should follow the same format, with the number of brackets (`[]`) indicating the depth of the subsection within the script. The deeper the subsection, the more brackets should be used. Use filler characters to maintain a consistent 88-character width.

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

1. **Triple Pound Signs**: As mentioned in the guidelines, consider using triple-pound signs (`###`) before resorting to quadruple-pound signs for subsections. Excessive use of quadruple-pound signs can make the script harder to read.

/////
////
//// tab | Triple Pound Signs (`###`)

- **Description**: Triple pound signs serve as a middle ground between double and quadruple-pound signs. They are used when blocks or lines of code require some distinction but do not necessitate a completely new section.
- **Usage**: Use triple pound signs where the code is different enough to warrant distinction but not significant enough to be placed in an entirely new section.
- **Formatting**: Triple pound sign comments should be formatted to ensure the transition between differing blocks of code is clear and visually distinct. Below are the recommended formatting guidelines:
    - **Section Naming**: Append `[ section_name ]` to the end of `###`, replacing `section_name` with a descriptive title for that section. The section name should clearly indicate the content or purpose of the code below it.
    - **Filler Characters**: After the section name, append a series of `#` characters to fill the remaining space up to the 88-character limit. Additionally, place three `#` characters above and below the section name line.
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


## Functions to perform various tasks...
```

/////
////
///

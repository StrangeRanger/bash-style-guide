# Style

This section focuses on style-related guidelines that directly impact the functionality of Bash scripts. It covers essential topics such as the proper use of quotes, best practices for variable declarations, and the correct way of writing shebang lines.

## Using Quotes

In Bash, the type of quotes you use—single, double, or none—directly affects how strings and variables are interpreted. Knowing when and how to use each one is essential to avoid common issues with [word splitting](https://mywiki.wooledge.org/WordSplitting) (1), [globbing](https://mywiki.wooledge.org/glob) (2), and [parameter expansion](https://mywiki.wooledge.org/BashGuide/Parameters#Parameter_Expansion) (3).
{ .annotate }

1. **Word splitting**: In Bash, word splitting occurs when the shell divides a string into multiple words or tokens based on the value of the `IFS` (Internal Field Separator) variable. This typically happens during variable expansion or command substitution, where the expanded value is broken into words before being processed.
2. **Globbing**: In Bash, globbing refers to the pattern-matching process that expands wildcard characters (`*`, `?`, `[...]`) into corresponding filenames or directory names. When you use a pattern with wildcards, Bash automatically matches it to existing files or directories, enabling flexible file and directory operations without needing to specifying exact names.
3. **Parameter expansion**: In Bash, parameter expansion is the process of replacing a variable or parameter with its value, optionally allowing for modifications such as setting default values, trimming characters, or substituting patterns. This expansion occurs when a parameter is referenced with a syntax like `${variable}`.

/// admonition | Guidelines
    type: info
//// tab | Double Quotes (`"`)

- **First Choice**: Double quotes should often be your first choice when quoting strings and variables.
    - **Reason**: Double quotes allow the interpretation of variables, command substitution, and escape sequences (e.g., `\n`, `\t`) within the quoted string while preventing word splitting and globbing.

///// admonition | Example
    type: example

```bash
name="John Doe"
echo "Hello, $name"
```

/////
////
//// tab | Single Quotes (`'`)

- **String Literals**: Use single quotes when you need to prevent the interpretation of variables, command substitution, and escape sequences within the string. This is especially important for commands like `find`, `grep`, and `awk` that interpret special characters.
    - **Reason**: Single quotes prevent all forms of expansion, ensuring the enclosed text is treated as a literal string. This is particularly useful when working with patterns or special characters that should not be expanded by the shell. (1)
        { .annotate }

        1. Although the type of quotes doesn’t directly affect how commands like `grep` handle their own pattern matching, using single quotes ensures that the shell passes the arguments exactly as written, without performing any expansions.

///// admonition | Example
    type: example

_Literal string_:

```bash
echo 'This is a literal $string with no variable substitution'
```

---

_Command argument_:

```bash
find . -name '*.txt' -exec grep 'pattern.*here' {} \; -print
```



/////
////
//// tab | Omitting Quotes

- **When to Omit Quotes**: In Bash, there are cases where quotes (primarily double quotes) can be safely omitted:
    - **Arithmetic Operations**: Quotes are not required when performing arithmetic operations within `$(( ... ))`.
    - **Double Bracket Tests**: You can safely omit quotes around variables within double brackets, as `[[ ... ]]` inherently protects against word splitting and globbing.

///// admonition | Example
    type: example

```bash
# Arithmetic operations.
sum=$((5 + 3))

## Double brackets for tests.
if [[ $variable == "value" ]]; then
    echo "Matched"
fi
```

/////
////
//// tab | Recommendations

- **Syntax Highlighting**: Ensure syntax highlighting is enabled in your IDE or text editor. Syntax highlighting helps quickly identify potential issues in strings, such as missing quotes, special characters, or escaped characters.
- **When in Doubt, Quote**: If you’re unsure whether to quote a string or variable, it’s generally safer to quote it.

////
///

## Declaring and Naming Variables

Bash offers several methods for declaring variables, each with implications for scope, immutability, and readability. Adhering to best practices for naming and declaring variables is crucial for maintaining script consistency and avoiding unintended side effects.

/// admonition | Guidelines
    type: info
//// tab | Global and Local Variables

- **Naming Conventions**: Use lowercase letters with words separated by underscores (`_`).
    - **Reason**: Lowercase letters for standard variables help distinguish them from constants and exported variables, which are conventionally written in uppercase.
    - **Example**:
        ```bash
        my_var="local value"
        ```
- **Scope Management**: Use the `local` keyword to limit a variable's scope to within a function.
    - **Reason**: Declaring a variable as `local` confines its scope and lifespan to the function, preventing accidental overwrites of global variables with the same name.
    - **Example**:
        ```bash
        my_var="global value"

        my_function() {
            # This local variable won't overwrite the global variable.
            local my_var="local value"
            echo "$my_var"
        }

        my_function
        echo "$my_var"
        ```
        **Explanation**: In this example, `my_var` is declared both globally and locally within the `my_function` function. The `local` keyword ensures that the `my_var` inside the function is distinct from the global `my_var`. When the function is called, it outputs the local value of `my_var`, while the final echo statement outputs the global value.

////
//// tab | Constant Variables

- **Naming Conventions**: Use uppercase letters, separating words with underscores (`_`) and prefixing the variable name with `C_`.
    - **Reason**: The `C_` prefix, combined with uppercase letters, clearly indicates that the variable is a constant. This convention aligns with common industry practices (1), making your code easier to understand by other developers. It also reduces the likelihood of accidental modification by distinguishing constants from other types of variables.
        { .annotate }

        1. **Common industry practices**: Common practices for naming constants involve using uppercase letters with words separated by underscores (`_`). This convention is widely adopted across various programming languages, including `C`, `Python`, and `Java`. The `C_` prefix is an additional convention in these guidelines to further emphasize the immutability and constant nature of the variable.

    - **Alternative Prefix**: While` C_` is the default prefix in these guidelines, it can be customized to align with your project's naming conventions. For instance, you may choose a prefix related to your project’s name. A notable example is the `OMZ_` prefix used by the [oh-my-zsh project](https://github.com/ohmyzsh/ohmyzsh).
    - **Example**:
        ```bash
        C_CONFIG_FILE_PATH="/etc/myapp/config.conf"
        ```

- **Selective Use of `readonly`**: Apply `readonly` at or immediately after initializing constants that must remain unchanged throughout the script.
    - **Reason**: The `readonly` keyword enforces immutability, preventing critical values from being altered accidentally. However, overusing `readonly` can make scripts overly restrictive, hindering development and debugging. Therefore, it should be reserved for _truly_ immutable values.
    - **When to use**:
        - **Global Constants**: Use `readonly` for global constants where changes could significantly impact script behavior, potentially leading to unexpected results.
    - **When to omit**:
        - **Function-Dependent Initialization**: Omit `readonly` when a function or complex operation determines a constant's value.
            - **Reason**: Applying `readonly` to a variable whose value is set by a function can cause errors if the function is called multiple times. Unless checks are in place to prevent reinitialization, it's best to omit `readonly` in such cases.

///// details | Example
    type: example

```bash
#!/bin/bash

## Constant variables using the naming convention with 'C_' prefix.
readonly C_CONFIG_FILE_PATH="/etc/myapp/config.conf"
readonly C_MAX_RETRIES=5
readonly C_API_ENDPOINT="https://api.example.com/data"

## Function that determines a value based on conditions.
initialize_dynamic_constant() {
    local environment="$1"

    if [[ "$environment" == "production" ]]; then
        C_DYNAMIC_URL="https://prod.example.com/api"
    else
        C_DYNAMIC_URL="https://dev.example.com/api"
    fi
}

# Call the function to initialize the dynamic constant.
initialize_dynamic_constant "production"

# Use the initialized values (note that C_DYNAMIC_URL is not readonly).
echo "API Endpoint: $C_API_ENDPOINT"
echo "Max Retries: $C_MAX_RETRIES"
echo "Configuration File Path: $C_CONFIG_FILE_PATH"
echo "Dynamic URL: $C_DYNAMIC_URL"

# Attempting to modify a readonly variable will result in an error.
# Uncommenting the line below will cause the script to fail.
#C_API_ENDPOINT="https://newapi.example.com"

## Function to perform an operation based on the constants.
perform_operation() {
    for ((i = 1; i <= C_MAX_RETRIES; i++)); do
        echo "Attempt $i: Fetching data from $C_API_ENDPOINT"
        sleep 1  # Simulate API call.
        break  # Break after first attempt for demo purposes.
    done
}

# Perform the operation using the constants.
perform_operation
```

/////
////
//// tab | Exported Variables

- **Naming Conventions**: Use uppercase letters, separating words with underscores (`_`) and prefixing the variable name with `E_`.
    - **Reason**: The `E_` prefix, combined with uppercase letters, clearly indicates that the variable is an exported/environment variable. This convention aligns with standard Unix coding practices for environment variables, typically using uppercase letters and underscores. It also helps differentiate exported variables from other types of variables, making your code easier to read and maintain.
    - **Example**:
        ```bash
        export E_PATH="$HOME/.local/bin/"
        ```
- **Declaration**: Use `export` at the time of, or immediately after, initializing exported variables.
    - **Reason**: Immediately exporting variables after initialization ensures that they are available to child processes and subshells.

///// details | Example
    type: example

```bash
# Overwrites the existing $PATH environment variable.
export PATH="$HOME/.local/bin/"

## Does NOT overwrite the existing $PATH environment variable.
E_PATH="$HOME/.local/bin/"
export E_PATH
```

/////
////
//// tab | Selective Use of `declare`

- **Guideline**: Use `declare` to manage advanced variable attributes, such as associative arrays.
    - **Reason**: The `declare` command is essential for handling complex declarations like associative arrays or when specific variable attributes are needed. However, it is typically unnecessary for simple variable declarations.

///// details | Example
    type: example

```bash
# Associative array containing the configuration settings for sshd_config.
declare -A C_SSHD_CONFIG=(
    ["LogLevel"]="VERBOSE"
    ["LoginGraceTime"]="30"
    ["PermitRootLogin"]="no"
    ["MaxAuthTries"]="3"
    ["MaxSessions"]="2"
    ["PubkeyAuthentication"]="yes"
    ["PermitEmptyPasswords"]="no"
    ["ChallengeResponseAuthentication"]="no"
    ["KbdInteractiveAuthentication"]="no"
    ["UsePAM"]="yes"
    ["AllowAgentForwarding"]="no"
    ["AllowTcpForwarding"]="no"
    ["X11Forwarding"]="no"
    ["PrintMotd"]="no"
    ["TCPKeepAlive"]="no"
    ["Compression"]="no"
    ["ClientAliveInterval"]="300"
    ["ClientAliveCountMax"]="2"
)
readonly C_SSHD_CONFIG
```

/////
////
///

## Shebang in Bash Scripts

In Bash scripting, the shebang (`#!`) is a crucial component that defines which interpreter should execute the script. The choice of shebang can significantly impact the script's compatibility and behavior across different operating systems and environments. Understanding how different shebangs work and when to use them is essential for writing robust Bash scripts.

/// admonition | Guidelines
    type: info

- **Universal Compatibility**: Use `#!/usr/bin/env bash` for scripts that must run on various operating systems, including BSD, macOS, Linux, and Windows Subsystem for Linux (WSL).
    - **Reason**: This shebang uses the `env` command to search the user's `PATH` and locate the first instance of the Bash executable, ensuring that the script finds Bash regardless of its installation location.
- **Linux-Specific Scripts**: For scripts intended to run exclusively on Linux, use `#!/bin/bash`.
    - **Reason**: On Linux systems, Bash is typically installed at `/bin/bash`. This shebang is ideal for scripts that are written exclusively for Linux, where cross-platform portability is not a concern.
- **Other Considerations**: For environments with non-standard Bash installations, specify the exact path to the Bash interpreter in the shebang to ensure the script runs correctly.
    - **Example**:
        ```bash
        #!/usr/local/bin/bash
        echo "This script is designed for environments where Bash is installed in a non-standard location."
        ```
///

/// admonition | Why the Choice Matters
    type: tip

- **Executable Location**: On systems like BSD and macOS, Bash may be installed in a different location compared to Linux. Sometimes, the default Bash version can be significantly outdated, prompting users to install an alternative version. For example, many macOS users upgrade Bash via [Homebrew](https://brew.sh/), which installs it at `/usr/local/bin/bash` on Intel Macs or `/opt/homebrew/bin/bash` on Apple Silicon, while the system version remains at `/bin/bash`.
- **Script Portability**: The choice of shebang affects whether your script can be executed on different systems without modification. Using `#!/usr/bin/env bash` enhances portability across platforms by ensuring that the script can locate the Bash interpreter regardless of its installation path.
- **System Compliance and Security**: In environments where system integrity and security are critical, using a direct path like `#!/bin/bash` ensures that the script runs with the system’s default, vetted Bash interpreter, reducing the risk of unintended behavior from using an alternative version.

///

# Style

This section provides style-related guidelines essential for writing functional and robust Bash scripts. It covers best practices for using quotes, declaring variables, and writing shebangs.

## Using Quotes

In Bash, the type of quotes you use—single, double, or none—determines how strings and variables are interpreted. Understanding when to use each type is essential to prevent common pitfalls, such as unintended [word splitting](https://mywiki.wooledge.org/WordSplitting), [globbing](https://mywiki.wooledge.org/glob), and [parameter expansion](https://mywiki.wooledge.org/BashGuide/Parameters#Parameter_Expansion).

/// admonition | Guidelines
    type: info
//// tab | Double Quotes (`"`)

- **First Choice**: Use double quotes as the default method for quoting strings and variables.
    - **Reason**: Double quotes allow the shell to interpret variables, command substitutions, and escape sequences (e.g., `\n`, `\t`) while preventing word splitting and globbing.

///// admonition | Example
    type: example

```bash
name="John Doe"
echo "Hello, $name"
```

/////
////
//// tab | Single Quotes (`'`)

- **String Literals**: Use single quotes to treat enclosed text exactly as written, without any form of expansion.
    - **Reason**: Single quotes preserve the string as a literal, preventing any form of expansion by the shell. This is crucial for commands like `find`, `grep`, and `awk`, which have their own rules for interpreting special characters. Using single quotes ensures that these commands receive the text exactly as written, without modification by the shell.

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

- **When to Omit Quotes**: In Bash, certain situations allow for safely omitting quotes:
    - **Arithmetic Operations**: Quotes are not required when performing arithmetic operations within `$(( ... ))`, as [arithmetic expansion](https://mywiki.wooledge.org/ArithmeticExpression) treats the content as a single unit, preventing word splitting and globbing.
    - **Double Bracket Tests (`[[ ... ]]`)**: Quotes around variables can be omitted within double brackets. The `[[ ... ]]` syntax is a Bash built-in that protects against word splitting and globbing, allowing comparisons and checks without needing quotes.

///// admonition | Example
    type: example

```bash
# Arithmetic operation.
sum=$((5 + 3))

## Double brackets to perform a comparison test.
if [[ $variable == "value" ]]; then
    echo "Matched"
fi
```

/////
////
//// tab | Recommendations

- **Syntax Highlighting**: Ensure syntax highlighting is enabled in your IDE or text editor. This helps identify potential issues, such as missing quotes or misplaced escape sequences and special characters.
- **When in Doubt, Quote**: If unsure whether to use quotes, it's generally safer to include them to avoid unexpected behavior.

////
///

## Declaring and Naming Variables

Bash provides several methods for declaring variables, each with implications for scope, immutability, and readability. Following best practices for naming and declaring variables is essential to maintain consistency and avoid unintended side effects

/// admonition | Guidelines
    type: info
//// tab | Global and Local Variables

- **Naming Conventions**: Use `snake_case` for variable names.
    <!-- - **Reason**: Snake case helps differentiate standard variables from constants and exported variables, which are conventionally written in uppercase. -->
- **Scope Management**: Use the `local` keyword to limit a variable's scope to within a function.
    - **Reason**: Declaring a variable as `local` prevents unintended overwrites of global variables with the same name. It also ensures that once the function completes, the variable is unset and released from memory. (1)
        { .annotate }

        1. **Variable Scope**: In Bash, variables declared inside a function without the `local` keyword are global by default, meaning they persist beyond the function scope and can impact other parts of the script.

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
        **Explanation**: In this example, `my_var` is defined both globally and locally within `my_function`. The `local` keyword ensures that the `my_var` inside the function is distinct from the global `my_var`. When the function is called, it outputs the local value, whereas the final `echo` statement outputs the global value.

////
//// tab | Constant Variables

- **Naming Conventions**: Use `UPPER_SNAKE_CASE` with a `C_` prefix for constants (e.g., `C_CONFIG_FILE_PATH`).
    - **Reason**: This naming convention helps distinguish constants from other variables and aligns with common practices across various programming languages. (1)
        { .annotate }

        1. **Constants and Environment Variables**: Constants typically use `UPPER_SNAKE_CASE`, a standard adopted in many languages, such as C, Python, and Java. Sticking to a common convention ensures that other developers can easily identify constants in your scripts. However, in Unix-like systems, environment variables are also written in `UPPER_SNAKE_CASE`. Using the same naming convention without any variation can lead to confusion. A prefix such as `C_` is used to differentiate constants from environment variables.

    - **Alternative Prefix**: `C_` is the default prefix for constants. However, you can use other prefixes, like `CONST_` or `CONFIG_`, if they better suit your naming conventions or project requirements. The key is to maintain consistency within your script or project.
        - **Example**: An example of an alternative prefix is the use of `OMZ_` for the [oh-my-zsh project](https://github.com/ohmyzsh/ohmyzsh). The prefix clearly indicates that this constant is part of the oh-my-zsh project while following the `UPPER_SNAKE_CASE` convention.

- **Selective Use of `readonly`**: Use `readonly` to define constants that must remain unchanged throughout the script.
    - **Reason**: The `readonly` keyword enforces immutability, preventing accidental modification of critical values. However, overusing `readonly` can make scripts overly restrictive, complicating development and debugging. Therefore, reserve `readonly` for _truly_ immutable values.
    - **Declaration**: Use `readonly` during variable initialization or immediately afterward.
    - **Example of When to Use**:
        - **Global Constants**: Apply `readonly` to global constants where changes could significantly affect script behavior or cause unexpected results.
    - **Example of When to Omit**:
        - **Function-Dependent Initialization**: Avoid using `readonly` when a constant's value is determined by a function or a complex operation.
            - **Reason**: Applying `readonly` to a variable initialized by a function can cause errors if the function is called multiple times. To avoid reinitialization errors, omit `readonly` unless safeguards are in place to prevent reassignment.

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

- **Naming Conventions**: Use `UPPER_SNAKE_CASE` with an `E_` prefix for exported variables (e.g., `E_PATH`).
    - **Reason**: This naming convention helps distinguish exported variables from other variable types and aligns with standard Unix practices. (1)
        { .annotate }

        1. **Exported and Environment Variables**: Under Unix conventions, environment variables are written in `UPPER_SNAKE_CASE`. However, just like constants, using the same naming convention for exported variables can cause confusion and lead to accidental modifications of existing environment variables. A prefix such as `E_` is used to differentiate exported variables from others while adhering to the `UPPER_SNAKE_CASE` convention.

- **Declaration**: Use `export` when initializing an exported variable or immediately afterward.

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

- **Guideline**: Use `declare` to manage advanced variable attributes, such as for associative arrays.
    - **Reason**: The `declare` command is essential for handling complex variable declarations, such as associative arrays, or when specific attributes are required. It is generally unneeded for simple variable declarations.

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

The shebang (`#!`) in Bash scripts is a crucial component that specifies the interpreter that should execute the script. The choice of shebang can significantly impact a script's compatibility and behavior across different operating systems and environments. Understanding the purpose and appropriate usage of different shebangs is essential for writing robust Bash scripts.

/// admonition | Guidelines
    type: info

- **Universal Compatibility**: Use `#!/usr/bin/env bash` for scripts that must run on various operating systems, including BSD, macOS, Linux, and Windows Subsystem for Linux (WSL).
    - **Reason**: This shebang uses the `env` command to search the user's `PATH` and locate the first instance of the Bash executable, ensuring the script finds Bash regardless of its installation location.
- **Linux-Specific Scripts**: Use `#!/bin/bash` for scripts intended to run exclusively on Linux.
    - **Reason**: On Linux systems, Bash is typically installed at `/bin/bash`. This shebang is suitable for scripts designed exclusively for Linux, where cross-platform compatibility is not required.
- **Non-Standard Installations**: For environments with non-standard Bash installations, specify the exact path to the Bash interpreter in the shebang to ensure correct execution.
    - **Example**:
        ```bash
        #!/usr/local/bin/bash
        echo "This script is designed for environments where Bash is installed in a non-standard location."
        ```
///

/// admonition | Why the Choice Matters
    type: tip

- **Executable Location**: On systems like BSD and macOS, Bash may be installed in a different location than on Linux. Sometimes, the default Bash version is outdated, prompting users to install an alternative version. For example, many macOS users upgrade Bash via [Homebrew](https://brew.sh/), which installs it at `/usr/local/bin/bash` on Intel Macs or `/opt/homebrew/bin/bash` on Apple Silicon, while the system version remains at `/bin/bash`.
- **Script Portability**: The shebang choice affects whether your script can be executed on different systems without modification. Using `#!/usr/bin/env bash` enhances portability by ensuring the script can locate the Bash interpreter regardless of its installation path.
- **System Compliance and Security**: In environments where system integrity and security are critical, using a direct path like `#!/bin/bash` ensures the script runs with the system’s default, vetted Bash interpreter, reducing the risk of unintended behavior from using an alternative version.

///

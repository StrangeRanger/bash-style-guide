# Style

This section focuses on style-related guidelines that directly impact the functionality of Bash scripts. It covers essential topics such as the correct use of quotes, variable declarations, and writing shebang lines.

## Using Quotes

The choice of quotes in Bash significantly impacts how strings and variables behave. Understanding when to use single, double, or no quotes is crucial for avoiding issues such as word splitting, globbing, and unintended variable expansion.

/// admonition | Guidelines
    type: info
//// tab | Double Quotes (`"`)

- **Usage**: Use double quotes as the default method for quoting strings in Bash.
    - **Reason**: Double quotes allow variable expansion while preventing word splitting and globbing. This feature makes double quotes the safest and most flexible option for most scenarios.

///// admonition | Example
    type: example

```bash
name="John Doe"
echo "Hello, $name"
```

/////
////
//// tab | Single Quotes (`'`)

- **Usage**: Use single quotes when you need to preserve string literals exactly as written.
    - **Reason**: Single quotes prevent all forms of expansion, ensuring the enclosed text is treated as a literal string.

///// admonition | Example
    type: example

```bash
name="John Doe"
echo 'Hello, $name'
```

/////
////
//// tab | Omitting Quotes

- **Guideline**: Quotes can be omitted when they aren't necessary, such as within `[[ ... ]]` statements, or when word splitting and globbing are intended.

///// admonition | Example
    type: example

```bash
name="John Doe"

if [[ $name == John* ]]; then
    echo "Name starts with John"
fi
```

/////
////
//// tab | Recommendations

- **Syntax Highlighting**: Ensure syntax highlighting is enabled in your IDE or text editor. Syntax highlighting helps you quickly identify potential issues in a string, such as missing quotes, special characters, or escaped characters.

////
///

## Declaring and Naming Variables

Bash provides several methods for declaring variables, each with implications for scope, immutability, and readability. Understanding the best practices for naming and declaring global, local, constant, and exported variables is essential for maintaining script consistency and preventing unintended side effects.

/// admonition | Guidelines
    type: info
//// tab | Global and Local Variables

<!-- TODO: Modify and ensure the wording the 'Explanation' is to my liking. -->

- **Naming Conventions**: Use lowercase letters, with words separated with underscores (`_`).
    - **Reason**: Using lowercase letters for standard variables help to distinguish from constants or exported variables, which are conventionally written in uppercase.
    - **Example**:
        ```bash
        my_var="local value"
        ```
- **Scope Management**: Use the `local` keyword to limit a variable's scope to within a function.
    - **Reason**: Declaring a variable as `local` ties its lifespan and scope to the function. This practice prevents accidental overwrites of global variables with the same name.
    - **Example**:
        ```bash
        my_var="global value"

        my_function() {
            local my_var="local value"
        echo "$my_var"
        }

        my_function
        echo "$my_var"
        ```
        **Explanation**: In this example, `my_var` is declared both globally and locally within the `my_function` function. The local keyword ensures that the `my_var` inside the function is distinct from the global `my_var`. When the function is called, it outputs the local value of `my_var`, while the final echo statement outputs the global value. This illustrates how `local` effectively manages variable scope, keeping global and local variables separate and avoiding conflicts.
- **Global Variable Declaration**: Declare global variables at the beginning of the script or in a separate configuration file.
    - **Reason**: Centralizing global variable declaration improves script organization and readability, making it easier to manage and update values.
    - **Exception**: If a variable's value is determined later in the script, consider one of the following approaches:
        - **Option 1**: Declare the variable at the beginning with a placeholder value and update it later in the script. Add comments to clarify the placeholder.
        - **Option 2**: Initialize the variable when its value is determined. While this approach is acceptable, it can make tracking global variables more challenging.


<!-- TODO: Add a general example? -->
<!-- ///// details | Example
    type: example

```bash
...
```

///// -->
////
//// tab | Constant Variables

- **Naming Conventions**: Use uppercase letters, separate words with underscores (`_`), and prefix the variable name with `C_`.
    - **Reason**: The `C_` prefix, combined with uppercase letters, signals that the variable is a constant. This naming convention helps distinguish constants from other variables, reducing the risk of accidental changes.
    - **Example**:
        ```bash
        C_CONFIG_FILE_PATH="/etc/myapp/config.conf"
        ```
- **Selective Use of `readonly`**: Apply `readonly` at or directly after initializing constants that must remain unchanged throughout the script.
    - **Reason**: `readonly` enforces immutability, protecting critical values from being altered accidentally. However, overusing `readonly` can make scripts too restrictive, hindering development and debugging. Therefore, `readonly` should be reserved for truly immutable values.
    - **When to use**:
        - **Global Constants**: Set `readonly` for global constants whose values significantly impact script behavior and where modifications could lead to unexpected results.
        - **Security-Sensitive Variables**: Use `readonly` for variables where changes could pose security risks (e.g., file paths, user permissions).
    - **When to omit**:
        - **Function-Dependent Initialization**: When a function or complex operation determines a constant's value, omitting `readonly` is usually the safest option.
            - **Reason**: Applying `readonly` to a variable whose value is determined by a function can result in errors if the function is called multiple times. Even if there is no intention to change the value, the potential for multiple function calls can lead to unexpected behavior and errors.
- **Global Constants**: Declare global constants at the beginning of the script or in a separate configuration file.
    - **Reason**: Centralizing constant declarations makes managing and updating values easier and improves script readability.
    - **Exception**: If a constant's value is determined later in the script, choose one of the following methods:
        - **Option 1**: Declare with a placeholder value, update it later, and apply readonly if needed. Add comments to clarify the placeholder.
        - **Option 2**: Initialize the variable when its value is determined, applying readonly if appropriate. While this approach is acceptable, it can make tracking global constants more challenging.

///// details | Example
    type: example

**NOTE**: There may be better examples than the following, but hopefully, it will still help you understand the concept.

```bash
#!/bin/bash

# Global constants.
C_CONFIG_FILE_PATH="/etc/myapp/config.conf"  # Centralized for easy reference.
readonly C_CONFIG_FILE_PATH

# Omitting readonly for flexibility during development.
C_TMP_DIR="/tmp/myapp"

# Function-dependent initialization.
initialize_cache_dir() {
  C_CACHE_DIR="$1"
}

setup_environment() {
  mkdir -p "$C_TMP_DIR"
  mkdir -p "$C_CACHE_DIR"
  chmod "$C_USER_PERMISSIONS" "$C_TMP_DIR"
}

# Example initialization.
initialize_cache_dir "/var/cache/myapp/$(date +%Y%m%d)"
setup_environment
```

/////
////
//// tab | Exported Variables

<!-- TODO: Maybe update guidelines and examples... -->

- **Naming Conventions**: Use uppercase letters, separate words with underscores (`_`), and prefix the variable name with `E_`.
    - **Reason**: The` E_` prefix, combined with uppercase letters, signals that the variable is an exported variable. This naming convention helps distinguish it from constants and other environment variables, reducing the risk of conflicts.
    - **Example**:
        ```bash
        export E_PATH="$HOME/.local/bin/"
        ```
- **Declaration**: Use `export` at the time of, or immediately after, variable assignment.
    - **Reason**: Explicitly exporting variables ensures they are available to child processes and other scripts.
    - **Example**:
        ```bash
        E_PATH="$HOME/.local/bin/"
        export E_PATH
        ```

///// details | Example
    type: example

```bash
# Overwrites the existing $PATH environment variable.
export PATH="$HOME/.local/bin/"

# Does NOT overwrite the existing $PATH environment variable.
export E_PATH="$HOME/.local/bin/"
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

On Unix-like systems, the shebang (`#!`) line at the beginning of a script specifies the interpreter that should execute the script. In Bash, the choice of shebang can significantly impact the portability and compatibility of scripts across different operating systems. Understanding the differences between `#!/bin/bash` and `#!/usr/bin/env bash` is crucial for ensuring that scripts run as intended.

/// admonition | Guidelines
    type: info

- **Universal Compatibility**: Use `#!/usr/bin/env bash` for scripts that must run on various operating systems, including BSD, macOS, Linux, and even Windows Subsystem for Linux (WSL).
    - **Reason**: This shebang searches the user's `PATH` to locate the first instance of the Bash executable, ensuring bash can be found regardless of its location.
    - **Example**:
        ```bash
        #!/usr/bin/env bash
        echo "This script is compatible with multiple operating systems."
        ```
- **Linux-Specific Scripts**: When writing scripts specifically for Linux environments, opt for `#!/bin/bash`.
    - **Reason**: Linux systems typically have Bash installed at `/bin/bash`, making this shebang ideal for Linux-specific scripts where portability is not a concern.
    - **Example**:
        ```bash
        #!/bin/bash
        echo "This script is intended for Linux systems."
        ```
- **Other Considerations**: Ensure the shebang reflects the intended interpreter location to avoid compatibility issues when writing scripts for specific environments.
    - **Example**:
        ```bash
        #!/usr/local/bin/bash
        echo "This script is designed for a specific Bash location."
        ```

///

/// admonition | Why the Choice Matters
    type: tip

<!-- TODO: Maybe include info about why using a specific location matters. -->

- **Variability in Bash Locations**: On non-Linux systems like BSD and macOS, Bash is often located in a different path than on Linux, or the installed version may be older. For instance, many macOS users upgrade Bash via [Homebrew](https://brew.sh/), which installs it at `/usr/local/bin/bash` (Intel Macs) or `/opt/homebrew/bin/bash` (Apple Silicon), while the system version remains at `/bin/bash`.
- **Path Flexibility**: Using `#!/usr/bin/env bash` provides significant flexibility. It enables the script to use the Bash version installed in the user's environment, which is crucial for accessing features available in newer Bash versions. This approach is handy when the default Bash version is outdated or non-standard.

///

# Style

As mentioned in the [Aesthetics](aesthetics.md) section of this Guide, this section covers style-related guidelines that are less subjective and affect functionality. It covers topics such as the correct use of quotes, variable declarations, and writing shebang lines.

## Using Quotes

The choice of quotes in Bash significantly impacts how strings and variables behave. Understanding when to use single, double, or no quotes is crucial for avoiding issues such as word splitting, globbing, and unintended variable expansion.

/// admonition | Guidelines
    type: info
//// tab | Double Quotes

- **Usage**: Use double quotes (`"`) as the default method for quoting strings in Bash.
- **Reason**: Double quotes allow variable expansion while preventing word splitting and globbing. This makes them the safest and most flexible option for most scenarios.

///// admonition | Example
    type: example

```bash
name="John Doe"
echo "Hello, $name"
```

/////
////
//// tab | Single Quotes

- **Single Quotes**: Use single quotes (`'`) when you need to preserve string literals exactly as written, without any variable expansion or command substitution.
- **Reason**: Single quotes prevent all expansions, ensuring the enclosed text is treated as a literal string.

///// admonition | Example
    type: example

```bash
echo 'Hello, $name'
```

/////
////
//// tab | Omitting Quotes

- **Guideline**: It’s acceptable to omit quotes in contexts where they aren't necessary, such as within `[[ ... ]]` statements or when word splitting and globbing are intended.
- **Reason**: Omitting quotes is sometimes necessary for specific operations, but should be done cautiously to avoid unintended behavior.

///// admonition | Example
    type: example

```bash
if [[ $name == John* ]]; then
    echo "Name starts with John"
fi
```

/////
////
//// tab | Recommendations

- **Syntax Highlighting**: Ensure your IDE or text editor has syntax highlighting enabled. Syntax highlighting helps identify special characters in strings, making it easier to spot expansions and other potential issues.

////
///

## Declaring and Naming Variables

Bash provides several methods for declaring variables, each with implications for scope, immutability, and readability. Understanding the best practices for naming and declaring global, local, constant, and exported variables is essential for maintaining script consistency and preventing unintended side effects.

/// admonition | Guidelines
    type: info
//// tab | Global and Local Variables

- **Naming Conventions**: Use lowercase letters for variable names, separating words with an underscore (`_`).
    - **Reason**: Lowercase naming helps distinguish between regular variables and constants or exported/environment variables, which are conventionally in uppercase.
    - **Example**:
        ```bash
        my_var="local value"
        ```
        **Note**: Avoid special characters, starting variable names with numbers, or using spaces to prevent syntax errors.
- **Scope Management**: Use the `local` keyword to limit a variable's scope within a function.
    - **Reason**: Declaring variables as `local` within functions prevents conflicts with global variables of the same name.
    - **Example**:
        ```bash
        my_function() {
            local my_var="local value"
            echo "$my_var"
        }
        my_function
        ```
        **Advantage**: Using `local` confines the variable's scope to the function, avoiding accidental overwrites of global variables.
- **Global Variable Declaration**: Declare global variables at the beginning of the script or in a separate configuration file.
    - **Reason**: Centralizing global variable declarations enhances script organization and readability, making it easier to manage and update values.
    - **Exception**: If a global variable's value is determined later in the script, consider one of these approaches:
        - **Options 1**: Declare the variable at the beginning with a placeholder value, and update it later in the script. Add comments to clarify the placeholder.
        - **Option 2**: Declare and assign the variable when its value is determined. This method is acceptable but can make tracking global variables more difficult.

///// details | Examples
    type: example

_General variable declaration:_

```bash
my_var="local value"
```

**Note**: Outside of a function, the `local` keyword is not applicable.

---

_Without scope management:_

```bash
my_function() {
    my_var="local value"
    echo "$my_var"
}

my_function
```

**Possible Issue:** If `my_var` is already defined globally, this assignment will overwrite the global value.

---

_With scope management:_

```bash
my_function() {
    local my_var="local value"
    echo "$my_var"
}

my_function
```

**Advantage:** Using `local` confines the variable's scope to the function, preventing conflicts with global variables with the same name.

/////
////
//// tab | Constant Variables

- **Naming Conventions**: Use uppercase letters prefixed with `C_`, separating words with underscores (`_`).
    - **Reason**: The `C_` prefix, combined with uppercase letters, signals that the variable is a constant, helping prevent accidental modification.
    - **Example**:
        ```bash
        C_CONFIG_FILE_PATH="/etc/myapp/config.conf"
        ```
- **Selective Use of `readonly`**: Apply `readonly` at or directly after assignment for constants that must remain unchanged throughout the script.
    - **Reason**: `readonly` enforces immutability, protecting critical values from being altered accidentally.
    - **When to use**:
        - **Global Constants**: Values that ensure script consistency and prevent accidental overwrites.
        - **Security-Sensitive Variables**: Values where changes could pose security risks (e.g., file paths, user permissions).
    - **Example**:
        ```bash
        readonly C_CONFIG_FILE_PATH="/etc/myapp/config.conf"
        ```
- **Omitting `readonly`**: Consider omitting `readonly` in cases where flexibility is necessary.
    - **Reason**: Overusing `readonly` can reduce script flexibility, so reserve it for truly immutable values.
    - **When to omit**:
        - **Development and Debugging Phases**: Temporarily omitting `readonly` can speed up testing and iteration.
        - **Readability and Simplicity**: In simple scripts, avoiding `readonly` can enhance readability.
        - **Function-Dependent Initialization**: When a constant's value is determined by a function or complex operation, omitting `readonly` may be ideal to avoid errors if the function runs multiple times.
- **Global Constants**: Declare global constants at the beginning of the script or in a separate configuration file.
    - **Reason**: Centralizing constant declarations makes managing and updating values easier and improves script readability.
    - **Exception**: If a constant's value is determined later in the script, choose one of the following methods:
        - **Options 1**: Declare with a placeholder value, updating it later and applying readonly if needed.
        - **Option 2**: Declare and assign when the value is determined, applying readonly if appropriate.
        - **NOTE**: Ensure the constant’s value is set before it is used.

///// details | Example
    type: example

```bash
#!/bin/bash

# Global constants
C_CONFIG_FILE_PATH="/etc/myapp/config.conf"  # Centralized for easy reference.
readonly C_CONFIG_FILE_PATH

# Omitting readonly for flexibility during development
C_TMP_DIR="/tmp/myapp"

# Function-dependent initialization
initialize_cache_dir() {
  C_CACHE_DIR="$1"
}

setup_environment() {
  mkdir -p "$C_TMP_DIR"
  mkdir -p "$C_CACHE_DIR"
  chmod "$C_USER_PERMISSIONS" "$C_TMP_DIR"
}

# Example initialization
initialize_cache_dir "/var/cache/myapp/$(date +%Y%m%d)"
setup_environment
```

/////
////
//// tab | Exported Variables

- **Naming Conventions**: Use uppercase letters prefixed with `E_`, separating words with underscores (`_`).
    - **Reason**: The` E_` prefix helps distinguish exported variables from constants and other environment variables, reducing the risk of conflicts.
    - **Example**:
        ```bash
        export E_PATH="$HOME/.local/bin/"
        ```
- **Declaration**: Use export at the time of or immediately after variable assignment.
    - **Reason**: Explicitly exporting variables ensures they are available to child processes and other scripts.
    - **Example**:
        ```bash
        E_PATH="$HOME/.local/bin/"
        export E_PATH
        ```

///// details | Example
    type: example

```bash
# Will overwrite the existing $PATH environment variable.
export PATH="$HOME/.local/bin/"

# Will NOT overwrite the existing $PATH environment variable.
export E_PATH="$HOME/.local/bin/"
```
/////
////
//// tab | Selective Use of `declare`

- **Guideline**: Use `declare` specifically for managing advanced variable attributes, such as associative arrays.
    - **Reason**: While `declare` can be useful, using direct assignment for simple variables reduces complexity and enhances script clarity.
    - **Example**:
        ```bash
        declare -A my_array  # Associative array declaration.
        my_array[key]="value"
        ```

///// details | Example
    type: example

```bash
# Using declare for an associative array
declare -A config_settings
config_settings[host]="localhost"
config_settings[port]="8080"

```

/////
////
///


## Shebang in Bash Scripts

On Unix-like systems, the shebang (`#!`) line at the beginning of a script specifies the interpreter that should execute the script. In Bash, the choice of shebang can significantly impact the portability and compatibility of scripts across different operating systems. Understanding the differences between `#!/bin/bash` and `#!/usr/bin/env bash` is crucial for ensuring that scripts run as intended.

/// admonition | Guidelines
    type: info

<!-- TODO: Potentially remove the example for the "Other Considerations" guideline. -->

- **Universal Compatibility**: Use `#!/usr/bin/env bash` for scripts that need to run on various operating systems, including BSD, macOS, and Linux.
    - **Reason**: This shebang searches the user's PATH to locate the first instance of the Bash executable, ensuring compatibility across different systems.
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
- **Other Considerations**: To avoid compatibility issues when writing scripts for specific environments, ensure the shebang reflects the intended interpreter location.
    - **Example**: For a script that requires a specific version of Bash, you might specify the path directly if you know the environment is controlled, such as:
        ```bash
        #!/opt/bin/bash
        echo "This script uses a specific Bash interpreter."
        ```

///

/// admonition | Why the Choice Matters
    type: tip

- **Variability in Bash Locations**: On non-Linux systems like BSD and macOS, Bash is often located in a different path than on Linux, or the installed version may be older. For instance, many macOS users upgrade Bash via [Homebrew](https://brew.sh/), which installs it at `/usr/local/bin/bash`, while the system version remains at `/bin/bash`. On BSD systems, Bash is often found at `/usr/local/bin/bash` rather than `/bin/bash`.
- **Path Flexibility**: Using `#!/usr/bin/env bash` provides significant flexibility. It enables the script to use the Bash version installed in the user's environment, which is crucial for accessing features available in newer Bash versions. This approach is particularly useful in environments where the default Bash version is outdated or non-standard.

///

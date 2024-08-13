# Style

As mentioned in the [Aesthetics](aesthetics.md) section of this Guide, this section covers style-related guidelines that are less subjective and affect functionality. This includes things like how to use quotes, declare variables, and write shebang lines.

## Using Quotes

The type of quotes used in Bash can significantly impact the behavior of strings and variables. Understanding when to use single, double, or no quotes is essential for preventing word splitting, globbing, and variable expansion issues.

/// admonition | Guidelines
    type: info

- **Double Quotes**: Use double quotes as the default method for quoting strings in Bash.
    - **Reason**: Double quotes are often considered the safest and most versatile method because they offer more flexibility with variable expansion while preventing word splitting and globbing.
- **Single Quotes**: Use single quotes when you want to preserve string literals exactly as written.
    - **Reason**: Single quotes prevent all expansions, treating the enclosed text as a literal string.
- **Omitting Quotes**: In situations where quotes are not necessary, such as within `[[ ... ]]`, or when the effects of word splitting and globbing are desired, omitting quotes is acceptable.
- **Recommendation**: Ensure your IDE or text editor has syntax highlighting. Syntax highlighting can help identify special characters in strings that perform expansions and other potential issues.

///

## Declaring and Naming Variables

<!-- TODO: Revisit this section to ensure everything is worded correctly and all examples to my liking. -->

Bash provides several methods for declaring variables, each with its own implications for scope, immutability, and readability. Understanding the best practices for naming and declaring global, local, constant, and exported variables is essential for maintaining script consistency and preventing unintended side effects.

/// admonition | Guidelines
    type: info
//// tab | Global and Local Variables

- **Naming Conventions**: Use lowercase letters, separating each word with an underscore (`_`).
    - **Reason**: Using lowercase letters helps distinguish between constant and exported/environment variables, which are conventionally in uppercase.
- **Scope Management**: Use the `local` keyword to limit the scope of a variable within a function.
    - **Reason**: Defining local variables within functions prevents conflicts with global variables.
- **Global Variable Declaration**: Declare global variables at the beginning of the script or in a separate configuration file.
    - **Reason**: Centralizing global variable declarations enhances script organization and readability, making managing and updating values easier.
    - **Exception**: If a global variable's value is determined later in the script, there are two options:
        - **Options 1**: Declare the variable at the beginning of the script, assign a placeholder value, and update the value later in the script. Ensure that you comment on the placeholder value to avoid confusion.
        - **Option 2**: Declare and assign the variable when the value is determined. This method is acceptable but can make it harder to keep track of global variables.

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

- **Naming Conventions**: Use uppercase letters prefixed with `C_`, separating each word with an underscore (`_`).
    - **Reason 1**: Uppercase naming with the `C_` prefix signals the immutability of these variables, making them easier to distinguish as constants.
    - **Reason 2**: The `C_` prefix prevents accidental overwriting of environment variables or other constants.
- **Selective Use of `readonly`**: Apply `readonly` at or directly after assignment for constants where preventing modification is <mark>**_CRITICAL_**</mark>.
    - **Reason**: Enforces immutability, ensuring critical values remain unchanged throughout the script.
    - **When to use**:
        - **Global Constants**: Values that must remain unchanged throughout the script to ensure consistency and prevent accidental overwrites.
        - **Security-Sensitive Variables**: Values that, if altered, could lead to a security risk.
            - **Example**: User permissions, file paths, or sensitive data.
- **Omitting `readonly`**: <mark>**_CONSIDER_**</mark> omitting `readonly` even for intended constants in specific situations where flexibility or simplicity is prioritized.
    - **Reason**: Overusing `readonly` can restrict script flexibility, so reserve it for truly immutable values.
    - **When to omit**:
        - **Development and Debugging Phases**: When you need to modify constants temporarily for testing, delaying `readonly` can aid in faster iteration.
        - **Readability and Simplicity**: In simple or short scripts, omitting `readonly` can keep the script easy to read and maintain without unnecessary complexity.
        - **Function-Dependent Initialization**: In situations where a function or complex operation determines the value of a constant, it may be ideal to omit `readonly`.
            - **Reason**: Unless checks are in place to prevent the function's execution multiple times, executing the function again would most likely result in an error if the constant was already declared as `readonly`.
- **Global Constants**: Declare global constants at the beginning of the script or in a separate configuration file for easy reference and modification.
    - **Reason**: Centralizing constant declarations enhances script organization and readability, making managing and updating values easier.
    - **Exception**: If the constant's value is determined later in the script, there are two options:
        - **Options 1**: Declare the variable at the beginning of the script and assign a placeholder value. Update the value later in the script, assigning `readonly` if applicable. Ensure that you comment on the placeholder value to avoid confusion.
        - **Option 2**: Declare and assign the variable when the value is determined, assigning `readonly` if applicable. This method is acceptable but can make it harder to keep track of global constants.
        - **NOTE**: Whichever option you choose, ensure the constant's value is set before it is used.
- **Treatment**: <mark>**_CONSIDER_**</mark> treating these variables as immutable, even if `readonly` is not applied.
    - **Reason**: Consistently treating constant variables as immutable ensures script reliability and maintainability.

///// details | Example
    type: example

<!-- TODO: Revise the example to ensure it aligns with the guidelines. -->

```bash
##!/bin/bash

## Global constants.
C_CONFIG_FILE_PATH="/etc/myapp/config.conf"  # Centralized for easy reference.
C_USER_PERMISSIONS="rwxr-xr-x"  # Permissions for user directories.
## Selectively using `readonly` for critical constants.
readonly C_CONFIG_FILE_PATH
readonly C_USER_PERMISSIONS
# `readonly` is omitted for flexibility during development and debugging. In an
# alternative scenario, it may have been omitted if we wanted to have a default value,
# but also wanted to allow the user to optionally set it via user input.
C_TMP_DIR="/tmp/myapp"

## `readonly` is omitted as the value is determined later in the script.
initialize_cache_dir() {
  C_CACHE_DIR="$1"
}

## Using the constants in a function.
setup_environment() {
  mkdir -p "$C_TMP_DIR"
  mkdir -p "$C_CACHE_DIR"
  chmod "$C_USER_PERMISSIONS" "$C_TMP_DIR"
  echo "Environment setup complete with config: $C_CONFIG_FILE_PATH"
}

# Here, the cache directory might have been set based on user input or other factors.
# For simplicity of this example, we set it directly.
initialize_cache_dir "/var/cache/myapp/$(date +%Y%m%d)"

# Calling the function.
setup_environment
```

/////
////
//// tab | Exported Variables

- **Naming Conventions**: Use uppercase letters prefixed with `E_`, separating each word with an underscore (`_`).
    - **Reason 1**: Uppercase naming with a specific prefix distinguishes these variables as an exported/environment variable, avoiding confusion with non-constant variables.
    - **Reason 2**: The `E_` prefix helps prevent confusion and accidental overwriting of existing environment variables or other constants.
- **Declaration**: Use the `export` declaration at the time of, or directly after, assignment.
    - **Reason**: Explicitly exporting variables ensures they are immediately available to child processes and other scripts.

///// details | Examples
    type: example

_Without recommended naming conventions:_

```bash
export PATH="$HOME/.local/bin/"
```

**Possible Issue:** If the intention was not to overwrite the environment variable `PATH`, this assignment could lead to unexpected behavior with system commands.

---

_With recommended naming conventions:_

```bash
export E_PATH="$HOME/.local/bin/"
```

**Advantage:** Using the `E_` prefix distinguishes exported variables from other constants and environment variables, reducing the risk of accidental overwriting.

/////
////
//// tab | Selective Use of `declare`

- **Guideline**: Employ `declare` specifically for managing advanced variable attributes, such as associative arrays, and stick to direct assignment for standard variable declarations unless scoping or attributes dictate otherwise.
- **Reason**: While `declare` is useful for setting variables, it's often more than needed for simple assignments. Using direct assignment reduces complexity and enhances script clarity.

///// details | Example
    type: example

<!-- TODO: Add a one or two good examples for this guideline. -->

```bash
```

/////
////
///


## Shebang in Bash Scripts

On Unix-like systems, the shebang (`#!`) line at the beginning of a script specifies which interpreter should execute the script. In Bash, the choice of shebang can impact script portability and compatibility across different operating systems. Understanding the differences between `#!/bin/bash` and `#!/usr/bin/env bash` is crucial for ensuring scripts run as intended.

/// admonition | Guidelines
    type: info

- **Universal Compatibility**: Use `#!/usr/bin/env bash` for scripts that must run on various operating systems, including BSD, macOS, and Linux.
    - **Reason**: This shebang searches the user’s `PATH` to locate the first instance of the Bash executable, ensuring compatibility across different systems.
- **Linux-Specific Scripts**: When creating scripts solely for Linux environments, opt for `#!/bin/bash`.
    - **Reason**: Linux systems typically have Bash installed at `/bin/bash`, making this shebang suitable for Linux-specific scripts.
- **Other Considerations**: To avoid compatibility issues when writing scripts for specific environments, ensure the shebang reflects the intended interpreter location.

///

/// admonition | Why the Choice Matters
    type: tip

- **Variability in Bash Locations**: On non-Linux systems like BSD and macOS, it's common for Bash to be located in a different path than on Linux or for the installed version to be older. For example, many macOS users upgrade Bash through [Homebrew](https://brew.sh/), which does not alter the system-installed version located at `/bin/bash`. On BSD, Bash is often located at `/usr/local/bin/bash` rather than `/bin/bash`.
- **Path Flexibility**: Using `#!/usr/bin/env bash` provides significant flexibility. It enables the script to utilize the Bash version installed in the user's environment, which is crucial for accessing features available in newer Bash versions.

///

/// admonition | Examples
    type: example
//// tab | Universal Compatibility

<!-- TODO: Add one or more examples for each shebang guideline. -->

```bash
```

////
///

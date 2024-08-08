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
- **Omitting Quotes**: In situations where quotes are not necessary, such as within `[[ ... ]]` tests, or when the effects of word splitting and globbing are desired, omitting quotes is acceptable.
- **Recommendation**: Ensure your IDE or text editor has syntax highlighting. Syntax highlighting can help identify special characters in strings that perform expansions and other potential issues.

///

/// details | Examples
    type: example
//// tab | Preventing Word Splitting

_Without quotes:_

```bash
filename='My Documents/Report.txt'
rm $filename
```

**Potential Issue:** Since `$filename` contains a space, `rm` will attempt to remove two files: `My` and `Documents/Report.txt`. This can lead to one or both files not being found or, if they exist, unintendedly removed.

---

_With single quotes:_

```bash
filename="My Documents/Report.txt"
rm "$filename"
```

**Potential Issue:** Single quotes prevent variable expansion, so `$filename` will be treated as a literal string, causing an error because there is no file named `$filename`.

---

_With double quotes:_

```bash
filename="My Documents/Report.txt"
rm "$filename"
```

**Advantage:** The double quotes preserve the variable as a single argument, ensuring the correct file is removed.

////
//// tab | Preventing Glob Expansion

_Without quotes:_

```bash
pattern=*.txt
echo File: $pattern
```

**Potential Issue:** If there are multiple `.txt` files in the directory, the variable `$pattern` will expand to match all of them, and the `echo` command will list all matching files instead of just displaying the intended string `File *.txt`.

---

_With double quotes:_

```bash
files=*.txt
echo "File $files"
```

**Advantage:** Double quotes prevent glob expansion, ensuring the variable is treated as a literal string.

////
//// tab | Using Single Quotes to Preserve String Literals

_With single quotes:_

```bash
grep '$HOME/*.log logfile.txt
```

**Advantage:** Single quotes preserve the string `$HOME/*.log` exactly as written, preventing variable expansion and globbing.

////
//// tab | Command Substitution

_Without quotes:_

```bash
output=$(ls -l)
echo $output
```

**Potential Issue:** Without quotes, the output of `ls -l` will undergo word splitting, potentially causing issues with whitespace and special characters.

---

_With double quotes:_

```bash
output=$(ls -l)
echo "$output"
```

**Advantage:** Double quotes preserve the command output as a single word, preventing word splitting and preserving whitespace.

////
///

## Declaring and Naming Variables

Bash provides several methods for declaring variables, each with its own implications for scope, immutability, and readability. Understanding the best practices for naming and declaring global, local, constant, and exported variables is essential for maintaining script consistency and preventing unintended side effects.

/// admonition | Guidelines
    type: info
//// tab | Global and Local Variables

- **Naming Conventions**: Use lowercase letters, separating each word with an underscore (`_`).
    - **Reason**: Using lowercase letters helps distinguish between constant and exported/environment variables, which are conventionally in uppercase.
- **Scope Management**: Use the `local` keyword to limit the scope of a variable within a function.
    - **Reason**: Defining local variables within functions prevents conflicts with global variables.

///// details | Example
    type: example

_General variable declaration:_

```bash
my_var="local value"
```

**Note**: Outside of a function, `local` is not applicable.

---

_Without scope management:_

```bash
my_function() {
    my_var="local value"
    echo "$my_var"
}

my_function
```

**Possible Issue:** If `my_var` is already defined globally, this assignment could overwrite the global value.

---

_With scope management:_

```bash
my_function() {
    local my_var="local value"
    echo "$my_var"
}

my_function
```

**Advantage:** Using `local` confines the variable's scope to the function, preventing conflicts with global variables.

/////
////
//// tab | Constant Variables

- **Naming Conventions**: Use uppercase letters prefixed with `C_`, separating each word with an underscore (`_`).
    - **Reason 1**: Uppercase naming with the `C_` prefix signals the immutability of these variables, making them easier to distinguish as constants.
    - **Reason 2**: The `C_` prefix prevents accidental overwriting of environment variables or other constants.
    - **Exception**: If the intention is to modify or overwrite an existing environment variable, follow the standard naming convention.
- **Selective Use of `readonly`**: Apply `readonly` at or directly after assignment for constants where preventing modification is crucial.
    - **Reason**: Enforces immutability, ensuring that critical values remain unchanged throughout the script.
    - **When to use**:
        - **Global Constants**: For values that must remain unchanged throughout the script to ensure consistency and prevent accidental overwrites.
        - **Security-Sensitive Variables**: For values that, if altered, could lead to security vulnerabilities, safeguarding critical information.
            - **Example**: API keys, passwords, or encryption keys.
            - **Note**: Storing sensitive information in scripts is **_highly_** discouraged; use secure methods like environment variables or external configuration files.
        - **Constants Shared Across Functions**: For values accessed by multiple functions, ensuring stable references and reducing the risk of bugs.
- **Omitting `readonly`**: <mark>**_CONSIDER_**</mark> omitting `readonly` even for intended constants in specific situations where flexibility or simplicity is prioritized.
    - **Reason**: Overusing `readonly` can restrict script flexibility, so reserve it for truly immutable values.
    - **When to omit**:
        - **Development and Debugging Phases**: When you need to modify constants temporarily for testing, delaying `readonly` can aid in faster iteration.
        - **Readability and Simplicity**: In simple or short scripts, omitting `readonly` can keep the script easy to read and maintain, without unnecessary complexity.
- **Treatment**: <mark>**_CONSIDER_**</mark> treating these variables as immutable, even if `readonly` is not applied.
    - **Reason**: Consistently treating constant variables as immutable ensures script reliability and maintainability.

///// details | Example
    type: example

_Without `readonly`:_

```bash
PI=3.14159
```

---

_With `readonly`:_

```bash
readonly PI=3.14159
```

**Advantage:** Using `readonly` ensures the constant cannot be modified, maintaining its immutability.

/////
////
//// tab | Exported Variables

- **Naming Conventions**: Use uppercase letters prefixed with `E_`, separating each word with an underscore (`_`).
    - **Reason 1**: Uppercase naming with a specific prefix distinguishes these variables as an exported/environment variable, avoiding confusion with constant variables.
    - **Reason 2**: The `E_` prefix helps prevent accidental overwriting of existing environment variables or other constants.
    - **Exception**: If the intention is to modify or overwrite an existing environment variable, follow the standard naming convention.
- **Declaration**: Use the `export` declaration at the time of, or directly after, assignment.
    - **Reason**: Explicitly exporting variables ensures they are immediately available to child processes and scripts.

///// details | Examples
    type: example

_Without clear indication:_

```bash
export PATH="/usr/local/bin:$PATH"
```

---

_With clear indication:_

```bash
export E_PATH="/usr/local/bin:$PATH"
```

**Advantage:** Using the `E_` prefix distinguishes exported variables from other constants and variables, clarifying their purpose.

/////
////
//// tab | Selective Use of `declare`

- **Guideline**: Employ `declare` specifically for managing advanced variable attributes, such as associative arrays, and stick to direct assignment for standard variable declarations unless scoping or attributes dictate otherwise.
- **Reason**: While `declare` is useful for setting variables, it's often more than needed for simple assignments. Using direct assignment reduces complexity and enhances script clarity.

///// details | Example
    type: example

_Without `declare`:_

```bash
my_var="value"
```

---

_With `declare` for associative arrays:_

```bash
declare -A my_array
my_array[foo]="bar"
echo "${my_array[foo]}"
```

**Advantage:** Use `declare` for advanced variable properties, such as associative arrays, to enhance script functionality.

/////
////
///


## Shebang in Bash Scripts

On Unix-like systems, the shebang (`#!`) line at the beginning of a script specifies the interpreter that should execute the script. In Bash scripts, the choice of shebang can impact script portability and compatibility across different operating systems. Understanding the differences between `#!/bin/bash` and `#!/usr/bin/env bash` is crucial for ensuring scripts run as intended.

/// admonition | Guidelines
    type: info

- **Universal Compatibility**: Use `#!/usr/bin/env bash` for scripts that need to run on various operating systems, including BSD, macOS, and Linux.
    - **Reason**: This shebang searches the user’s `PATH` to locate the Bash executable, ensuring compatibility across different systems.
- **Linux-Specific Scripts**: Opt for `#!/bin/bash` when creating scripts solely for Linux environments.
    - **Reason**: Linux systems typically have Bash installed at `/bin/bash`, making this shebang suitable for Linux-specific scripts.
- **Other Considerations**: When writing scripts for specific environments, ensure the shebang reflects the intended interpreter location to avoid compatibility issues.

///

/// details | Examples
    type: example
//// tab | Universal Compatibility

_Using `#!/usr/bin/env bash`_

```bash
#!/usr/bin/env bash
echo "This script uses the Bash found in the user's PATH."
```

**Advantage:** Ensures compatibility across various operating systems and environments by using the Bash version specified in the user's PATH.

////
//// tab | Linux-Specific Scripts

_Using `#!/bin/bash`_

```bash
#!/bin/bash
echo "This script uses the Bash located at /bin/bash."
```

**Advantage:** Guarantees the script runs with the Bash located at `/bin/bash`, ensuring predictable execution in Linux environments.

////
///

### Why the Choice of Shebang Matters

- **Variability in Bash Locations**: On non-Linux systems like BSD and macOS, Bash might not be located in the same path, or the installed version might be older. Many macOS users, for example, upgrade Bash through [Homebrew](https://brew.sh/), which does not alter the system-installed version located at `/bin/bash`. On BSD, Bash is often located at `/usr/local/bin/bash` rather than `/bin/bash`.
- **Path Flexibility**: Using `#!/usr/bin/env bash` provides significant flexibility. It enables the script to utilize the Bash version installed in the user’s environment, which is crucial for accessing features available in newer Bash versions.

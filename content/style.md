# Style

As mentioned in the [Aesthetics](/content/aesthetics) section of this Guide, this section covers style related guidelines that are less subjective and have an effect on functionality. This includes things like how to declare variables, when to use quotes, and how to write shebang lines.

## Using Quotes

Quotes in Bash play a crucial role in how text is interpreted and expanded, affecting whitespace preservation, word splitting, and variable expansion.

/// admonition | Guidelines
    type: tip

- **Double Quotes**: Use double as the default method for quoting strings in Bash.
    - **Reason**: They prevent word splitting and globbing, while still allowing variables and commands to be expanded within the string.
- **Single Quotes**: Use single quote when you want to preserve string literals exactly as written.
    - **Reason**: Single quotes prevent all expansions, treating the enclosed text as a literal string.
- **Omitting Quotes**: In situations where quotes are not necessary, such as within `[[ ... ]]` tests, or when the affects of word splitting and globbing are desired, omitting quotes is acceptable.
- **Recommendation**: Ensure your IDE or text editor has syntax highlighting. This can help identify special characters in strings that perform expansions, and other potential issues.

///

/// details | Examples
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

## Declaring Variables

Variable declaration in Bash is essential for managing data, ensuring clarity, and preventing conflicts. Properly declaring variables with consistent naming conventions and scoping practices enhances script readability and maintainability.

/// admonition | Guidelines
    type: tip
//// tab | Global and Local Variables

- **Naming Conventions**: Use lowercase letters, separating each word with an underscore (`_`).
    - **Reason**: Using lowercase letters helps distinguish between constant and exported/environment variables, which are conventionally in uppercase.
- **Scope Management**: Use the `local` keyword to limit the scope of a variable within a function.
    - **Reason**: Defining local variables within functions prevents conflicts with global variables.

///// details | Example

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
- **Omitting `readonly`**: <mark>**_Consider_**</mark> omitting `readonly` even for intended constants in specific situations where flexibility or simplicity is prioritized.
    - **Reason**: Overusing `readonly` can restrict script flexibility, so reserve it for truly immutable values.
    - **When to omit**:
        - **Development and Debugging Phases**: When you need to modify constants temporarily for testing, delaying `readonly` can aid in faster iteration.
        - **Readability and Simplicity**: In simple or short scripts, omitting `readonly` can keep the script easy to read and maintain, without unnecessary complexity.
- **Treatment**: <mark>**_Consider_**</mark> treating these variables as immutable, even if `readonly` is not applied.
    - **Reason**: Consistently treating constant variables as immutable ensures script reliability and maintainability.

///// details | Example

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

- **Naming Conventions**: Use uppercase letters prefixed with `E_`, separating each word with an underscore (e.g., `E_PATH`, `E_CONFIG`).
    - **Reason 1**: Uppercase naming with a specific prefix distinguishes these variables as an exported/environment variable, avoiding confusion with constant variables.
    - **Reason 2**: The `E_` prefix helps prevent accidental overwriting of existing environment variables or other constants.
    - **Exception**: If the intention is to modify or overwrite an existing environment variable, follow the standard naming convention.
- **Declaration**: Use the `export` declaration at the time of, or directly after, assignment.
    - **Reason**: Explicitly exporting variables ensures they are immediately available to child processes and scripts.

///// details | Examples

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
///

/// admonition | Practices to Avoid
    type: warning
//// tab | Avoid `let` for Arithmetic Operations

- **Guideline**: Use `((...))` or `$((...))` when performing arithmetic operations.
- **Reason**: The `let` command, while functional, is less intuitive and can lead to errors if the expression is miswritten. Using arithmetic expansion (`$((...))`) or arithmetic evaluation (`((...))`) provides clearer, safer operations.

///// details | Example

_Using `let`:_

```bash
let result=1+2
echo "Result: $result"
```

---

_Using `$((...))`:_

```bash
result=$((1 + 2))
echo "Result: $result"
```

**Advantage:** `$((...))` provides clearer and safer arithmetic operations.

/////

////
//// tab | Selective Use of `declare`

- **Guideline**: Employ `declare` specifically for managing advanced variable properties, such as associative arrays, and stick to direct assignment for standard variable declarations unless scoping or attributes dictate otherwise.
- **Reason**: While `declare` is useful for setting advanced variable properties, it's often more than needed for simple assignments. Using direct assignment reduces complexity and enhances script clarity.

///// details | Example

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

## Shebang Lines in Bash Scripts

The shebang line, also known as the hashbang or interpreter directive, is a crucial component of Bash scripts. It specifies the interpreter that should execute the script, ensuring compatibility and consistent behavior across different environments.†

/// admonition | Guidelines
    type: tip

- **Universal Compatibility**: Use `#!/usr/bin/env bash` for scripts that need to run on various operating systems, including BSD, macOS, and Linux. This shebang is particularly useful because it searches the user’s `PATH` to find and use the first instance of Bash, which helps accommodate non-standard Bash installations.
- **Linux-Specific Scripts**: Opt for `#!/bin/bash` when creating scripts solely for Linux environments. In these cases, Bash is typically located at `/bin/bash`, and this shebang ensures direct and predictable script execution.

///

/// details | Examples
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

- **Variability in Bash Locations**: On non-Linux systems like BSD and macOS, Bash might not be located in the same path, or the installed version might be older. Many macOS users, for example, upgrade Bash through [Homebrew](https://brew.sh/), which does not alter the system-installed version located at `/bin/bash`. On BSD, Bash is often located at `/usr/local/bin/bash`, rather than `/bin/bash`.
- **Path Flexibility**: Using `#!/usr/bin/env bash` provides significant flexibility, enabling the script to utilize the Bash version installed in the user’s environment, which is crucial for accessing features available in newer Bash versions.

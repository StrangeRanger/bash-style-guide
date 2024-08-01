# Style

As mentioned in the [Aesthetics](/content/aesthetics) section of this Guide, this section covers style related guidelines that are less subjective and have an effect on functionality.

## Using Quotes

Quotes in bash play a role in how text is interpreted and expanded, affecting whitespace preservation, word splitting, and variable expansion. Understanding the correct usage of quotes is crucial for maintaining script integrity and preventing unexpected behavior.

/// admonition | Guidelines
    type: tip

- **Double Quotes**: Use double quotes in most cases to safely expand variables and command outputs, preventing word splitting and globing.
- **Single Quotes**: When a string should remain exactly as written, with no expansion or interpretation, use single quotes. This is crucial in utilities like `sed` or `awk` (e.g., `grep 'exact string' filename`).
- **Omit Quotes With Caution**: In specific scenarios, such as within `[[ ... ]]` for conditional expressions, quotes can be safely omitted since Bash does not perform word splitting.

///

/// details | Examples
//// tab | Variable Expansion with Spaces

_Without quotes:_

```bash
name="John Doe"
echo $name
```

**Potential Issue:** If `name` contains spaces, it will be split into multiple words.

---

_With double quotes:_

```bash
name="John Doe"
echo "$name"
```

**Advantage:** Preserves the original value, preventing word splitting.

////
//// tab | Preventing Glob Expansion

_Without quotes:_

```bash
files=*.txt
echo $files
```

**Potential Issue:** If there are multiple `.txt` files, they will be expanded and printed separately.

---

_With double quotes:_

```bash
files="*.txt"
echo "$files"
```

**Advantage:** Prevents globbing, treating the pattern as a literal string.

////
//// tab | Using Single Quotes to Preserve Literal Strings

_With single quotes:_

```bash
grep 'exact string' filename
```

**Advantage:** Ensures the string is used exactly as written, without any expansion.

////
//// tab | Command Substitution

_Without quotes:_

```bash
output=$(ls -l)
echo $output
```

**Potential Issue:** If the output contains spaces or newlines, it will be split into multiple words.

---

_With double quotes:_

```bash
output=$(ls -l)
echo "$output"
```

**Advantage:** Treats the entire output as a single word, preserving whitespace.

////
///

### Why Quotes Matter

- **Preventing Word Splitting**: Without quotes, Bash interprets spaces as word separators, leading to unintended behavior. For example, `echo $foo` would split the variable `foo` into separate words if it contains spaces.
- **Variable Expansion**: Double quotes ensure variables are expanded correctly, preserving their original form. Without quotes, variables are subject to word splitting and globbing, potentially altering their intended values.
- **Command Substitution**: Using double quotes around command substitutions ensures the output is treated as a single word, preventing issues with whitespace and special characters.

## Declaring Variables

Variable declaration in Bash is essential for managing data, ensuring clarity, and preventing conflicts. Properly declaring variables with consistent naming conventions and scoping practices enhances script readability and maintainability.

/// admonition | Guidelines
    type: tip

//// tab | Generic and Local Variables

- **Naming Conventions**: Use lowercase for local variable names, separating each word by an underscore (`_`).
- **Scope Management**: Use the `local` keyword to limit their scope within functions, preventing conflicts with global variables.
- **Reason**: Lowercase with underscores helps visually differentiate local variables from global environment variables, which are conventionally in uppercase. This distinction minimizes confusion, especially in complex scripts with both local and global variables.

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

- **Naming Conventions**: Use uppercase for constant variable names, separating each word by an underscore (`_`).
- **Treatment**: Always treat these variables as immutable, even if `readonly` is not applied.
- **Selective Use of `readonly`**: Apply `readonly` at the time of assignment for critical constants where preventing modification is crucial.
- **Reason**: Uppercase naming signals the immutability of these variables, making them easily recognizable as constants within their local contexts. While `readonly` can enforce immutability, using it selectively helps maintain flexibility during development and prevents accidental script rigidity.

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

- **Naming Conventions**: Use uppercase with the prefix `E_` before the first underscore for exported variables (e.g., `E_PATH`, `E_CONFIG`). This modification helps distinguish them from constant variables.
- **Declaration**: Use the `export` declaration at the time of assignment to clearly indicate environmental export.
- **Reason**: Using an uppercase naming with a specific prefix ensures these variables are distinguishable and understood as part of the global environment. This modification avoids confusion with constant variables while aligning with common Unix/Linux conventions.

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

- **Guideline**: Use `((...))` for clarity and consistency in arithmetic operations.
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
//// tab | Minimize the Use of `readonly`

- **Guideline**: Apply `readonly` only when crucial to prevent modifications.
- **Reason**: Overusing `readonly` can restrict script flexibility, as these variables cannot be reassigned. Limiting its use to truly immutable needs prevents accidental script rigidity.

///// details | Example

...

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

The shebang line is vital in a Bash script to determine which shell executes the script, ensuring script compatibility across different environments and systems.

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

- **Variability in Bash Locations**: On non-Linux systems like BSD and macOS, Bash might not be located in the same path, or the installed version might be older. Many macOS users, for example, upgrade Bash through Homebrew, which does not alter the system-installed version located at `/bin/bash`.
- **Path Flexibility**: Using `#!/usr/bin/env bash` provides significant flexibility, enabling the script to utilize the Bash version installed in the user’s environment, which is crucial for accessing features available in newer Bash versions.

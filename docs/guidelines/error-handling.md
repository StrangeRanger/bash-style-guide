# Proper Error Handling

Error handling in Bash is crucial for ensuring that scripts function as intended, providing predictable behavior, useful feedback, and effective management of unexpected events. Implementing proper error handling practices can prevent small issues from escalating into major problems.

This section covers essential practices for handling errors in Bash, including checking for command success, using `trap` for signal handling, redirecting errors to `stderr`, and understanding the implications of `set -e`.

## Checking Command Success

Commands like `cd`, `rm`, and `mv` can fail for various reasons, such as incorrect paths, insufficient permissions, or missing files. Failing to check the success of these commands can lead to unintended consequences, such as deleting the wrong files or modifying the wrong directories.

/// admonition | Guidelines
    type: info
//// tab | Conditional Checks

- **Usage**: <mark>**_ALWAYS_**</mark> follow commands that could cause issues if they fail, with a conditional check to handle errors appropriately.
    - **Reason**: By checking the success of critical commands, you can prevent further operations if something goes wrong, protecting your script from unintended behavior.

///// admonition | Examples
    type: example

```bash
cd /some/path || exit 1
rm file
```

**Explanation**: If the `cd` command fails, the script exits with a status of `1`, preventing any potentially harmful actions.

/////
////
//// tab | Error Messages for Clarity

- **Guideline**: When a command fails, provide a clear and descriptive error message to indicate what went wrong.
    - **Reason**: Clear error messages help users (or yourself) understand what failed and why, making it easier to troubleshoot issues.

///// admonition | Examples
    type: example

```bash
cd /some/path || {
    echo "Error: Failed to change directory to /some/path" >&2
    exit 1
}
rm file
```

**Explanation**: This example provides a clear error message and directs it to `stderr` (using `>&2`), ensuring that the failure is properly logged and visible.

/////
////
///

## Redirecting Errors to `stderr`

Standard error (`stderr`) is a file descriptor (file descriptor `2`) used for outputting error messages and diagnostics on Unix-like systems. By default, `stderr` displays these messages on the terminal alongside standard output (`stdout`). Redirecting errors to `stderr` is crucial for separating error messages from regular output, making it easier to identify and manage errors.

/// admonition | Guidelines
    type: info

- **Usage**: <mark>**_ALWAYS_**</mark> use `>&2` to redirect error messages to the standard error stream.
    - **Reason**: Command-line utilities and scripts expect errors to be sent to `stderr` for proper error handling and integration with other tools. This practice ensures that error messages are not mixed with regular output, which is especially important when output is being redirected or piped into other commands.

//// admonition | Examples
    type: example

```bash
cd /some/nonexistent/path || {
    echo "Error: Failed to change directory to /some/nonexistent/path" >&2
    exit 1
}
```

**Explanation**: This example attempts to change the directory to a non-existent path. When the command fails, an error message is sent to `stderr` using `>&2`, and the script exits with a status of `1`.

////
///

## Using `trap` for Signal Handling

Signal handling in Bash allows scripts to capture and respond to signals sent by the system or user actions, such as pressing ++ctrl+'C'++ to interrupt a script. The `trap` command lets you define specific actions that should be taken when a signal is received, such as cleaning up resources before the script exits.

/// admonition | Guidelines
    type: info
//// tab | Cleanup Operations

- **Usage**: Use `trap` to define cleanup operations or other necessary actions that should occur before a script exits.
    - **Reason**: Without `trap`, it can be difficult to ensure that temporary files or other resources are properly cleaned up, especially if an unexpected error causes the script to terminate prematurely. `trap` helps maintain a clean environment by ensuring that these operations are executed regardless of how the script exits.

///// admonition | Examples
    type: example

```bash
TMP_FILE=$(mktemp)

# Define a trap to remove the temporary file on exit.
trap 'rm -f "$TMP_FILE"' EXIT

echo "Temporary file created: $TMP_FILE"
echo "Random data" > "$TMP_FILE"

# Simulate normal script execution.
exit 0
```

**Explanation**: This example creates a temporary file and uses `trap` to ensure that the file is deleted when the script exits, whether it completes successfully or is interrupted.

/////
////
//// tab | Functions for Complex Trapping Logic

- **Guideline**: For complex signal handling, define a function that performs all necessary actions, and then invoke this function in your `trap` command.
    - **Reason**: Using a function helps organize and manage complex trapping logic, making your script easier to read and maintain, especially when dealing with multiple signals or detailed cleanup operations.

///// admonition | Examples
    type: example

```bash
TMP_FILE=$(mktemp)

# Define a cleanup function.
cleanup() {
    if (( $1 == 0 )); then
        echo "[INFO]  Exiting normally"
    else
        echo "[ERROR] An error occurred" >&2
    fi

    echo "[INFO]  Cleaning up..."
    rm -f "$TMP_FILE" \
        && echo "[INFO]  Temporary file removed" \
        || {
            echo "[ERROR] Failed to remove temporary file" >&2
            echo "[NOTE]  Please remove it manually: $TMP_FILE"
        }

    echo "[INFO]  Exiting with status code: $1"
    exit "$1"
}

# Trap EXIT signal and invoke the cleanup function.
trap 'cleanup $?' EXIT

echo "[INFO]  Performing some operations..."

# Simulate an error.
exit 1
```

**Explanation**: In this example, a cleanup function is defined to handle both successful and erroneous exits. The `trap` command ensures that cleanup is called when the script exits, providing a clear structure for managing resources and logging exit statuses.

/////
////
///

## Understanding `set -e`

The `set -e` option in Bash instructs the shell to immediately exit if any command returns a non-zero exit status. While this behavior can be useful for ensuring that scripts halt execution upon encountering an error, it can also lead to unexpected exits if not used carefully.

/// admonition | Guidelines
    type: info

- **Use Carefully**: Use `set -e` judiciously, particularly in scripts where premature exits could cause problems or where errors are expected and handled.
    - **Reason**: `set -e` can cause unexpected script exits, potentially leaving tasks incomplete if a command fails unexpectedly or if the script isn’t structured to handle such failures gracefully.
- **Test Thoroughly**: Thoroughly test scripts with `set -e` under various scenarios to ensure they behave as expected and only exit prematurely when intended.
    - **Reason**: Testing is crucial to verify that the script handles all possible outcomes correctly, particularly when using `set -e`, which can make debugging more challenging if not thoroughly validated.

///

/// admonition | Risks of Using `set -e`
    type: warning

- **Unintended Exits**: `set -e` can cause a script to terminate prematurely if a command fails, even if the failure is expected or handled, leading to incomplete execution.
- **Limited Control**: `set -e` enforces a blanket error-handling approach, which can be too restrictive in situations where failures are expected or need specific handling.
- **Debugging Challenges**: The immediate exit behavior of `set -e` can obscure the exact point of failure, making it harder to identify and fix issues.
- **Compatibility Issues**: `set -e` may behave inconsistently across different shell environments or under certain conditions (e.g., subshells, pipelines), potentially causing inconsistencies in script execution.

///


/// details | Examples
    type: example

_Example 1: Script Exits Due to a Command Failure_

```bash
#!/bin/bash
set -e

echo "Starting the script..."
mkdir -p /some/directory

# This command will cause the script to exit immediately if the file doesn't exist
rm non_existent_file.txt

echo "This line won't be executed."
```

**Explanation**: In this example, the script will exit immediately when `rm non_existent_file.txt` fails because the file doesn’t exist. As a result, the subsequent echo statement is never executed. This behavior could be problematic if the non-existence of the file was expected or acceptable.

---

_Example 2: Handling Specific Failures While Using `set -e`_

```bash
#!/bin/bash
set -e

echo "Starting the script..."
mkdir -p /some/directory

# Use a conditional check to handle the failure gracefully
rm non_existent_file.txt || echo "File not found, continuing without deletion."

echo "This line will be executed if the file was not found."
```

**Explanation**: Here, the script uses a conditional check with `||` to handle the failure of `rm` gracefully. The script does not exit when `rm` fails, allowing the script to continue executing.

///

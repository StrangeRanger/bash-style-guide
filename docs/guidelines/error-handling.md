# Proper Error Handling

Error handling in Bash is crucial for ensuring that scripts function as intended, produce predictable behavior, provide useful feedback, and efficiently manage unexpected events. Implementing proper error handling practices can prevent small issues from escalating into major problems.

This section covers essential practices for handling errors in Bash, including checking for command success, redirecting errors to `stderr`, and using `trap` for signal handling.

## Checking Command Success

Commands like `cd`, `rm`, and `mv` can fail for various reasons, such as incorrect paths, insufficient permissions, or missing files. Failing to check the success of these commands can lead to unintended consequences, such as deleting the wrong files or modifying the wrong directories.

/// admonition | Guidelines
    type: info
//// tab | Conditional Checks

- **Usage**: <mark>**_ALWAYS_**</mark> perform conditional checks on commands where failure could cause issues if not properly handled.
    - **Reason**: Checking the success of critical commands prevents further execution if an error occurs, reducing the risk of unintended behavior in your script.

///// admonition | Examples
    type: example

```bash
cd /some/path || exit 1
rm file
```

**Explanation**: When `cd` fails to change to the specified directory, the script exits with a status of `1`, preventing the subsequent `rm` command from executing. This ensures that the `rm` command is only run if the `cd` command succeeds.

/////
////
//// tab | Error Messages for Clarity

- **Guideline**: When a command fails, provide a clear and descriptive error message to indicate what went wrong.
    - **Reason**: Descriptive error messages help users (or yourself) understand what failed and why, making it easier to troubleshoot issues.

///// admonition | Examples
    type: example

```bash
cd /some/path || {
    echo "ERROR: Failed to change directory to /some/path" >&2
    exit 1
}
rm file
```

**Explanation**: In this example, when `cd` fails, an error message is displayed on `stderr` using `>&2`, and the script exits with a status of `1`. This provides a clear indication of the problem to the user and helps with debugging.

/////
////
///

## Redirecting Errors to `stderr`

Standard error (`stderr`) is a file descriptor (file descriptor `2`) used for outputting error messages and diagnostics on Unix-like systems. By default, `stderr` displays these messages on the terminal alongside standard output (`stdout`). Redirecting errors to `stderr` is crucial for separating error messages from regular output, making it easier to identify and manage errors.

/// admonition | Guidelines
    type: info

- **Usage**: <mark>**_ALWAYS_**</mark> use `>&2` to redirect error messages to the standard error stream.
    - **Reason**: Command-line utilities and scripts rely on errors being sent to `stderr` for proper error handling and integration with other tools.

///

/// admonition | Example
    type: example

```bash
cd /some/nonexistent/path || {
    echo "Error: Failed to change directory to /some/nonexistent/path" >&2
    exit 1
}
```

**Explanation**: In this example, `cd` attempts to change to a nonexistent directory. When the command fails, the error message is sent to stderr using >&2, ensuring that the message is kept separate from regular output.

///

## Using `trap` for Signal Handling

Signal handling in Bash enables scripts to capture and respond to signals sent by the system or user actions. The `trap` command allows you to define specific actions that should be executed when a signal is received. This is particularly useful for performing tasks such as cleanup operations before a script exits or for responding to specific signals like `SIGINT` (++ctrl+'C'++).

/// admonition | Guidelines
    type: info
//// tab | Cleanup Operations

- **Usage**: Use `trap` to define actions, like cleaning up temporary files, that should run before a script exits.
    - **Reason**: Without `trap`, ensuring proper cleanup can be challenging, especially if a script terminates unexpectedly. `trap` ensures these actions are executed regardless of how the script ends, helping to maintain a clean environment.

///// admonition | Example
    type: example

```bash
TMP_FILE=$(mktemp)

# Define a trap statement to remove the temporary file on exit.
trap 'rm -f "$TMP_FILE"' EXIT

echo "Temporary file created: $TMP_FILE"
echo "Random data" > "$TMP_FILE"

# Simulate normal script execution.
exit 0
```

**Explanation**: This example creates a temporary file and uses `trap` to ensure that the file is deleted when the script exits.

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

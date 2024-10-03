# Proper Error Handling

Error handling is essential for ensuring scripts operate as intended, providing meaningful feedback, and efficiently managing unexpected events. It helps prevent minor issues from escalating into major problems.

This section outlines essential practices for handling errors in Bash, such as verifying command success, managing standard error streams (`stderr`) (1), and using `trap` for signal handling.
{ .annotate }

1. **Streams**: In Unix-like systems, streams are standardized channels for input and output operations between programs and the operating system. `stdin` (standard input) is the stream from which a program reads input data, typically from the keyboard. `stdout` (standard output) is the stream to which a program writes its output, usually directed to the terminal. `stderr` (standard error) is the stream specifically used for outputting error messages and can be redirected independently of `stdout`.<br>_(Definition provided by ChatGPT, vetted by human)_

## Checking Command Success

Commands like `cd`, `rm`, and `mv` can fail for various reasons, such as incorrect paths, insufficient permissions, or missing files. If the success of these commands is not verified, it may lead to unintended consequences, such as deleting the wrong files or modifying the wrong directories.

/// admonition | Guidelines
    type: info
//// tab | Conditional Checks

- **Usage**: <mark>**_ALWAYS_**</mark> perform conditional checks on commands where failure could cause issues if left unhandled.
    - **Reason**: Verifying the success of critical commands ensures that failures are handled appropriately, preventing unintended side effects and maintaining control over script execution.

///// admonition | Example
    type: example

```bash
cd /some/path || exit 1
rm file
```

**Explanation**: In this example, the script attempts to change directories. If the `cd` command fails, the script immediately exits with a status code of `1`, preventing any subsequent commands (like `rm`) from running.

/////
////
//// tab | Error Messages for Clarity

- **Guidelines**: When a command fails, provide a clear, descriptive error message indicating what went wrong.
    - **Reason**: Descriptive error messages help users—including yourself—understand what failed and why, making it easier to troubleshoot issues.

///// admonition | Example
    type: example

```bash
cd /some/path || {
    echo "ERROR: Failed to change directory to '/some/path'" >&2
    exit 1
}
rm file
```

**Explanation**: In this example, if the `cd` command fails, the script outputs a clear error message to `stderr` and then exits. The message specifies which directory change failed, indicating where the issue occurred.

/////
////
///

## Standard Error Stream

Standard error (`stderr`) is a [file descriptor](https://mywiki.wooledge.org/FileDescriptor) used for outputting error messages on Unix-like systems. By default, messages sent to `stderr` are displayed in the terminal separately from standard output (`stdout`). This separation is essential for effective error management, as it ensures that error messages are clearly distinguished and can be handled independently. This is particularly useful when scripts are part of pipelines or used by command-line tools that parse specific output streams.

/// admonition | Guidelines
    type: info
//// tab | Redirecting Errors

- **Guideline**: <mark>**_ALWAYS_**</mark> use `>&2` to redirect error messages to the standard error stream (`stderr`).
    - **Reason**: As previously mentioned, command-line utilities and scripts expect errors to be sent to `stderr` and regular output to `stdout`. Directing errors to `stderr` ensures that error messages are displayed correctly and can be captured separately for further processing.

///// admonition | Example
    type: example

```bash
cd /some/nonexistent/path || {
    echo "Error: Failed to change directory to '/some/nonexistent/path'" >&2
    exit 1
}
```

**Explanation**: In this example, `cd` attempts to change its working directory to a nonexistent path. When the command fails, the error message is sent to `stderr` using `>&2`, ensuring that the error is communicated clearly and separately from regular output. The script then exits with a status code of `1`, signaling that an error occurred.

/////
////
//// tab | Logging Errors

- **Guideline**: When dealing with scripts that may produce many errors, consider logging errors to a file for later review while still using `stderr` for critical error messages.
    - **Reason**: This practice helps maintain a clean output in the terminal while ensuring that all errors are captured and accessible for debugging or auditing purposes.

///// admonition | Example
    type: example

```bash
#!/bin/bash

# Directories to list.
directories=("/etc" "/nonexistent" "/var")

# File to log errors.
error_log="errors.log"

# Clear previous error log.
echo "" > "$error_log"

# Iterate over each directory.
for dir in "${directories[@]}"; do
    echo "Listing contents of $dir:"
    if ! ls "$dir" 2>> "$error_log"; then
        echo "Error: Failed to list contents of $dir" >&2
    fi
done

echo "Completed. Check $error_log for non-critical errors."
```

**Explanation**: This script attempts to list the contents of directories specified in the `directories` array. If an error occurs during the `ls` command, the error message is appended to the `error_log` file using `2>>`. Additionally, a critical error message is sent to `stderr` using `>&2`, ensuring it is immediately visible to the user in the terminal. This approach keeps the terminal output clean while capturing all errors for later review.

/////
////
//// tab | Meaningful Error Messages

- **Guideline**: When sending errors to `stderr`, include clear and descriptive messages that indicate what went wrong and where.
    - **Reason**: Clear error messages improve the usability of scripts and make troubleshooting easier, especially when logs are reviewed long after an issue occurred.

////
///

## Using `trap` for Signal Handling

Signal handling in Bash allows scripts to capture and respond to signals sent by the system or user actions. The `trap` command lets you define specific actions to execute when a signal is received. This is particularly useful for tasks like cleanup operations before a script exits or responding to signals like `SIGINT` (triggered by pressing ++ctrl+'C'++).

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

**Explanation**: This example creates a temporary file and uses `trap` to ensure that the file is deleted when the script exits, whether it exits normally or due to an error.

/////
////
//// tab | Functions for Complex Trapping Logic

- **Guideline**: For complex signal handling, define a function that performs all necessary actions, and then invoke this function in your `trap` command.
    - **Reason**: Using a function helps organize and manage complex trapping logic, making your script easier to read and maintain, especially when dealing with multiple signals or detailed cleanup operations.

///// admonition | Example
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

**Explanation**: In this example, a cleanup function is defined to handle both successful and erroneous exits. The `trap` command ensures that `cleanup` is called when the script exits, providing a clear structure for managing resources and logging exit statuses.

/////
////
///

# Proper Error Handling

Error handling helps scripts behave predictably, report useful feedback, and recover cleanly from unexpected events. It also keeps small problems from turning into larger failures.

This section outlines essential practices for handling errors in Bash, including checking whether commands succeed, writing errors to the standard error stream (`stderr`) (1), and using `trap` for signal handling.
{ .annotate }

1. **Streams**: In Unix-like systems, streams are standard channels for input and output between programs and the operating system. `stdin` (standard input) is where a program reads input, with the keyboard typically serving as the default input source. `stdout` (standard output) is where a program writes its normal output, usually to the terminal. `stderr` (standard error) is where a program writes error messages, which are typically directed to the terminal separately from `stdout`.

## Checking Command Success

Commands like `cd`, `rm`, and `mv` can fail for many reasons, including incorrect paths, insufficient permissions, or missing files. If those failures are not checked, a script may continue in the wrong state and produce unintended results, such as deleting the wrong files or modifying the wrong directories.

/// admonition | Guidelines
    type: info
//// tab | Conditional Checks

- **Usage**: <mark>**_ALWAYS_**</mark> check commands whose failure could affect the rest of the script.
    - **Reason**: Checking critical commands makes failures explicit, prevents unintended side effects, and keeps control over script execution.

///// admonition | Example
    type: example

```bash
cd /some/path || exit 1
rm file
```

/////
////
//// tab | Error Messages for Clarity

- **Guideline**: When a command fails, provide a clear error message that explains what went wrong.
    - **Reason**: Clear error messages help users, including yourself, understand what failed and why, making troubleshooting easier.

///// admonition | Example
    type: example

```bash
cd /some/path || {
    echo "ERROR: Failed to change directory to '/some/path'" >&2
    exit 1
}
rm file
```

/////
////
///

## Standard Error Stream

Standard error (`stderr`) is a [file descriptor](https://mywiki.wooledge.org/FileDescriptor) used for error messages on Unix-like systems. By default, messages sent to `stderr` are displayed in the terminal separately from standard output (`stdout`). This separation lets error messages be handled independently from normal output.

/// admonition | Guidelines
    type: info

- **Guideline**: <mark>**_ALWAYS_**</mark> use `>&2` to redirect error messages to `stderr`.
    - **Reason**: Command-line utilities rely on keeping `stdout` separate from `stderr`. Redirecting errors to `stderr` ensures that error messages are displayed correctly and can be captured separately for logging or further processing.

///

/// admonition | Example
    type: example

```bash
cd /some/nonexistent/path || {
    echo "Error: Failed to change directory to '/some/nonexistent/path'" >&2
    exit 1
}
```

///

## Using `trap` for Signal Handling

The `trap` command lets scripts capture and respond to signals sent by the system or triggered by the user. It lets you define actions to run when a signal is received, making it useful for cleanup before a script exits or for handling signals like `SIGINT` (triggered by pressing ++ctrl+'C'++).

/// admonition | Guidelines
    type: info
//// tab | Cleanup Operations

- **Usage**: Use `trap` to define actions, such as removing temporary files, that should run before a script exits.
    - **Reason**: Without `trap`, reliable cleanup can be difficult, especially if a script exits unexpectedly. `trap` runs cleanup actions regardless of how the script ends, helping keep the environment clean.

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

- **Guideline**: For complex signal handling, define a function that performs the necessary actions, then call that function from your `trap`.
    - **Reason**: Using a function keeps complex trapping logic organized and makes the script easier to read and maintain, especially when handling multiple signals or detailed cleanup operations.

///// admonition | Example
    type: example

```bash
TMP_FILE=$(mktemp)

# Define a cleanup function.
cleanup() {
    if (( $1 == 0 )); then
        echo "[INFO] Exiting normally"
    else
        echo "[ERROR] An error occurred" >&2
    fi

    echo "[INFO]  Cleaning up..."
    rm -f "$TMP_FILE" \
        && echo "[INFO] Temporary file removed" \
        || {
            echo "[ERROR] Failed to remove temporary file" >&2
            echo "[NOTE] Please remove it manually: $TMP_FILE"
        }

    echo "[INFO] Exiting with status code: $1"
    exit "$1"
}

# Trap EXIT signal and invoke the cleanup function.
trap 'cleanup $?' EXIT

echo "[INFO]  Performing some operations..."

# Simulate an error.
exit 1
```

**Explanation**: In this example, a cleanup function handles both successful and failed exits. The `trap` ensures that `cleanup` runs when the script exits, providing a clear structure for managing resources and logging exit statuses.

/////
////
///

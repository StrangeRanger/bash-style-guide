# Common Mistakes

This section covers common mistakes and pitfalls encountered when writing Bash scripts. Understanding these issues and learning how to avoid them can help improve script reliability and maintainability.

## Understanding When to Use For vs. While Loops

Choosing between `for` and `while` loops in Bash scripting depends on the nature of the data being processed. `For` loops are optimal for fixed-size data sets such as lists and arrays, while `while` loops excel in handling streams or large, indefinite data sets.

### Limitations of For Loops with Newline Separated Data

Using `for` loops to process newline-separated data, such as files or command outputs, can lead to several issues:

- **Memory Usage**: `For` loops load all elements into memory, which can become inefficient with large data sets.
- **Field Splitting Issues**: Incorrectly parsing spaces and special characters can cause errors, as `for` loops split data based on spaces by default.
- **Word Splitting and Globbing**: Unquoted variables in `for` loops can lead to unexpected word splitting and globbing, altering script behavior.

### Advantages of While Loops

- **Memory Efficiency**: `While` loops process data line by line, which is more efficient for memory usage.
- **Accurate Field Handling**: Using `while` loops with `read` allows precise control over field splitting and can handle complex data patterns more reliably.

### Guidelines for Using For and While Loops

- **Use For Loops for Known, Fixed-Size Data Sets**: Employ `for` loops when the number of iterations is known beforehand, such as iterating over an array or a range of numbers defined by brace expansion.
- **Opt for While Loops for Streaming or Large Data Sets**: Choose `while` loops when dealing with potentially large or undefined data streams, particularly when reading lines from a file or the output of a command. This method prevents the entire data set from being loaded into memory at once.
- **Handle Field and Word Splitting Carefully**: When using `for` loops, ensure variables are properly quoted to prevent word splitting and globbing issues. In `while` loops, use `IFS` (Internal Field Separator) adjustments and `read` to manage how input lines are divided into fields.

### Examples

/// details | Examples
//// tab | Using For Loops for Fixed-Size Data Sets

_Iterating Over an Array_

```bash
array=(one two three)
for item in "${array[@]}"; do
    echo "$item"
done
```

**Advantage:** Efficiently iterates over a known, fixed-size array.

////
//// tab | Using For Loops with Brace Expansion

_Iterating Over a Range_

```bash
for i in {1..5}; do
    echo "Number: $i"
done
```

**Advantage:** Ideal for iterating over a predefined range of numbers.

////
//// tab | Limitations of For Loops with Newline Separated Data

_Processing Lines from a File_

```bash
for line in $(cat myfile.txt); do
    echo "$line"
done
```

**Issue:** Can lead to incorrect parsing and high memory usage for large files.

////
//// tab | Using While Loops for Streaming Data

_Reading Lines from a File_

```bash
while IFS= read -r line; do
    echo "$line"
done < myfile.txt
```

**Advantage:** Processes each line efficiently, reducing memory usage and handling field splitting accurately.

////
//// tab | Handling Command Output with While Loops

_Processing Command Output_

```bash
command_output=$(ls -1)
while IFS= read -r line; do
    echo "$line"
done <<< "$command_output"
```

**Advantage:** Reads and processes command output line by line, ensuring accurate field handling and memory efficiency.

////
//// tab | Preventing Word Splitting in For Loops

_Quoting Variables in For Loops_

```bash
list="one two three"
for item in "$list"; do
    echo "$item"
done
```

**Advantage:** Prevents word splitting and globbing issues by quoting the variable.

////
///

## `cat` Abuse

***...TO DO...***

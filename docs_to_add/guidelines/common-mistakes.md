# Common Mistakes

This section covers common mistakes and pitfalls in Bash scripts. Understanding these issues and learning how to avoid them can improve script reliability and maintainability.

## Understanding When to Use For vs. While Loops

Choosing between `for` and `while` loops in Bash depends on the data being processed. `for` loops are best for fixed-size data sets, such as lists and arrays, while `while` loops are better for streams or large, indefinite data sets.

### Limitations of For Loops with Newline-Separated Data

Using `for` loops to process newline-separated data, such as files or command outputs, can lead to several issues:

- **Memory Usage**: `for` loops load all elements into memory, which can become inefficient with large data sets.
- **Field Splitting Issues**: Incorrectly parsing spaces and special characters can cause errors because `for` loops split data on spaces by default.
- **Word Splitting and Globbing**: Unquoted variables in `for` loops can lead to unexpected word splitting and globbing, altering script behavior.

### Advantages of While Loops

- **Memory Efficiency**: `while` loops process data line by line, which is more memory efficient.
- **Accurate Field Handling**: Using `while` loops with `read` allows precise control over field splitting and can handle complex data patterns more reliably.

### Guidelines for Using For and While Loops

- **Use For Loops for Known, Fixed-Size Data Sets**: Use `for` loops when the number of iterations is known beforehand, such as when iterating over an array or a range of numbers defined by brace expansion.
- **Use While Loops for Streaming or Large Data Sets**: Choose `while` loops for potentially large or undefined data streams, especially when reading lines from a file or command output. This prevents the entire data set from being loaded into memory at once.
- **Handle Field and Word Splitting Carefully**: When using `for` loops, quote variables properly to prevent word splitting and globbing issues. In `while` loops, adjust `IFS` (Internal Field Separator) and use `read` to control how input lines are divided into fields.

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
//// tab | Limitations of For Loops with Newline-Separated Data

_Processing Lines from a File_

```bash
for line in $(cat myfile.txt); do
    echo "$line"
done
```

**Issue:** This can lead to incorrect parsing and high memory usage for large files.

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

**Advantage:** Reads and processes command output line by line, ensuring accurate field handling and efficient memory use.

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

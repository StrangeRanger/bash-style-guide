# TODO

## Small TODOs

- Instead of just saying, "It makes things more clear," explain how it makes things more clear.
    - Example:
        - **Explanation**: Bash globbing, using patterns like `*`, directly matches filenames in the specified directory without invoking an external command. Unlike `ls`, which produces plain text output that may misinterpret or mishandle filenames containing spaces, newlines, or special characters, Bash globbing handles these scenarios correctly and efficiently. When a glob pattern is used (e.g., `/path/to/dir/*`), the shell itself expands the pattern into a list of matching filenames. This approach ensures that each filename, regardless of its complexity or the characters it contains, is correctly identified and processed. Consequently, Bash globbing is more reliable and secure than parsing the output of ls, which can easily lead to errors or security vulnerabilities when encountering unconventional filenames.
- Ensure terminology is consistent and correct throughout the document. (e.g., "Block statements" vs. "Control statements")

## Big(ger) TODOs

- Add a section explaining the perils of using `eval` and why it should be avoided.
- Add a section explaining how to properly use `set -e`.
- Add information regarding how to deal with logical operators (or redirect and i/o operators), similar to that of inside "Formatting Multi-lined Commands", to _____...
- Add information to (some section) specifying that global variables should often be placed at the top of the script. This ensures that the script isn't reliant on variables defined within a function. (or something like that)
- While out of scope of this document, consider adding information to "Using Quotes" in style.md about the background of using quotes and how they are used in other programming languages. This could help readers understand the importance of quotes in Bash and why they should be used consistently.
- Add a section describing the importance of shebangs...

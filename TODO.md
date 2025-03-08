# Todo List

## Immediate Tasks

Pages requiring review and improvement (completed pages are highlighted as green):

- [x] Home Page
- [x] Aesthetics
- [ ] Bashism
- [ ] Common Mistakes
- [ ] Error Handling
- [ ] Style

Action items for these pages:

- Replace vague explanations (like "It makes things more clear") with specific, detailed reasoning.
  - **Example**:
    - **Explanation**: Bash globbing, using patterns like `*`, directly matches filenames in the specified directory without invoking an external command. Unlike `ls`, which produces plain text output that may misinterpret or mishandle filenames containing spaces, newlines, or special characters, Bash globbing handles these scenarios correctly and efficiently. When a glob pattern is used (e.g., `/path/to/dir/*`), the shell itself expands the pattern into a list of matching filenames. This approach ensures that each filename, regardless of its complexity or the characters it contains, is correctly identified and processed. Consequently, Bash globbing is more reliable and secure than parsing the output of ls, which can easily lead to errors or security vulnerabilities when encountering unconventional filenames.
- Standardize terminology throughout the document (e.g., consistently use either "Block statements" or "Control statements", not both).

## Substantial Enhancements

- [ ] Create a dedicated section detailing the dangers of `eval` usage and recommended alternatives.
- [ ] Develop comprehensive guidelines for the proper implementation of `set -e` with examples.
- [ ] Document best practices for working with logical operators and I/O redirection operators, following the format established in the "Formatting Multi-lined Commands" section.
- [ ] Add guidance on variable scope best practices, emphasizing that global variables should be declared at the script's beginning to prevent dependency issues with function-local variables.
- [ ] Consider expanding the "Using Quotes" section in style.md to include comparative context from other programming languages, highlighting why consistent quoting practices are particularly important in Bash.
- [ ] Draft a new section explaining shebang (`#!`) importance, proper syntax, and common implementations.

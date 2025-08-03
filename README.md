# Bash Style Guide

[![Project Tracker](https://img.shields.io/badge/repo%20status-Project%20Tracker-lightgrey)](https://hthompson.dev/project-tracker#project-255784006)

[![Click to access Bash Style Guide](https://img.shields.io/badge/Click%20to%20access%20Bash%20Style%20Guide-blue?style=for-the-badge)](https://bsg.hthompson.dev)

Over the past few years, I've written many Bash scripts—some good, some bad, and some downright ugly. This guide distills those experiences into a comprehensive set of best practices for writing safe, predictable, and maintainable Bash scripts.

Whether you're a beginner looking to avoid common pitfalls or an experienced developer seeking to improve your scripting practices, this guide provides practical advice on error handling, code style, performance considerations, and security practices.

The guide expands significantly upon [bahamas10's Bash Style Guide](https://github.com/bahamas10/bash-style-guide) and incorporates foundational principles from [Wooledge's Bash Practices](http://mywiki.wooledge.org/BashGuide/Practices). Any practices not explicitly covered here generally align with those resources.

You are encouraged to fork this repository for your own use and contribute to its improvements.

<details>
<summary><strong>Table of Contents</strong></summary>

- [Bash Style Guide](#bash-style-guide)
  - [Local Development](#local-development)
    - [Prerequisites](#prerequisites)
    - [Download and Setup](#download-and-setup)
  - [Acknowledgments](#acknowledgments)
  - [License](#license)

</details>

## Local Development

> [!NOTE]
> The Bash Style Guide is hosted online at [bsg.hthompson.dev](https://bsg.hthompson.dev). You only need to set up a local environment if you want to preview changes or contribute to the documentation.

### Prerequisites

To build and preview the documentation locally, you'll need:

- **Python** 3.9 or higher
- **[uv](https://github.com/astral-sh/uv#installation)** (a fast Python package manager and resolver)

### Download and Setup

1. **Clone the repository:**
    ```bash
    git clone https://github.com/StrangeRanger/bash-style-guide
    cd bash-style-guide
    ```

2. **Install dependencies:**
   ```bash
   uv sync
   ```

3. **Start the development server:**
   ```bash
   uv run mkdocs serve
   ```

4. **Open your browser:**
   Visit [http://localhost:8000](http://localhost:8000) to view the documentation with live reload.

## Acknowledgments

- [bahamas10/bash-style-guide](https://github.com/bahamas10/bash-style-guide)
- [Wooledge's Bash Practices](http://mywiki.wooledge.org/BashGuide/Practices)

## License

This project is licensed under the [MIT License](LICENSE).

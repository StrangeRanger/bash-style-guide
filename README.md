# Bash Style Guide

[![Project Tracker](https://img.shields.io/badge/repo%20status-Project%20Tracker-lightgrey)](https://hthompson.dev/project-tracker#project-255784006)

[![Click to access Bash Style Guide](https://img.shields.io/badge/Click%20to%20access%20Bash%20Style%20Guide-blue?style=for-the-badge)](https://bsg.hthompson.dev)

> [!NOTE]
> To access the full Bash Style Guide, click the blue badge above or navigate to [bsg.hthompson.dev](https://bsg.hthompson.dev).

Over the past few years, I've written many Bash scripts—some good, some bad, and some downright ugly. This guide distills those experiences into a set of best practices for writing safe, predictable, and maintainable Bash scripts.

The guide expands significantly upon [bahamas10’s Bash Style Guide](https://github.com/bahamas10/bash-style-guide) and incorporates foundational principles from [Wooledge's Bash Practices](http://mywiki.wooledge.org/BashGuide/Practices). Any practices not explicitly covered here generally align with those resources.

You are encouraged to fork this repository for your own use and contribute to its improvements.

<details>
<summary><strong>Table of Contents</strong></summary>

- [Bash Style Guide](#bash-style-guide)
  - [Requirements](#requirements)
  - [Quick Start](#quick-start)
  - [Project Structure](#project-structure)
  - [Build \& Development](#build--development)
  - [Acknowledgments](#acknowledgments)
  - [License](#license)

</details>

## Requirements

These tools are necessary **only** if you wish to build or preview the documentation locally.

- Python 3.10 or higher ([Download Python](https://www.python.org/downloads/))
- [uv](https://github.com/astral-sh/uv#installation) (a fast Python package manager and resolver)


## Quick Start

To preview the Bash Style Guide locally:

1. Ensure you have Python 3.10+ installed.
2. Install [uv](https://github.com/astral-sh/uv#installation).
3. Install dependencies:
   ```bash
   uv sync
   ```
4. Start the development server:
   ```bash
   uv run mkdocs serve
   ```
5. Open [http://localhost:8000](http://localhost:8000) in your browser.

Or, visit the hosted version at [bsg.hthompson.dev](https://bsg.hthompson.dev).

## Project Structure

- `docs/` — Main documentation content (guidelines, changelog, etc.)
- `docs/stylesheets/` — Custom CSS for the site
- `overrides/` — MkDocs Material theme overrides
- `mkdocs.yml` — MkDocs configuration

## Build & Development

- **Install dependencies:**
  ```bash
  uv sync
  ```
- **Serve locally for live preview:**
  ```bash
  uv run mkdocs serve
  ```
- **Build the static site for deployment:**
  ```bash
  uv run mkdocs build
  ```
- **Deploy to GitHub Pages (if configured):**
  ```bash
  uv run mkdocs gh-deploy
  ```

## Acknowledgments

- [bahamas10/bash-style-guide](https://github.com/bahamas10/bash-style-guide)
- [Wooledge's Bash Practices](http://mywiki.wooledge.org/BashGuide/Practices)

## License

This project is licensed under the [MIT License](LICENSE).

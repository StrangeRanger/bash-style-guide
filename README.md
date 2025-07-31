# Bash Style Guide

[![Project Tracker](https://img.shields.io/badge/repo%20status-Project%20Tracker-lightgrey)](https://hthompson.dev/project-tracker#project-255784006)
[![GitHub License](https://img.shields.io/github/license/StrangeRanger/HThompson)](LICENSE)
<br />
[![Static Badge](https://img.shields.io/badge/Click%20to%20access%20Bash%20Style%20Guide-blue)](https://bsg.hthompson.dev)

> [!NOTE]
> To access the full Bash Style Guide, click the blue badge above or navigate to [bsg.hthompson.dev](https://bsg.hthompson.dev).

Over the past few years, I've written many Bash scripts—some good, some bad, and some downright ugly. This guide distills the lessons I've learned into a set of best practices for writing safe, predictable, and maintainable Bash scripts.

This Bash Style Guide provides detailed recommendations for writing safe and predictable bash scripts. The guidelines in this document are a complete rewrite of the [Bash Style Guide](https://github.com/bahamas10/bash-style-guide) written by [bahamas10](https://github.com/bahamas10), drawing inspiration from the core concepts and ideas in [Wooledge's Bash Practices](http://mywiki.wooledge.org/BashGuide/Practices). While this guide expands upon and reinterprets those foundational principles, any practices not explicitly covered here should be assumed to align with the standards outlined in the aforementioned resources.

You are encouraged to fork this repository for your own use and contribute to its improvements.

## Quick Start

To preview the Bash Style Guide locally:

1. Install [uv](https://github.com/astral-sh/uv#installation) (a fast Python package manager and resolver).
2. Install dependencies:
   ```bash
   uv sync
   ```
3. Start the development server:
   ```bash
   uv run mkdocs serve
   ```
4. Open [http://localhost:8000](http://localhost:8000) in your browser.

Or, visit the hosted version at [bsg.hthompson.dev](https://bsg.hthompson.dev).

## Project Structure

- `docs/` — Main documentation content (guidelines, changelog, etc.)
- `stylesheets/` — Custom CSS for the site
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

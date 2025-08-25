# Bash Style Guide

Over the past few years, I've written many Bash scripts—some good, some bad, and some downright ugly. This guide distills those experiences into a comprehensive set of best practices for writing safe, predictable, and maintainable Bash scripts.

Whether you're a beginner looking to avoid common pitfalls or an experienced developer seeking to improve your scripting practices, this guide provides practical advice on error handling, code style, performance considerations, and security practices.

While rooted in my own experiences, this guide expands significantly upon [bahamas10's Bash Style Guide](https://github.com/bahamas10/bash-style-guide) and incorporates foundational principles from [Wooledge's Bash Practices](http://mywiki.wooledge.org/BashGuide/Practices). Any practices not explicitly covered here generally align with those resources.

You are encouraged to fork this [guide on GitHub](https://github.com/StrangeRanger/bash-style-guide) for your own use.

## Preface

This guide strives to be as objective as possible by providing well-founded reasons for each recommended practice. It covers both functional and aesthetic scripting choices. For stylistic preferences that may vary, please refer to the [Aesthetics](guidelines/aesthetics.md) section.

### Report Issues and Discuss Ideas

If you find any issues with this guide—such as typos, broken links, or incorrect examples—or if you have suggestions for improvement, please use the following:

- **[Issues](https://github.com/StrangeRanger/bash-style-guide/issues)**: For actionable problems with the guide or site (typos, broken links, contradictions, build/deploy problems, etc.).
- **[Discussions](https://github.com/StrangeRanger/bash-style-guide/discussions)**: For questions, clarifications, proposing new or revised guidelines, or sharing resources and examples.

## Extra Resources

- [Bashism - mywiki.wooledge.org](https://mywiki.wooledge.org/Bashism)
- [BashPitfalls - mywiki.wooledge.org](https://mywiki.wooledge.org/BashPitfalls)

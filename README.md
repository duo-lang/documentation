# Duo Documentation
[![Publish Docs on GH Page](https://github.com/duo-lang/documentation/actions/workflows/docs.yml/badge.svg)](https://github.com/duo-lang/documentation/actions/workflows/docs.yml)
[![CC BY 4.0][cc-by-shield]][cc-by]

The rendered documentation is available at [duo-lang.github.io/documentation](https://duo-lang.github.io/documentation/)

The docs are written in Markdown, and we use [mdbook](https://github.com/rust-lang/mdBook) to compile them to a html page.
The manual for mdbook can be found [here](https://rust-lang.github.io/mdBook/).

## Installing mdbook

In order to build the documentation locally you need the mdbook binary.
The simplest way to obtain mdbook is to get a rust toolchain using [rustup](https://rustup.rs/).
You can then use the `cargo` tool to install mdbook using [these](https://rust-lang.github.io/mdBook/guide/installation.html) instructions in the mdbook user guide.

## Building the documentation

The `mdbook` binary contains a builtin webserver which can serve the documentation.
In order to use this builtin webserver use:

```
mdbook serve --open
```

## License

This work is licensed under a
[Creative Commons Attribution 4.0 International License][cc-by].

[![CC BY 4.0][cc-by-image]][cc-by]

[cc-by]: http://creativecommons.org/licenses/by/4.0/
[cc-by-image]: https://i.creativecommons.org/l/by/4.0/88x31.png
[cc-by-shield]: https://img.shields.io/badge/License-CC%20BY%204.0-lightgrey.svg

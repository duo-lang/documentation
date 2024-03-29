# Editor Integration

Editor integration is currently available for VSCode and vim.
The editor integration uses the language server protocol, so it should be possible to support other editors in the future.

## Installing the VSCode extension

We provide a prebuilt `.vsix` extension which can be installed as an extension to VSCode.
The prebuilt extension is available as a release on our [GitHub page](https://github.com/duo-lang/vscode-plugin/releases/tag/latest) .
Once you have downloaded the extension, use the following command to install the extension as a plugin in VSCode.

```console
code --install-extension duo-lang-client-0.0.1.vsix
```
The extension itself provides syntax highlighting and indentation support.
The advanced features are implemented using a language server, and require the `duo` binary to be available on the path.

## Installing the vim extension

For installing the vim extension, please follow the instructions on the corresponding [GitHub page](https://github.com/duo-lang/vim-plugin).
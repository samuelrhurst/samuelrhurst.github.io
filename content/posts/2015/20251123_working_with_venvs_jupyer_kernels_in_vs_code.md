---
title: "Working with venvs and Jupyter Kernels in VS Code"
date: 2025-11-23
draft: true
tags: ['howto', 'vscode']
---

There is some basic built-in support for Jupyter notebooks in VS Code via the built in '.ipynb Support' extension.

What you really want is to install Microsoft's official [Jupyter VS Code Extensions.](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.jupyter). This extension will also include a few other extensions (Jupyter Keymap, Jupyter Notebook Renderers, Jupyter Cell Tags, Jupyter Slideshow)

```
ms-toolsai.jupyter
```

## Creating a venv with UV

## Opening a Jupyter Notebook using that venv

1. Open the .ipynb file in VS Code.
2. On the top right hand side of the notebook, click 'Select Kernel' (or choose Notebook: Select Notebook Kernel from the Command Palette)
3. Choose 'Python Environments...'
4. 
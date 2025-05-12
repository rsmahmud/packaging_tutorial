# Packaging Python Projects

This tutorial walks you through how to package a simple Python project. It will show you how to add the necessary files and structure to create the package, how to build the package, and how to upload it to the Python Package Index (PyPI).

> **Tip**  
> If you have trouble running the commands in this tutorial, please copy the command and its output, then [open an issue](https://github.com/pypa/packaging-problems/issues/new?template=packaging_tutorial.yml&title=Trouble+with+the+packaging+tutorial&guide=https://packaging.python.org/tutorials/packaging-projects) on the [packaging-problems](https://github.com/pypa/packaging-problems) repository on GitHub. We'll do our best to help you!

Some of the commands require a newer version of `pip`, so start by making sure you have the latest version installed:

**Unix/macOS:**
```bash
python3 -m pip install --upgrade pip
```

**Windows:**
```bat
py -m pip install --upgrade pip
```

## A simple project

This tutorial uses a simple project named `example_package_YOUR_USERNAME_HERE`. If your username is `me`, then the package would be `example_package_me`; this ensures that you have a unique package name that doesn't conflict with packages uploaded by other people following this tutorial. We recommend following this tutorial as-is using this project, before packaging your own project.

Create the following file structure locally:

```
packaging_tutorial/
└── src/
    └── example_package_YOUR_USERNAME_HERE/
        ├── __init__.py
        └── example.py
```

The directory containing the Python files should match the project name. This simplifies the configuration and is more obvious to users who install the package.

Creating the file `__init__.py` is recommended because the existence of an `__init__.py` file allows users to import the directory as a regular package, even if (as is the case in this tutorial) `__init__.py` is empty.  
[*See note on namespace packages below.*]

`example.py` is an example of a module within the package that could contain the logic (functions, classes, constants, etc.) of your package. Open that file and enter the following content:

```python
def add_one(number):
    return number + 1
```

If you are unfamiliar with Python’s modules and import packages, take a few minutes to read over the [Python documentation for packages and modules](https://docs.python.org/3/tutorial/modules.html#packages).

Once you create this structure, you'll want to run all of the commands in this tutorial within the `packaging_tutorial` directory.

## Creating the package files

You will now add files that are used to prepare the project for distribution. When you're done, the project structure will look like this:

```
packaging_tutorial/
├── LICENSE
├── pyproject.toml
├── README.md
├── src/
│   └── example_package_YOUR_USERNAME_HERE/
│       ├── __init__.py
│       └── example.py
└── tests/
```

## Creating a test directory

`tests/` is a placeholder for test files. Leave it empty for now.

## Choosing a build backend

Tools like `pip` and `build` do not actually convert your sources into a *distribution package* (like a wheel); that job is performed by a *build backend*. The build backend determines how your project will specify its configuration, including metadata (information about the project, for example, the name and tags that are displayed on PyPI) and input files.

You can choose from a number of backends; this tutorial uses **Hatchling** by default, but it will work identically with **setuptools**, **Flit**, **PDM**, and others that support the `[project]` table for metadata.

> **Note**  
> Some build backends are part of larger tools that provide a command-line interface with additional features like project initialization and version management, as well as building, uploading, and installing packages. This tutorial uses single-purpose tools that work independently.

The `pyproject.toml` tells *build frontend* tools like `pip` and `build` which backend to use for your project. Below are some examples for common build backends, but check your backend's documentation for more details.

The `requires` key is a list of packages that are needed to build your package. The *frontend* should install them automatically when building your package. This should always include your backend’s package, and might have other build-time dependencies.

The `build-backend` key is the name of the Python object that frontends will use to perform the build.

Additional configuration of the build tool will either be in a `[tool]` section of the `pyproject.toml`, or in a special file defined by the build tool. For example, when using `setuptools`, additional configuration may be added to a `setup.py` or `setup.cfg` file.

## Configuring metadata

Open `pyproject.toml` and enter the following content. Change the `name` to include your username.

```toml
[project]
name = "example_package_YOUR_USERNAME_HERE"
version = "0.0.1"
authors = [
  { name="Example Author", email="author@example.com" },
]
description = "A small example package"
readme = "README.md"
requires-python = ">=3.9"
classifiers = [
    "Programming Language :: Python :: 3",
    "Operating System :: OS Independent",
]
license = "MIT"
license-files = ["LICEN[CS]E*"]

[project.urls]
Homepage = "https://github.com/pypa/sampleproject"
Issues = "https://github.com/pypa/sampleproject/issues"
```

### Explanation of fields

- `name`: The distribution name of your package. Must be unique on PyPI.
- `version`: The package version.
- `authors`: A list of authors with name and email.
- `description`: Short summary of the package.
- `readme`: Path to a README file used as long description.
- `requires-python`: The range of Python versions supported.
- `classifiers`: Metadata used by PyPI and tools like pip.
- `license`: SPDX license identifier.
- `license-files`: Glob pattern to include license files.
- `urls`: Additional links shown on PyPI.

See the [pyproject.toml guide](https://packaging.python.org/en/latest/specifications/pyproject-toml/) for more details.

## Creating README.md

Open `README.md` and enter:

```markdown
# Example Package

This is a simple example package. You can use
[GitHub-flavored Markdown](https://guides.github.com/features/mastering-markdown/)
to write your content.
```

## Creating a LICENSE

It’s important for every package uploaded to PyPI to include a license. For help picking a license, see https://choosealicense.com/.

For example, the MIT license:

```
Copyright (c) 2018 The Python Packaging Authority

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction...
```

Most build backends include license files automatically. If you define `license-files` in `pyproject.toml` and your backend supports [PEP 639](https://peps.python.org/pep-0639/), the file

## Including other files

The files listed above will be included automatically in your *source distribution*. If you want to include additional files, see the documentation for your build backend.

## Generating distribution archives

The next step is to generate *distribution packages* for the package. These are archives that are uploaded to the Python Package Index and can be installed by `pip`.

Make sure you have the latest version of PyPA’s `build` installed:

**Unix/macOS:**
```bash
python3 -m pip install --upgrade build
```

**Windows:**
```bat
py -m pip install --upgrade build
```

> **Tip**  
> If you have trouble installing these, see the [installing packages tutorial](https://packaging.python.org/en/latest/tutorials/installing-packages/).

Now run this command from the same directory where `pyproject.toml` is located:

**Unix/macOS:**
```bash
python3 -m build
```

**Windows:**
```bat
py -m build
```

This command should output a lot of text and once completed should generate two files in the `dist` directory:

```
dist/
├── example_package_YOUR_USERNAME_HERE-0.0.1-py3-none-any.whl
└── example_package_YOUR_USERNAME_HERE-0.0.1.tar.gz
```

The `.tar.gz` file is a *source distribution* and the `.whl` file is a *built distribution*. Newer versions of `pip` prefer built distributions but will fall back to source if needed.

You should always upload a source distribution and provide built distributions for the platforms your project supports. Our example package is compatible with all platforms, so only one wheel is needed.

## Uploading the distribution archives

Finally, it’s time to upload your package to the Python Package Index!

First, register an account on [TestPyPI](https://test.pypi.org/account/register/). This is a test instance of PyPI used for experimentation.

> **Note**  
> You must verify your email address before uploading packages.

To securely upload your project, you’ll need a PyPI [API token](https://test.pypi.org/help/#apitoken). Create one at https://test.pypi.org/manage/account/#api-tokens with "Scope" set to "Entire account".  
**Copy and save the token—you won’t see it again.**

Install `twine`:

**Unix/macOS:**
```bash
python3 -m pip install --upgrade twine
```

**Windows:**
```bat
py -m pip install --upgrade twine
```

Use Twine to upload the archives in `dist`:

**Unix/macOS:**
```bash
python3 -m twine upload --repository testpypi dist/*
```

**Windows:**
```bat
py -m twine upload --repository testpypi dist/*
```

You’ll be prompted for an API token. Paste the token including the `pypi-` prefix. The input will be hidden.

Example output:

```
Uploading distributions to https://test.pypi.org/legacy/
Enter your API token:
Uploading example_package_YOUR_USERNAME_HERE-0.0.1-py3-none-any.whl
100% ━━━━━━━━━━━━━━━━━━━━━━ 8.2/8.2 kB • 00:01
Uploading example_package_YOUR_USERNAME_HERE-0.0.1.tar.gz
100% ━━━━━━━━━━━━━━━━━━━━━━ 6.8/6.8 kB • 00:00
```

Your package should now be viewable on TestPyPI, for example:  
`https://test.pypi.org/project/example_package_YOUR_USERNAME_HERE`

## Installing your newly uploaded package

You can use `pip` to install your package from TestPyPI and verify that it works. Create a virtual environment and install your package:

**Unix/macOS:**
```bash
python3 -m pip install --index-url https://test.pypi.org/simple/ --no-deps example-package-YOUR-USERNAME-HERE
```

**Windows:**
```bat
py -m pip install --index-url https://test.pypi.org/simple/ --no-deps example-package-YOUR-USERNAME-HERE
```

Make sure to replace `YOUR-USERNAME-HERE` with your actual username.

You should see output like:

```
Collecting example-package-YOUR-USERNAME-HERE
  Downloading https://test-files.pythonhosted.org/packages/.../example_package_YOUR_USERNAME_HERE_0.0.1-py3-none-any.whl
Installing collected packages: example_package_YOUR_USERNAME_HERE
Successfully installed example_package_YOUR_USERNAME_HERE-0.0.1
```

> **Note**  
> The `--index-url` flag tells `pip` to use TestPyPI. The `--no-deps` flag avoids installing dependencies from TestPyPI (which may not be available).

To test your installed package, start Python and import it:

**Unix/macOS:**
```bash
python3
```

**Windows:**
```bat
py
```

In the Python shell:

```pycon
>>> from example_package_YOUR_USERNAME_HERE import example
>>> example.add_one(2)
3
```

## Next steps

**Congratulations, you've packaged and distributed a Python project!** ✨🍰✨

Keep in mind TestPyPI is for experimentation. Packages and accounts may be deleted. When you're ready to publish to the real PyPI:

- Choose a unique, memorable name for your package.
- Register an account at https://pypi.org.
- Upload your package with `twine upload dist/*`.
- Install your package from real PyPI using `python3 -m pip install [your-package]`.

### Want to learn more?

- Advanced configuration for build backends:  
  - [Hatchling config](https://hatch.pypa.io/latest/config/metadata/)
  - [setuptools](https://setuptools.pypa.io/en/latest/userguide/pyproject_config.html)
  - [Flit](https://flit.pypa.io/en/latest/pyproject_toml.html)
  - [PDM](https://pdm-project.org/latest/reference/pep621/)

- Explore more guides:  
  [Packaging guides](https://packaging.python.org/guides/),  
  [Discussions](https://packaging.python.org/discussions/)

- Consider using a project manager like `hatch`, `flit`, `pdm`, or `poetry`.

---

## Notes

> **Note**  
> You can technically create packages without an `__init__.py` file, called [namespace packages](https://packaging.python.org/guides/packaging-namespace-packages/). These are considered an advanced topic and not covered in this tutorial. For beginners, it’s recommended to use regular packages with `__init__.py`, even if it's empty.

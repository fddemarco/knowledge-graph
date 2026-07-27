---
base: "[[Reading List.base]]"
Rating: ⭐️⭐️⭐️⭐️
Category:
  - Python
Author: ""
Status: Completed
---
## Overview

**Source Distributions. **Conceptually, a source distribution is an archive of the source code in raw form. Concretely, an **sdist **is a **.tar.gz archive **containing the source code plus an additional special file called PKG-INFO, which holds the project metadata. The presence of this file helps packaging tools to be more efficient by not needing to compute the metadata themselves. The PKG-INFO file follows the format specified in Core metadata specifications and is not intended to be written by hand.

Sdists serve several purposes in the packaging ecosystem. When pip, the standard Python package installer, cannot find a wheel to install, it will fall back on downloading a source distribution, compiling a wheel from it, and installing the wheel. Furthermore, sdists are often used as the package source by downstream packagers (such as Linux distributions, Conda, Homebrew and MacPorts on macOS, …), who, for various reasons, may prefer them over, e.g., pulling from a Git repository.

A source distribution is recognized by its file name, which has the form package_name-version.tar.gz, e.g., pip-23.3.1.tar.gz.

**Compiled Distributions (Wheels)**. Conceptually, a wheel contains exactly the files that need to be copied when installing the package.

There is a big difference between sdists and wheels for packages with extension modules, written in compiled languages like C, C++ and Rust, which need to be compiled into platform-dependent machine code. With these packages, wheels do not contain source code (like C source files) but compiled, executable code (like .so files on Linux or DLLs on Windows). Furthermore, while there is only one sdist per version of a project, there may be many wheels. Again, this is most relevant in the context of extension modules. The compiled code of an extension module is tied to an operating system and processor architecture, and often also to the version of the Python interpreter (unless the Python stable ABI is used).

For pure-Python packages, the difference between sdists and wheels is less marked. There is normally one single wheel, for all platforms and Python versions. Python is an interpreted language, which does not need ahead-of-time compilation, so wheels contain .py files just like sdists.

If you are wondering about .pyc bytecode files: they are not included in wheels, since they are cheap to generate, and including them would unnecessarily force a huge number of packages to distribute one wheel per Python version instead of one single wheel. Instead, installers like pip generate them while installing the package.

With that being said, there are still important differences between sdists and wheels, even for pure Python projects. **Wheels are meant to contain exactly what is to be installed, and nothing more**. In particular, wheels should never include tests and documentation, while sdists commonly do. Also, the wheel format is more complex than sdist. For example, it includes a special file – called RECORD – that lists all files in the wheel along with a hash of their content, as a safety check of the download’s integrity.

At a glance, you might wonder if wheels are really needed for “plain and basic” pure Python projects. **Keep in mind that due to the flexibility of sdists, installers like pip cannot install from sdists directly** – they need to first build a wheel, by invoking the build backend that the sdist specifies (the build backend may do all sorts of transformations while building the wheel, such as compiling C extensions). For this reason, even for a pure Python project, you should always upload both an sdist and a wheel to PyPI or other package indices. This makes installation much faster for your users, since a wheel is directly installable. By only including files that must be installed, wheels also make for smaller downloads.

On the technical level, **a wheel is a ZIP archive (unlike sdists which are TAR archives)**. You can inspect its contents by unpacking it as a normal ZIP archive, e.g., using unzip on UNIX platforms like Linux and macOS, Expand-Archive in Powershell on Windows, or the command line interface of Python’s zipfile module. This can be very useful to check that the wheel includes all the files you need it to.

Inside a wheel, you will find the package’s files, plus an additional directory called package_name-version.dist-info. This directory contains various files, including a METADATA file which is the equivalent of PKG-INFO in sdists, as well as RECORD. This can be useful to ensure no files are missing from your wheels.

The file name of a wheel (ignoring some rarely used features) looks like this: package_name-version-python_tag-abi_tag-platform_tag.whl. This naming convention identifies which platforms and Python versions the wheel is compatible with. For example, the name pip-23.3.1-py3-none-any.whl means that:

- (py3) This wheel can be installed on any implementation of Python 3, whether CPython, the most widely used Python implementation, or an alternative implementation like PyPy;
- (none) It does not depend on the Python version;
- (any) It does not depend on the platform.

The pattern py3-none-any is common for pure Python projects. Packages with extension modules typically ship multiple wheels with more complex tags.

## Installation

[Spack](https://github.com/spack/spack) is a flexible package manager designed to support multiple versions, configurations, platforms, and compilers. It was built to support the needs of large supercomputing centers and scientific application teams, who must often build software many different ways. Spack is not limited to Python; it can install packages for `C`, `C++`, `Fortran`, `R`, and other languages. It is non-destructive; installing a new version of one package does not break existing installations, so many configurations can coexist on the same system.

Spack offers a simple but powerful syntax that allows users to specify versions and configuration options concisely. Package files are written in pure Python, and they are templated so that it is easy to swap compilers, dependency implementations (like MPI), versions, and build options with a single package file. Spack also generates *module* files so that packages can be loaded and unloaded from the user’s environment.

## Native namespace packages

Python 3.3 added implicit namespace packages from PEP 420. All that is required to create a native namespace package is that you just omit **init**.py from the namespace package directory. An example file structure (following src-layout):

```markdown
mynamespace-subpackage-a/
    pyproject.toml # AND/OR setup.py, setup.cfg
    src/
        mynamespace/ # namespace package
            # No __init__.py here.
            subpackage_a/
                # Regular import packages have an __init__.py.
                __init__.py
                module.py
```

**Namespace packages **allow you to split the sub-packages and modules within a single package across multiple, separate distribution packages (referred to as distributions in this document to avoid ambiguity.

Namespace packages can be useful for a large collection of loosely-related packages (such as a large corpus of client libraries for multiple products from a single company). However, namespace packages come with several caveats and are not appropriate in all cases. A simple alternative is to use a prefix on all of your distributions such as import mynamespace_subpackage_a (you could even use import mynamespace_subpackage_a as subpackage_a to keep the import object short).

These namespace packages are mainly used in OSS projects such as pytest, flake8, etc. plugin extensions.

## Creating and discovering plugins

Often when creating a Python application or library you’ll want the ability to provide customizations or extra features via plugins. Because Python packages can be separately distributed, your application or library may want to automatically discover all of the plugins available.

There are three major approaches to doing automatic plugin discovery:

- Using naming convention.
- Using namespace packages.
- Using package metadata.

### Using naming convention

If all of the plugins for your application follow the same naming convention, you can use pkgutil.iter_modules() to discover all of the top-level modules that match the naming convention. For example, Flask uses the naming convention flask_{plugin_name}. If you wanted to automatically discover all of the Flask plugins installed:

```python
import importlib
import pkgutil

discovered_plugins = {
name: importlib.import_module(name)
for finder, name, ispkg
in pkgutil.iter_modules()
if name.startswith('flask_')
}
```

If you had both the Flask-SQLAlchemy and Flask-Talisman plugins installed then discovered_plugins would be:

```json
{
'flask_sqlalchemy': <module: 'flask_sqlalchemy'>,
'flask_talisman': <module: 'flask_talisman'>,
}
```

Using naming convention for plugins also allows you to query the Python Package Index’s simple repository API for all packages that conform to your naming convention.

### Using namespace packages

Namespace packages can be used to provide a convention for where to place plugins and also provides a way to perform discovery. For example, if you make the sub-package myapp.plugins a namespace package then other distributions can provide modules and packages to that namespace. Once installed, you can use pkgutil.iter_modules() to discover all modules and packages installed under that namespace:

```python
import importlib
import pkgutil

import myapp.plugins

def iter_namespace(ns_pkg):
    # Specifying the second argument (prefix) to iter_modules makes the
    # returned name an absolute name instead of a relative one. This allows
    # import_module to work without having to do additional modification to
    # the name.
    return pkgutil.iter_modules(ns_pkg.__path__, ns_pkg.__name__ + ".")

discovered_plugins = {
    name: importlib.import_module(name)
    for finder, name, ispkg
    in iter_namespace(myapp.plugins)
}
```

## Package index mirrors and caches

Mirroring or caching of PyPI (and other package indexes) can be used to speed up local package installation, allow offline work, handle corporate firewalls or just plain Internet flakiness.

There are multiple classes of options in this area:

- local/hosted caching of package indexes.
- local/hosted mirroring of a package index. A mirror is a (whole or partial) copy of a package index, which can be used in place of the original index.
- private package index with fall-through to public package indexes (for example, to mitigate dependency confusion attacks), also known as a proxy.

### Caching with pip

pip provides a number of facilities for speeding up installation by using local cached copies of packages:

- Fast & local installs by downloading all the requirements for a project and then -pointing pip at those downloaded files instead of going to PyPI.
- A variation on the above which pre-builds the installation files for the requirements using python3 -m pip wheel:

```bash
python3 -m pip wheel --wheel-dir=/tmp/wheelhouse SomeProject
python3 -m pip install --no-index --find-links=/tmp/wheelhouse SomeProject
```

### Fast & local installs

In some cases, you may want to install from local packages only, with no traffic to PyPI.

First, download the archives that fulfill your requirements:

```bash
python -m pip download --destination-directory DIR -r requirements.txt
```

Note that pip download will look in your wheel cache first, before trying to download from PyPI. If you’ve never installed your requirements before, you won’t have a wheel cache for those items. In that case, if some of your requirements don’t come as wheels from PyPI, and you want wheels, then run this instead:

```bash
python -m pip wheel --wheel-dir DIR -r requirements.txt
```

Then, to install from local only, you’ll be using --find-links and --no-index like so:

```bash
python -m pip install --no-index --find-links=DIR -r requirements.txt
```

### Pip wheel

**Build wheel archives for your requirements and dependencies**. Wheel is a built-package format, and offers the advantage of not recompiling your software during every install. For more details, see the wheel docs.

pip wheel – does more than just build your own code

- It resolves dependencies (unless you pass --no-deps)
- It builds wheels for your package and its dependencies (if wheels don’t already exist)
- It can cache wheels for offline use
- Does not build a source distribution
- Uses PEP 517 if pyproject.toml exists

## Accessing version information at runtime

Version information for all distribution packages that are locally available in the current environment can be obtained at runtime using the standard library’s importlib.metadata.version() function:

```python
importlib.metadata.version("cryptography")
'41.0.7'
```

Many projects also choose to version their top level import packages by providing a package level **version** attribute:

```python
import cryptography
cryptography.version
'41.0.7'
```

This technique can be particularly valuable for CLI applications which want to ensure that version query invocations (such as pip -V) run as quickly as possible.

As import packages and modules are not required to publish runtime version information in this way (see the withdrawn proposal in PEP 396), the **version** attribute should either only be queried with interfaces that are known to provide it (such as a project querying its own version or the version of one of its direct dependencies), or else the querying code should be designed to handle the case where the attribute is missing.

## src layout vs flat layout

- **The src layout requires installation of the project to be able to run its code, and the flat layout does not**. This means that the src layout involves an additional step in the development workflow of a project (typically, an editable installation is used for development and a regular installation is used for testing).
- **The src layout helps prevent accidental usage of the in-development copy of the code**. This is relevant since the Python interpreter includes the current working directory as the first item on the import path. This means that if an import package exists in the current working directory with the same name as an installed import package, the variant from the current working directory will be used. This can lead to subtle misconfiguration of the project’s packaging tooling, which could result in files not being included in a distribution. The src layout helps avoid this by keeping import packages in a directory separate from the root directory of the project, ensuring that the installed copy is used.
- **The src layout helps enforce that an editable installation is only able to import files that were meant to be importable**. This is especially relevant when the editable installation is implemented using a path configuration file that adds the directory to the import path. The flat layout would add the other project files (eg: [README.md](http://readme.md/), tox.ini) and packaging/tooling configuration files (eg: [setup.py](http://setup.py/), [noxfile.py](http://noxfile.py/)) on the import path. This would make certain imports work in editable installations but not regular installations.
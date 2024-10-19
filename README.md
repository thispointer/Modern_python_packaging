# Modern Python Project Packaging

Today, a clear standard has been defined to package python projects by using pyproject.toml. Compliant application of PEPs, build systems implementing the PEPs and embracing the import system enables you to call your project from anywhere, ensure consitetn imports, and have just one file that will work with many build systems. 

The source of the document is the [realpython.com code coversation about pyproject.toml](https://realpython.com/courses/packaging-with-pyproject-toml/).

## Create Folder for hosting your Application
Important is to structure our code in a folder structure. It is recommended to group all the developed source code of the application in one root directory `<PROJECT_NAME>` which is composed out of zero, one or more sub directories. 

## Create Virtual Environment

When you’re working on a Python project that uses third-party packages, it’s usually a bad idea to install these packages into the system-wide environment. There are two main reasons why you want to avoid doing this: First, you’re polluting a global namespace. Testing and debugging your projects gets a lot easier when you run them in isolated and reproducible environments. If two projects depend on conflicting versions of the same package, a single environment isn’t even an option. Second, your distribution or operating system may have carefully curated the system-wide environment. Installing and uninstalling packages behind the back of its package manager introduces a real chance of breaking your system.

Virtual environments were invented to solve these problems. They’re isolated from the system-wide installation and from each other. Under the hood, a virtual environment is a lightweight Python environment that stores third-party packages and a reference to its parent environment. Packages in virtual environments are only visible to the interpreter in the environment.

You create a virtual environment with the command <br> `python -m venv <VENV_DIR>`.<br> The last argument `<VENV_DIR>` is the location where you want the environment to exist— its root directory. Normally it is located in the project directory. The virtual environment - the `<VENV_DIR> ` folder - looks much like a Python installation, except that some files are missing, most notably the entire standard library.

![Structure of a python virtual environment](:/f10e24a5f2ab4871b97ae7182dbc2771)

Summary of creating a virtual workenvironment - `<VENV_DIR>`  equal to .venv - in folder  `<PROJECT_NAME` :
```zsh
python3 -m venv .venv
source .venv/bin/activate
pip install --upgrade pip
```

You will have in my example following directory structure:
```
<PROJECT_NAME>
	↳<VENV_DIR>
``` 

You also can use virtualenv if you need more advanced features in managing or optimizing your virtual environment.

## Add Your First Python Code
For making it clear, we use an example copied from the above-mentioned code conversation from realpython.com:
- we create a directory `<source_dir>` which contain the source code
- one of the files should be dunder main, i.e. __main__.py

```
<PROJECT_NAME>
	↳	<VENV_DIR>
	↳	<source_dir>
		↳	python source code files
``` 

Hence in our example we have:
![project structure](:/901914d2e05e48aaa8399e767c136f97)
Hereby `<source dir>` is **snakesay** and  `<VENV_DIR>` is **.venv**.
Ignore __pycache__ and other system generated files and folders.

The business logic of our application snakesay is in snake.py:
```python
SNAKE = r"""  \
   \    __
    \  {oo}
       (__)\
         λ \\
           _\\__
          (_____)_
         (________)Oo°
"""


def bubble(message):
    bubble_length = len(message) + 2
    return f"""
 {"_" * bubble_length}
( {message} )    
 {"‾" * bubble_length}"""


def say(message):
    print(bubble(message))
    print(SNAKE)
```

The contents of dunder main are:
```python
import sys

from snakesay import snake


def main():
    snake.say(' '.join(sys.argv[1:]))


if __name__ == '__main__':
    main()
```

If we run the application with the following command `python3 -m snakesay Sleep tight, little man-cub`, we achieve the desired result; 
![output python -m snakesay Sleep tight, little man-cub](:/ee11969884cf4d29b666e8807722a37d)

Running it by the command 
`python3 snakesay Sleep tight, little man-cub`, thus without the module parameter will produce an error message. Python did not find module snakesay. 


## pyproject.toml
The content of the pyproject.toml file is described by PEP621. It contains the specification of which information has to be added in the toml file as well as the structure. The syntay format is toml. 

The obligated elements in pyproject.toml that must nbe present are:
```toml
[build-system]
requires = ["setuptools", "setuptools-scm"]
build-backend = "setuptools.build_meta"

[project]
name = "snakesay"
version = "1.0.0"
```

Do note that build-system can also be Flite or Poetry. Poetry does not follow 100% the PEP621 standard but it has commited to be compliant in short term. The differences are small.

Before launching the application, we install the module snakesay. When ae are in the folder of the project, where we find the pyproject.toml and source code folder snakesay, we run:
`python -m pip install --editable .`  or `python -m pip install -e .`. The editable parameter allow us to edit the source code files and avoid to re-run the pip install command. 

Now, `python snakesay` and `python -m snakesay` will work from anywhere. You also can call the application from the REPL.

## Command Script
By adding [project.scripts]
section in pyproject.toml, we can launch the the aplication like an ordinary script command.

In our example, the application is launched by scriptname snakey.

```python
[build-system]
requires = ["setuptools", "setuptools-scm"]
build-backend = "setuptools.build_meta"

[project]
name = "snakesay"
version = "1.0.0"

[project.scripts]
snakey = "snakesay.__main__:main"
```

In PEP 621 the script to be called is determined by `my-script = "my_package.module:function"`

Note: when you changepyproject.toml, you have to rerun `python -m pip install -e .` 

Now you can run the application anywhere by `snakey <message >`

Hereby the project structure and complete pyproject.toml file:
![Overall folder strcuture and pyproject.toml](:/94aece7a214a424b887c52173a7eea74)
The egg-info directory contains PKG_info meta datameta that is created by the buikd system. This meta data canbe interogated by the importlib meta data package in e.g. REPL. 

References: 
1. [Configuring setuptools by using pyproject.toml](https://setuptools.pypa.io/en/latest/userguide/pyproject_config.html) 
2. [Python Packaging User Guide: Writing your pyproject.toml](https://packaging.python.org/en/latest/guides/writing-pyproject-toml/#writing-pyproject-toml)
3. [realpython.com code coversation about pyproject.toml](https://realpython.com/courses/packaging-with-pyproject-toml/)
4. [Flit Build System](https://flit.pypa.io/en/stable/)
5. [Poetry](https://python-poetry.org/)
6.[PEP 621 – Storing project metadata in pyproject.toml](https://peps.python.org/pep-0621/)





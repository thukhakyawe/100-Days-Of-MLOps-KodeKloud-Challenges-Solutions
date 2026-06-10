# Lab Information

The xFusionCorp Industries deployment team needs the fraud-detection model code packaged as an installable Python distribution. A draft pyproject.toml exists at /root/code/fraud-detection/, but it does not build a wheel that meets the team's standard. Correct the file and produce a compliant package.

    The project at /root/code/fraud-detection/ already contains the source code under src/fraud_detection/. The Python files are complete—you do not need to modify any of them.

    The corrected pyproject.toml must satisfy every one of the following:
        it declares a [build-system] section with requires = ["setuptools>=61.0", "wheel"] and build-backend = "setuptools.build_meta";
        name is fraud_detection (the distribution name must match the module path under src/);
        version is 0.1.0;
        requires-python is >=3.10;
        dependencies is ["scikit-learn", "pandas", "numpy"].

    Review the existing pyproject.toml and correct everything that does not match the requirements above.

    Build the package from the project directory:

   cd /root/code/fraud-detection
   python3 -m build

    The build must produce a wheel named fraud_detection-0.1.0-*.whl under dist/.

The build package is already installed. Use python3 rather than python.


---

# Lab Solutions


🧭 Part 1: Lab Step-by-Step Guidelines


Step 1 — Move into the project directory

```
cd /root/code/fraud-detection
```

Step 2 — Inspect the existing pyproject.toml

```
cat pyproject.toml
```

Look for:

missing [build-system]
incorrect package name
wrong version
missing dependencies
incorrect Python version requirement

Step 3 — Fix pyproject.toml

Open the file:

```
vi pyproject.toml
```

Replace the contents with:

```
[build-system]
requires = ["setuptools>=61.0", "wheel"]
build-backend = "setuptools.build_meta"

[project]
name = "fraud_detection"
version = "0.1.0"
requires-python = ">=3.10"
dependencies = [
    "scikit-learn",
    "pandas",
    "numpy"
]

[tool.setuptools.packages.find]
where = ["src"]
```


Step 4 — Build the Package

Run exactly:

```
python3 -m build
```

Expected output:

```
root@controlplane ~/code/fraud-detection via 🐍 v3.12.3 ✖ python3 -m build
* Creating isolated environment: venv+pip...
* Installing packages in isolated environment:
  - setuptools>=61.0
  - wheel
* Getting build dependencies for sdist...
running egg_info
creating src/fraud_detection.egg-info
writing src/fraud_detection.egg-info/PKG-INFO
writing dependency_links to src/fraud_detection.egg-info/dependency_links.txt
writing requirements to src/fraud_detection.egg-info/requires.txt
writing top-level names to src/fraud_detection.egg-info/top_level.txt
writing manifest file 'src/fraud_detection.egg-info/SOURCES.txt'
reading manifest file 'src/fraud_detection.egg-info/SOURCES.txt'
writing manifest file 'src/fraud_detection.egg-info/SOURCES.txt'
* Building sdist...
running sdist
running egg_info
writing src/fraud_detection.egg-info/PKG-INFO
writing dependency_links to src/fraud_detection.egg-info/dependency_links.txt
writing requirements to src/fraud_detection.egg-info/requires.txt
writing top-level names to src/fraud_detection.egg-info/top_level.txt
reading manifest file 'src/fraud_detection.egg-info/SOURCES.txt'
writing manifest file 'src/fraud_detection.egg-info/SOURCES.txt'
warning: sdist: standard file not found: should have one of README, README.rst, README.txt, README.md

running check
creating fraud_detection-0.1.0
creating fraud_detection-0.1.0/src/fraud_detection
creating fraud_detection-0.1.0/src/fraud_detection.egg-info
copying files to fraud_detection-0.1.0...
copying pyproject.toml -> fraud_detection-0.1.0
copying src/fraud_detection/__init__.py -> fraud_detection-0.1.0/src/fraud_detection
copying src/fraud_detection/predict.py -> fraud_detection-0.1.0/src/fraud_detection
copying src/fraud_detection.egg-info/PKG-INFO -> fraud_detection-0.1.0/src/fraud_detection.egg-info
copying src/fraud_detection.egg-info/SOURCES.txt -> fraud_detection-0.1.0/src/fraud_detection.egg-info
copying src/fraud_detection.egg-info/dependency_links.txt -> fraud_detection-0.1.0/src/fraud_detection.egg-info
copying src/fraud_detection.egg-info/requires.txt -> fraud_detection-0.1.0/src/fraud_detection.egg-info
copying src/fraud_detection.egg-info/top_level.txt -> fraud_detection-0.1.0/src/fraud_detection.egg-info
copying src/fraud_detection.egg-info/SOURCES.txt -> fraud_detection-0.1.0/src/fraud_detection.egg-info
Writing fraud_detection-0.1.0/setup.cfg
Creating tar archive
removing 'fraud_detection-0.1.0' (and everything under it)
* Building wheel from sdist
* Creating isolated environment: venv+pip...
* Installing packages in isolated environment:
  - setuptools>=61.0
  - wheel
* Getting build dependencies for wheel...
running egg_info
writing src/fraud_detection.egg-info/PKG-INFO
writing dependency_links to src/fraud_detection.egg-info/dependency_links.txt
writing requirements to src/fraud_detection.egg-info/requires.txt
writing top-level names to src/fraud_detection.egg-info/top_level.txt
reading manifest file 'src/fraud_detection.egg-info/SOURCES.txt'
writing manifest file 'src/fraud_detection.egg-info/SOURCES.txt'
* Building wheel...
running bdist_wheel
running build
running build_py
creating build/lib/fraud_detection
copying src/fraud_detection/predict.py -> build/lib/fraud_detection
copying src/fraud_detection/__init__.py -> build/lib/fraud_detection
running egg_info
writing src/fraud_detection.egg-info/PKG-INFO
writing dependency_links to src/fraud_detection.egg-info/dependency_links.txt
writing requirements to src/fraud_detection.egg-info/requires.txt
writing top-level names to src/fraud_detection.egg-info/top_level.txt
reading manifest file 'src/fraud_detection.egg-info/SOURCES.txt'
writing manifest file 'src/fraud_detection.egg-info/SOURCES.txt'
installing to build/bdist.linux-x86_64/wheel
running install
running install_lib
creating build/bdist.linux-x86_64/wheel
creating build/bdist.linux-x86_64/wheel/fraud_detection
copying build/lib/fraud_detection/predict.py -> build/bdist.linux-x86_64/wheel/./fraud_detection
copying build/lib/fraud_detection/__init__.py -> build/bdist.linux-x86_64/wheel/./fraud_detection
running install_egg_info
Copying src/fraud_detection.egg-info to build/bdist.linux-x86_64/wheel/./fraud_detection-0.1.0-py3.12.egg-info
running install_scripts
creating build/bdist.linux-x86_64/wheel/fraud_detection-0.1.0.dist-info/WHEEL
creating '/root/code/fraud-detection/dist/.tmp-k44hfcde/fraud_detection-0.1.0-py3-none-any.whl' and adding 'build/bdist.linux-x86_64/wheel' to it
adding 'fraud_detection/__init__.py'
adding 'fraud_detection/predict.py'
adding 'fraud_detection-0.1.0.dist-info/METADATA'
adding 'fraud_detection-0.1.0.dist-info/WHEEL'
adding 'fraud_detection-0.1.0.dist-info/top_level.txt'
adding 'fraud_detection-0.1.0.dist-info/RECORD'
removing build/bdist.linux-x86_64/wheel
Successfully built fraud_detection-0.1.0.tar.gz and fraud_detection-0.1.0-py3-none-any.whl
```

Step 5 — Verify the Wheel File

Check the dist/ directory:

```
ls dist/
```

Expected wheel name pattern:

Example:

fraud_detection-0.1.0-py3-none-any.whl  fraud_detection-0.1.0.tar.gz

---

🧠 Part 2: Simple Beginner-Friendly Explanation

What is this lab about?

This lab focuses on packaging a Python project into an installable distribution.

The goal is to:

configure the project correctly
build a Python wheel package
make the ML code installable on other systems

What is pyproject.toml?
pyproject.toml is the modern Python project configuration file.

It defines:
project metadata
dependencies
build system configuration
packaging settings

It replaces older files like:
setup.py
setup.cfg

in many modern Python projects.
Understanding the [build-system] Section

The lab requires:

[build-system]
requires = ["setuptools>=61.0", "wheel"]
build-backend = "setuptools.build_meta"

This tells Python:
which tools are needed to build the package
which backend performs the build

Why setuptools and wheel Are Required
Package	Purpose
setuptools	Builds Python packages
wheel	Creates .whl files

Without these:
package building may fail

Why the Package Name Matters
The lab requires:
name = "fraud_detection"
This must match the module directory:
src/fraud_detection/
Using a different name can break:

imports
packaging
installation

What requires-python Means
Required setting:
requires-python = ">=3.10"
This means:
the package only supports Python 3.10 or newer.

Why Dependencies Are Declared
Required dependencies:

dependencies = [
    "scikit-learn",
    "pandas",
    "numpy"
]

When someone installs the package:
these libraries install automatically

What python3 -m build Does

Command:
python3 -m build
performs the packaging process.

It creates:

source distribution (.tar.gz)
wheel distribution (.whl)

inside:

dist/

What Is a Wheel File?
A wheel (.whl) is a standard Python package format.

Example:
fraud_detection-0.1.0-py3-none-any.whl

It allows fast installation using:
pip install package.whl

Why python3 Must Be Used
The lab explicitly requires:

python3 -m build

instead of:

python -m build

This guarantees the correct Python interpreter is used.

---
# Lab Information

The xFusionCorp Industries ML team enforces code quality with ruff and black on every pull request. The project at /root/code/fraud-detection/ currently fails both tools. Make it pass them.

    The project at /root/code/fraud-detection/ contains a pyproject.toml and sample sources under src/.

    The corrected project must meet the following requirements:
        ruff and black are both configured with a line length of 120.
        ruff lint rule selection includes E, F, W, and I, and is declared under [tool.ruff.lint] – The schema required by ruff 0.1 and later.
        Running ruff check src/ from the project directory exits with status 0.
        Running black --check src/ from the project directory exits with status 0.

    Review the existing configuration and source files, and correct everything that prevents the two commands above from exiting cleanly.

ruff, black, and mypy are already installed.


# Lab Solutions

🧭 Part 1: Lab Step-by-Step Guidelines

Step 1 — Move into the project directory

```
cd /root/code/fraud-detection
```

Step 2 — Inspect the current configuration

Check the existing pyproject.toml:

```
cat pyproject.toml
```

Look for:

incorrect Ruff schema
wrong line length
missing lint selections

Step 3 — Fix pyproject.toml

Open the file:

```
vi pyproject.toml
```

Replace or correct the contents to include:

```
[project]
name = "fraud-detection"
version = "0.1.0"

[tool.black]
line-length = 120

[tool.ruff]
line-length = 120

[tool.ruff.lint]
select = ["E", "F", "W", "I"]
```



Step 4 — Run Ruff Check

Run:

```
ruff check src/
```

If errors appear, fix them.

Example issues:

```
root@controlplane ~/code/fraud-detection via 🐍 v3.12.3 ➜  ruff check src/
I001 [*] Import block is un-sorted or un-formatted
 --> src/data/process_data.py:1:1
  |
1 | / import os
2 | | import pandas as pd
  | |___________________^
  |
help: Organize imports

F401 [*] `os` imported but unused
 --> src/data/process_data.py:1:8
  |
1 | import os
  |        ^^
2 | import pandas as pd
  |
help: Remove unused import: `os`

Found 2 errors.
[*] 2 fixable with the `--fix` option.
```


Step 5 — Auto-fix Ruff Issues (Recommended)

Run:

```
ruff check src/ --fix
ruff check src/
```

Example Output:

```
root@controlplane ~/code/fraud-detection via 🐍 v3.12.3 ➜  ruff check src/ --fix
Found 1 error (1 fixed, 0 remaining).

root@controlplane ~/code/fraud-detection via 🐍 v3.12.3 ➜  ruff check src/
All checks passed!
```


Step 6 — Format with Black

Run:

```
black src/
```

Expected Output:

```
black src/
All done! ✨ 🍰 ✨
5 files left unchanged.
```

Step 8 — Final Ruff Validation

Run again:

```
ruff check src/
```

Expected:

```
root@controlplane ~/code/fraud-detection via 🐍 v3.12.3 ➜  black --check src/
All done! ✨ 🍰 ✨
5 files would be left unchanged.
```

---

🧠 Part 2: Simple Beginner-Friendly Explanation

What is this lab about?

This lab focuses on Python code quality and formatting.

The project must pass:

Ruff → linting tool
Black → formatting tool

These tools help teams maintain:

clean code
consistent style
fewer bugs

What is Ruff?

Ruff is a fast Python linter.

It checks for problems like:

unused imports
syntax issues
bad formatting
import ordering problems

Example:

import os

If os is never used:

Ruff reports an error.

What is Black?

Black is an automatic Python formatter.

It rewrites code into a consistent style.

Example:

Before:

x=[1,2,3]

After Black:

x = [1, 2, 3]

Why Line Length Matters

The lab requires:

line-length = 120

This means:

code lines may be up to 120 characters long
both Ruff and Black must use the same limit

If tools use different limits:

formatting conflicts happen
Understanding Ruff Rule Selection

The lab requires:

[tool.ruff.lint]
select = ["E", "F", "W", "I"]

These rule groups mean:

Rule	Purpose
E	pycodestyle errors
F	Pyflakes checks
W	style warnings
I	import sorting

Why [tool.ruff.lint] Is Important

Older Ruff versions allowed:

[tool.ruff]
select = [...]

But Ruff 0.1+ requires:

[tool.ruff.lint]

Using the old schema causes configuration errors.

Why Run ruff check src/ --fix

This command:

automatically fixes many problems
sorts imports
removes simple issues

It saves time compared to manual editing.

Why Run black src/

This command reformats all Python files under:

src/

After formatting, this check should pass:

black --check src/

---
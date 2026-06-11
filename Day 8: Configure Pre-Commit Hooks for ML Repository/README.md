# Lab Information

The xFusionCorp Industries ML team enforces code quality on every commit via pre-commit. A draft .pre-commit-config.yaml exists in the git repository at /root/code/fraud-detection/, but it does not match the team's standard and pre-commit run --all-files fails against it. Correct the configuration.

    A git repository already exists at /root/code/fraud-detection/ with .pre-commit-config.yaml and process.py already tracked. pre-commit is installed system-wide.

    The corrected configuration must declare the following five hooks so that pre-commit run --all-files executes every one of them:
        trailing-whitespace, end-of-file-fixer, and check-yaml – All three sourced from the pre-commit/pre-commit-hooks repository, pinned to a current release;
        ruff – Sourced from the astral-sh/ruff-pre-commit repository, pinned to a current release;
        black – Sourced from the psf/black-pre-commit-mirror repository, pinned to a current release.

    Every repository entry in the configuration must include a rev: field.

    Review the existing .pre-commit-config.yaml and correct everything that prevents the hooks above from running.

    Once the configuration is correct, register the hooks with git and run them against the tracked files:

   pre-commit install
   pre-commit run --all-files

    Tip: pre-commit autoupdate queries each referenced repository and rewrites the rev: pins to the latest released tag. This is the standard way to discover current versions without looking them up by hand.



---

# Lab Solutions

🧭 Part 1: Lab Step-by-Step Guidelines


Step 1 — Move into the Repository

```
cd /root/code/fraud-detection
```

Step 2 — Inspect the Current Configuration

```
cat .pre-commit-config.yaml
```

Look for:

missing hooks
missing rev: fields
incorrect repository URLs
outdated configuration structure
Step 3 — Create a Valid Configuration

Edit the file:

```
vi .pre-commit-config.yaml
```

Use a configuration like this:


```
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v0.0.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml

  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.0.0
    hooks:
      - id: ruff

  - repo: https://github.com/psf/black-pre-commit-mirror
    rev: v0.0.0
    hooks:
      - id: black
```


Step 4 — Update to Current Releases

Run:

```
pre-commit autoupdate
```

Example Output:

```
root@controlplane fraud-detection on  main via 🐍 v3.12.3 ➜  pre-commit autoupdate
[https://github.com/pre-commit/pre-commit-hooks] updating v0.0.0 -> v6.0.0
[https://github.com/astral-sh/ruff-pre-commit] updating v0.0.0 -> v0.15.16
[https://github.com/psf/black-pre-commit-mirror] updating v0.0.0 -> 26.5.1
```


This automatically:

discovers current released tags
updates all rev: values
satisfies the lab requirement that repositories are pinned to current releases

Verify:

```
cat .pre-commit-config.yaml
```

You should now see real version tags instead of placeholders.

```
root@controlplane fraud-detection on  main [!] via 🐍 v3.12.3 ➜  cat .pre-commit-config.yaml
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v6.0.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml

  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.15.16
    hooks:
      - id: ruff

  - repo: https://github.com/psf/black-pre-commit-mirror
    rev: 26.5.1
    hooks:
      - id: black
```


Step 5 — Install the Hooks

```
pre-commit install
```

Expected output:

pre-commit installed at .git/hooks/pre-commit


Step 6 — Run All Hooks

```
pre-commit run --all-files
```

Expected output:

```
root@controlplane fraud-detection on  main [!] via 🐍 v3.12.3 ➜  pre-commit run --all-files
[INFO] Initializing environment for https://github.com/pre-commit/pre-commit-hooks.
[INFO] Initializing environment for https://github.com/astral-sh/ruff-pre-commit.
[INFO] Initializing environment for https://github.com/psf/black-pre-commit-mirror.
[INFO] Installing environment for https://github.com/pre-commit/pre-commit-hooks.
[INFO] Once installed this environment will be reused.
[INFO] This may take a few minutes...
[INFO] Installing environment for https://github.com/astral-sh/ruff-pre-commit.
[INFO] Once installed this environment will be reused.
[INFO] This may take a few minutes...
[INFO] Installing environment for https://github.com/psf/black-pre-commit-mirror.
[INFO] Once installed this environment will be reused.
[INFO] This may take a few minutes...
trim trailing whitespace.................................................Failed
- hook id: trailing-whitespace
- exit code: 1
- files were modified by this hook

Fixing process.py

fix end of files.........................................................Failed
- hook id: end-of-file-fixer
- exit code: 1
- files were modified by this hook

Fixing .pre-commit-config.yaml

check yaml...............................................................Passed
ruff (legacy alias)......................................................Passed
black....................................................................Passed
```



---

🧠 Part 2: Simple Beginner-Friendly Explanation

What is Pre-Commit?
Pre-commit is a tool that automatically checks code before it is committed to Git.

It helps catch:
formatting problems
linting issues
whitespace errors
configuration mistakes

before code reaches the repository.

What is .pre-commit-config.yaml?
This file tells pre-commit:

which checks to run
where the hooks come from
which versions to use

Each hook is downloaded from a Git repository.

Why rev: Is Required
Example:

- repo: https://github.com/pre-commit/pre-commit-hooks
  rev: v6.0.0

The rev: field pins the hook to a specific release.

Benefits:
reproducible behavior
same version for every developer
prevents unexpected changes

The lab requires every repository entry to have a rev: field.

Understanding the Required Hooks
trailing-whitespace
Removes spaces at the ends of lines.
Bad:
hello···

Good:
hello

end-of-file-fixer
Ensures files end with a single newline.
Many tools expect this format.

check-yaml
Validates YAML syntax.
Useful for files such as:
.pre-commit-config.yaml
docker-compose.yml

ruff
Runs Ruff linting checks on Python code.
Detects:
unused imports
syntax issues
import ordering problems

black
Formats Python code automatically.
Ensures all developers use the same code style.

What pre-commit autoupdate Does
Command:
pre-commit autoupdate
automatically updates:
rev:
to the latest released versions.
This is the recommended way to obtain current release tags.

Why You May Need to Run Hooks Twice
Some hooks automatically fix files.
Example:
Black reformats Python code
trailing-whitespace removes extra spaces

The first run modifies files.
The second run verifies everything is clean.

---
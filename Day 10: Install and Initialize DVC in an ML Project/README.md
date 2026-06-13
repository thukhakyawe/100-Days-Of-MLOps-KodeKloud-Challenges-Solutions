# Lab Information

The xFusionCorp Industries ML team is adopting DVC so that datasets and model files are versioned separately from code. Initialise DVC inside the existing Git repository at /root/code/fraud-detection/ and record the initialisation in Git.

    A Git repository already exists at /root/code/fraud-detection/ with an initial commit.

    Initialise DVC inside that repository so that the standard .dvc/ control directory and .dvcignore file are created alongside the existing Git working tree.

    Stage every file that DVC produces during initialisation, and record them in a new Git commit with the message Initialize DVC.

    Once initialisation is complete, the DVC extension will detect the new .dvc/ directory and surface the DVC TRACKED section in the EXPLORER panel together with a DVC indicator in the bottom status bar.



--- 

# Lab Solutions

✅ Part 1: Lab Step-by-Step Guidelines

Step 1: Move into the existing Git repository

```
cd /root/code/fraud-detection
```

Verify it's a Git repository:

```
git status
```

You should see something like:

On branch main
nothing to commit, working tree clean

Step 2: Verify DVC is installed

```
dvc version
```

Expected output:

```
root@controlplane fraud-detection on  master ➜  dvc version
DVC version: 3.67.1 (pip)
-------------------------
Platform: Python 3.12.3 on Linux-6.8.0-94-generic-x86_64-with-glibc2.39
Subprojects:
        dvc_data = 3.18.3
        dvc_objects = 5.2.0
        dvc_render = 1.0.2
        dvc_task = 0.40.2
        scmrepo = 3.6.2
Supports:
        http (aiohttp = 3.13.5, aiohttp-retry = 2.9.1),
        https (aiohttp = 3.13.5, aiohttp-retry = 2.9.1),
        s3 (s3fs = 2026.4.0)
Config:
        Global: /root/.config/dvc
        System: /etc/xdg/dvc
```

Step 3: Initialize DVC

Run:

```
dvc init
```

This creates:

.dvc/
.dvcignore

Typical output:

```
root@controlplane fraud-detection on  master ➜  dvc init
Initialized DVC repository.

You can now commit the changes to git.

+---------------------------------------------------------------------+
|                                                                     |
|        DVC has enabled anonymous aggregate usage analytics.         |
|     Read the analytics documentation (and how to opt-out) here:     |
|             <https://dvc.org/doc/user-guide/analytics>              |
|                                                                     |
+---------------------------------------------------------------------+

What's next?
------------
- Check out the documentation: <https://dvc.org/doc>
- Get help and share ideas: <https://dvc.org/chat>
- Star us on GitHub: <https://github.com/treeverse/dvc>
```

Step 4: Verify the new files

```
ls -la
```

You should now see:

.dvc/
.dvcignore

You can also inspect:

```
find .dvc
```

Step 5: Check Git status

```
git status
```

Expected output will show new DVC files:

```
root@controlplane fraud-detection on  master [+] ➜  git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   .dvc/.gitignore
        new file:   .dvc/config
        new file:   .dvcignore
```

Step 6: Stage all DVC-generated files

```
git add .dvc .dvcignore
```

Verify:

```
git status
```

Everything should be staged.

Step 7: Commit the DVC initialization

Use the exact commit message required by the lab:

```
git commit -m "Initialize DVC"
```

Expected output:

```
root@controlplane fraud-detection on  master [+] ➜  git commit -m "Initialize DVC"
[master e1de19a] Initialize DVC
 3 files changed, 6 insertions(+)
 create mode 100644 .dvc/.gitignore
 create mode 100644 .dvc/config
 create mode 100644 .dvcignore
```

Step 8: Verify the commit

```
git log --oneline -n 2
```

Expected:

```
root@controlplane fraud-detection on  master ➜  git log --oneline -n 2
e1de19a (HEAD -> master) Initialize DVC
446962d Initial commit
```

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

- What is DVC?

DVC (Data Version Control) is a tool used in Machine Learning projects to track:

Datasets
Models
Large files

without storing them directly in Git.

Think of it like:

Git  -> tracks code
DVC  -> tracks data and models

- Why are we running dvc init?

The repository already exists as a Git project.

When we run:

dvc init

DVC adds its own management files:

.dvc/
.dvcignore

These files tell DVC how to manage datasets and model artifacts.

- What does .dvc/ contain?

Example:

.dvc/
├── config
├── .gitignore
└── tmp/

These files store DVC configuration information.

- Why do we commit the DVC files?

The lab specifically says:

Record the initialization in Git.

This means the new DVC files must be saved in Git history.

So we:

git add .dvc .dvcignore
git commit -m "Initialize DVC"

This creates a permanent record that DVC was enabled in the project.

--- 
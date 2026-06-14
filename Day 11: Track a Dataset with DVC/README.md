# Lab Information

A teammate has added the transactions dataset to the xFusionCorp Industries fraud-detection repository, but it was committed directly to Git instead of being tracked with DVC. Bring the repository in line with the team standard—every dataset under data/ must be tracked by DVC, not by Git.

    A project exists at /root/code/fraud-detection/ with DVC already initialised. The dataset data/raw/transactions.csv is currently tracked by Git, and the team standard requires DVC to own it instead.

    Stop Git from tracking the dataset without deleting it from disk.

    Track the same dataset with DVC so a .dvc pointer file is produced and data/raw/.gitignore excludes the dataset itself.

    Stage the new .dvc pointer and the new .gitignore, then record a Git commit with the message Track transactions dataset with DVC.

    Once tracking is moved to DVC, the DVC TRACKED section in the EXPLORER panel will list the dataset, confirming the extension recognises it as a DVC-managed file.

---

# Lab Solutions

✅ Part 1: Lab Step-by-Step Guidelines

Step 1: Move into the repository

```
cd /root/code/fraud-detection
```

Verify the dataset is currently tracked by Git:

```
git ls-files | grep transactions.csv
```

Expected:

```
root@controlplane fraud-detection on  main ➜  git ls-files | grep transactions.csv
data/raw/transactions.csv
```

Step 2: Stop Git from tracking the dataset (keep the file)

The lab specifically says:

Stop Git from tracking the dataset without deleting it from disk.

Use:

```
git rm --cached data/raw/transactions.csv
```

Important: Use --cached.

Removes the file from Git tracking
Keeps the actual file on disk

Verify:

```
ls -l data/raw/transactions.csv
```

The file should still exist.

Step 3: Track the dataset with DVC

Run:

```
dvc add data/raw/transactions.csv
```

DVC will create:

data/raw/transactions.csv.dvc

and update:

data/raw/.gitignore

Expected output:

```
root@controlplane fraud-detection on  main [✘?] ➜  dvc add data/raw/transactions.csv
100% Adding...|███████████████████████████████████████|1/1 [00:00, 60.46file/s]
                                                                               
To track the changes with git, run:

        git add data/raw/.gitignore data/raw/transactions.csv.dvc

To enable auto staging, run:

        dvc config core.autostage true
```

Step 4: Verify DVC artifacts

Check:

```
ls -la data/raw
```

Expected:

```
root@controlplane fraud-detection on  main [✘?] ➜  ls -la data/raw
total 20
drwxr-xr-x 2 root root 4096 Jun 14 11:59 .
drwxr-xr-x 3 root root 4096 Jun 14 11:56 ..
-rw-r--r-- 1 root root   18 Jun 14 11:59 .gitignore
-rw-r--r-- 1 root root  379 Jun 14 11:59 transactions.csv
-rw-r--r-- 1 root root   95 Jun 14 11:59 transactions.csv.dvc
```

Inspect the new files:

```
cat data/raw/.gitignore
cat data/raw/transactions.csv.dvc
```

Step 5: Check Git status

```
git status
```

You should see something similar to:

```
root@controlplane fraud-detection on  main [✘?] ➜  git status
On branch main
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        deleted:    data/raw/transactions.csv

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        data/
```

Step 6: Stage the required files

Stage everything needed for the migration:

```
git add data/raw/transactions.csv.dvc
git add data/raw/.gitignore
git add -u
```

The git add -u stages the removal of transactions.csv from Git tracking.

Verify:

```
git status
```

Everything should be staged.

Step 7: Commit the changes

Use the exact commit message required by the lab:

```
git commit -m "Track transactions dataset with DVC"
```

Step 8: Verify the commit

```
git log --oneline -n 1
```

Expected:

```
root@controlplane fraud-detection on  main ➜  git log --oneline -n 1
1b8a2c7 (HEAD -> main) Track transactions dataset with DVC
```

Step 9: Final verification

Confirm Git no longer tracks the dataset:

```
git ls-files | grep transactions.csv
```

Expected:

data/raw/transactions.csv.dvc

Confirm DVC tracks it:

dvc status

Expected:

Data and pipelines are up to date. 

---


🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

- What is the problem?

Right now:

Git
 └── data/raw/transactions.csv

Git is tracking a dataset file.

The team standard says:

Git → track code and metadata
DVC → track datasets and models

So we need to move ownership of the dataset from Git to DVC.

Why use git rm --cached?

If you run:

git rm data/raw/transactions.csv

Git removes the file completely.

We don't want that.

Instead:

git rm --cached data/raw/transactions.csv

removes it only from Git tracking.

The file remains on disk:

data/raw/transactions.csv

- What does dvc add do?

When you run:

dvc add data/raw/transactions.csv

DVC creates a pointer file:

data/raw/transactions.csv.dvc

Think of it as:

transactions.csv.dvc
   ↓
points to
   ↓
transactions.csv

Git stores the small .dvc file instead of the large dataset.

- Why is .gitignore created?

DVC automatically adds:

data/raw/.gitignore

so Git ignores:

transactions.csv

This prevents someone from accidentally committing the dataset again.

- What gets committed to Git?

After migration, Git stores:

data/raw/transactions.csv.dvc
data/raw/.gitignore

Git no longer stores:

data/raw/transactions.csv

---
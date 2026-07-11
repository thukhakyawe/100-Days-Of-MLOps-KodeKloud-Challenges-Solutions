# Lab Information

The xFusionCorp Industries MLOps team versions datasets and models on separate Git branches so it can reproduce and roll between versions cleanly. Tag the current state as v1.0, create a v2-improved branch built on a newer dataset (which retrains the model), and confirm that switching back restores the original data and model.

A project exists at /root/code/fraud-detection/ with a working DVC pipeline (it processes the data and trains a model) and the baseline data/raw/transactions.csv already tracked.

An improved dataset has been pre-staged at /root/code/fraud-detection/data/raw/transactions_v2.csv and is visible in the file explorer. Do not delete this file.

Acceptance criteria:

    On the main branch, the current state is tagged v1.0.
    A branch named v2-improved holds the v2 state: the tracked dataset carries the contents of the v2 file (re-tracked with DVC), the pipeline has been re-run so models/model.pkl is retrained and versioned alongside the dataset, and the changes are committed.
    Back on the main branch, the v1 dataset and model are restored on disk, matching the hashes recorded by the v1.0 tag.

    The DVC extension's DVC TRACKED section in the EXPLORER panel reflects the tracked dataset and model for the branch you currently have checked out. To compare the exact hashes recorded on each branch, use git show <ref>:dvc.lock or dvc status.



---

# Lab Solutions

✅ Part 1: Lab Step-by-Step Guidelines

This is one of the most realistic DVC labs. You'll combine Git branches + DVC + pipeline reproducibility.


Step 1: Go to the project

```
cd /root/code/fraud-detection
```

Step 2: Make sure everything is committed

```
git status
```

Expected:

On branch main
nothing to commit, working tree clean

If not clean, commit your changes before continuing.

Step 3: Tag the current version

The lab requires the current main branch to be tagged as v1.0.

```
git tag v1.0
```

Verify:

```
git tag
```
Expected:

v1.0

Step 4: Create the new branch

```
git checkout -b v2-improved
```

Verify:

```
git branch
```

Expected:

* v2-improved
  main

Step 5: Replace the tracked dataset with the new version

The improved dataset already exists:

data/raw/transactions_v2.csv

Replace the original dataset:

```
cp data/raw/transactions_v2.csv data/raw/transactions.csv
```

Step 6: Update DVC tracking

Since the tracked file changed, update the DVC metadata:

```
dvc add data/raw/transactions.csv
```

This updates:

transactions.csv.dvc
DVC cache

Step 7: Retrain the pipeline

Run:

```
dvc repro
```
This should rerun:

process_data
split_data
train

and regenerate:

models/model.pkl
metrics.json
dvc.lock

Step 8: Check Git status

```
git status
```

You should see changes similar to:

modified: data/raw/transactions.csv.dvc
modified: dvc.lock
modified: metrics.json
modified: models/model.pkl.dvc   (if model is DVC tracked)

Note: The exact files may differ depending on how the lab repository is set up.

Step 9: Commit the v2 state

Stage everything:

```
git add .
```

Commit:

```
git commit -m "Update dataset to v2 and retrain model"
```

Step 10: Return to main

```
git checkout main
```

Step 11: Restore the DVC-tracked files

After switching branches, restore the correct dataset and model:

```
dvc checkout
```


Step 12: Verify

Check the dataset:

```
md5sum data/raw/transactions.csv
```

Check the model:

```
ls -l models/model.pkl
```

Verify DVC:

```
dvc status
```

Expected:

Data and pipelines are up to date.

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

- Why do we create a Git tag?

The command:

git tag v1.0

creates a permanent label for the current project state.

Think of it as taking a snapshot:

main
 │
 ├── v1.0  ← Snapshot

You can always return to exactly this version later.

- Why create a new branch?

Instead of changing the main branch directly, we create:

v2-improved

This allows us to safely experiment with a new dataset.

The structure becomes:

main
 │
 └── v2-improved

- Why run dvc add again?

The file name stays the same:

transactions.csv

but its contents change because you copied in the improved dataset.

DVC tracks file contents (using hashes), not just filenames.

Running:

dvc add data/raw/transactions.csv

updates the .dvc pointer to reference the new dataset version.

- Why rerun the pipeline?

The dataset changed.

That means:

transactions.csv
        │
        ▼
process_data
        ▼
split_data
        ▼
train
        ▼
model.pkl

Every downstream stage depends on the dataset, so DVC reruns the pipeline to produce a model trained on the new data.

- Why use dvc checkout?

When you switch back to:

git checkout main

Git restores:

dvc.yaml
.dvc files
dvc.lock

However, Git does not restore the actual dataset or model, because DVC manages those files.

Running:

dvc checkout

reads the .dvc files and restores the correct versions of:

transactions.csv
model.pkl

from the DVC cache.

---
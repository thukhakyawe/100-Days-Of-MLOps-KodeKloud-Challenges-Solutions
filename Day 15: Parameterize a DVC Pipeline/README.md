# Lab Information

The xFusionCorp Industries ML team manages model hyperparameters using params.yaml, enabling experiments to be conducted without altering the code. In the fraud-detection project, the train stage retrieves the n_estimators parameter from params.yaml, but this parameter is not declared to DVC, which means that changing its value does not initiate retraining. Integrate the parameter into the pipeline and illustrate the concept of parameter-driven reproducibility.

    A project exists at /root/code/fraud-detection/ with a three-stage DVC pipeline (process_data, split_data, train) and a params.yaml declaring n_estimators: 100. src/models/train.py already reads n_estimators from params.yaml. Do not modify the Python files.

    The train stage in dvc.yaml has no params: section, so DVC does not track n_estimators — changing it would not re-run the stage. Add a params: entry to the train stage that lists n_estimators (edit dvc.yaml, or use dvc stage add --force … -p n_estimators …).

    Run the full pipeline with dvc repro.

    Demonstrate parameter-driven retraining: change n_estimators to a different value (for example 200) and run dvc repro again. Only the train stage should re-execute, the new value must be recorded in dvc.lock, and models/model.pkl must be regenerated.

    dvc params diff reports changes to the tracked parameter values across Git commits, which is useful when comparing experiments.

---

# Lab Solutions

✅ Part 1: Lab Step-by-Step Guidelines

Step 1: Move into the repository

```
cd /root/code/fraud-detection
```

Step 2: Inspect the current pipeline

Open dvc.yaml:

```
cat dvc.yaml
```

Locate the train stage.

It will look similar to:

```
stages:
  process_data:
    cmd: python3 src/data/process_data.py
    deps:
      - data/raw/transactions.csv
      - src/data/process_data.py
    outs:
      - data/processed/clean_transactions.csv

  split_data:
    cmd: python3 src/data/split_data.py
    deps:
      - data/processed/clean_transactions.csv
      - src/data/split_data.py
    outs:
      - data/processed/train.csv
      - data/processed/test.csv

  train:
    cmd: python3 src/models/train.py
    deps:
      - data/processed/train.csv
      - src/models/train.py
    outs:
      - models/model.pkl
```

Step 3: Verify params.yaml

```
cat params.yaml
```
Expected:

n_estimators: 100

Step 4: Add the parameter to the train stage

Edit dvc.yaml:

vi dvc.yaml

Add the highlighted section:

```
train:
  cmd: python3 src/models/train.py

  deps:
    - data/processed/train.csv
    - src/models/train.py

  params:
    - n_estimators

  outs:
    - models/model.pkl
```

Save the file.


Step 5: Run the pipeline

Execute:

```
dvc repro
```

Expected:

```
root@controlplane fraud-detection on  main ➜  dvc repro
Running stage 'process_data':                                                  
> python3 src/data/process_data.py
Processed 15 rows
Generating lock file 'dvc.lock'                                                
Updating lock file 'dvc.lock'

Running stage 'split_data':                                                    
> python3 src/data/split_data.py
Train: 12 rows, Test: 3 rows
Updating lock file 'dvc.lock'                                                  

Running stage 'train':                                                         
> python3 src/models/train.py
Trained RandomForestClassifier with n_estimators=100
Updating lock file 'dvc.lock'                                                  

To track the changes with git, run:

        git add data/processed/.gitignore dvc.lock models/.gitignore

To enable auto staging, run:

        dvc config core.autostage true
Use `dvc push` to send your updates to remote storage.
```

Step 6: Verify dvc.lock

Search for the parameter:

grep -A5 params dvc.lock

You should see something like:

```
root@controlplane fraud-detection on  main [!?] ➜  grep -A5 params dvc.lock
    params:
      params.yaml:
        n_estimators: 100
    outs:
    - path: models/model.pkl
      hash: md5
      md5: ba2187225d8642cbbcd4b127310ced91
```

This confirms DVC is now tracking the parameter.

Step 7: Change the parameter

Edit:

```
vi params.yaml
```
Change:

n_estimators: 100

to

n_estimators: 200

Save the file.

Step 8: Run the pipeline again

```
dvc repro
```

Expected:

```
root@controlplane fraud-detection on  main [!?] ➜  dvc repro
Stage 'process_data' didn't change, skipping                                   
Stage 'split_data' didn't change, skipping                                     
Running stage 'train':                                                         
> python3 src/models/train.py
Trained RandomForestClassifier with n_estimators=200
Updating lock file 'dvc.lock'                                                  

To track the changes with git, run:

        git add dvc.lock

To enable auto staging, run:

        dvc config core.autostage true
Use `dvc push` to send your updates to remote storage.
```

Step 9: Verify dvc.lock

```
grep -A5 params dvc.lock
```
Expected:

```
    params:
      params.yaml:
        n_estimators: 200
    outs:
    - path: models/model.pkl
      hash: md5
      md5: c74b4c9de3ba10fc6f8c3789964ec187
```

Step 11: View parameter differences

Run:

```
dvc params diff
```

Expected:

```
root@controlplane fraud-detection on  main [!?] ➜  dvc params diff
Path         Param         HEAD    workspace
params.yaml  n_estimators  100     200
```

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

- What is params.yaml?

Instead of hardcoding values like:

n_estimators = 100

the project stores them in a separate file:

n_estimators: 100

The training script reads this value automatically.

This makes it easy to try different settings without changing the Python code.

- Why isn't changing params.yaml enough?

Even though train.py reads params.yaml, DVC doesn't automatically know that the train stage depends on it.

If the params: section is missing from dvc.yaml, DVC thinks:

"Nothing important changed."

So running:

dvc repro

may skip the training stage.

- What does the params: section do?

Adding:

params:
  - n_estimators

tells DVC:

"The train stage depends on the value of n_estimators."

Now, whenever n_estimators changes, DVC knows the model needs to be retrained.

- What happens after changing the parameter?

Initially:

n_estimators: 100

After editing:

n_estimators: 200

When you run:

dvc repro

DVC compares the tracked parameter values and notices that n_estimators has changed.

Since only the training configuration changed:

✅ train runs again.
✅ process_data is skipped.
✅ split_data is skipped.

This saves time by only rerunning the stage that is affected.

- What is dvc.lock?

After each successful run, DVC records the parameter value used.

Example:

params:
  params.yaml:
    n_estimators: 200

This allows anyone to reproduce the exact experiment later using the same parameter values.

- What does dvc params diff do?

The command:

dvc params diff

shows how parameter values changed between Git commits.

For example:

Path         Param          Old   New
params.yaml  n_estimators   100   200

This is useful for comparing different experiments and understanding what changed between model versions.

---
# Lab Information

The xFusionCorp Industries MLOps team needs every model training run to be reproducible, automatically tracked, and easy to compare so a chosen configuration can be promoted into version control. The fraud-detection pipeline is parameterized by max_depth, currently set shallow enough to underfit. Using DVC experiments, run three tracked experiments over different max_depth values, compare their recorded f1_score on the held-out test set, and promote the best-scoring run so its parameters, metrics, and model become the tracked workspace state.

A project exists at /root/code/fraud-detection/ with a parameterised DVC pipeline already in place. params.yaml declares n_estimators: 100 and max_depth: 4, and the baseline pipeline has been run once. src/models/train.py reads both parameters, trains the model, and evaluates it on the held-out test set, writing the real accuracy and f1_score to metrics.json. Do not modify the Python files.

Acceptance criteria:

    Three DVC experiments have been run, each with a different value for max_depth across a reasonable range (for example 2, 6, and 12); each experiment retrains the model and produces a fresh metrics.json.
    The experiment with the highest f1_score is applied to the workspace, so its max_depth, metrics.json, and models/model.pkl become the tracked state.

    The DVC extension's EXPERIMENTS view (open the DVC panel from the Activity Bar) lists every experiment alongside its parameters and metrics, which is a convenient way to compare runs at a glance.

---

# Lab Solutions

✅ Part 1: Lab Step-by-Step Guidelines

This lab introduces DVC Experiments (dvc exp), which let you test different parameter values without committing changes to Git.

Step 1: Move into the project

```
cd /root/code/fraud-detection
```

Step 2: Verify the current parameter

```
cat params.yaml
```

Expected:

n_estimators: 100
max_depth: 4

Step 3: Run the first experiment (max_depth=2)

```
dvc exp run -S max_depth=2
```

This will:

Update max_depth to 2 for the experiment.
Retrain the model.
Generate a new metrics.json.
Save the experiment in DVC (without changing your Git history).

Step 4: Run the second experiment (max_depth=6)

```
dvc exp run -S max_depth=6
```
Step 5: Run the third experiment (max_depth=12)

```
dvc exp run -S max_depth=12
```

Now you have three experiments, each using a different max_depth.

Step 6: Compare the experiments 


Run:

```
apt update
apt install -y less
dvc exp show
```

root@controlplane fraud-detection on  main [!] ➜  dvc exp show
 ───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────>
  Experiment                 Created    accuracy   f1_score   n_estimators   max_depth   data/processed/clean_transactions.csv   data/processed/>
 ───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────>
  workspace                  -              0.75     0.6795   100            12          34832f8da536af18bb25236981b31c04        1d9e91ebeec0d17>
  main                       09:37 AM       0.71     0.5735   100            4           34832f8da536af18bb25236981b31c04        1d9e91ebeec0d17>
  ├── f75ec1a [lying-zigs]   09:46 AM       0.75     0.6795   100            12          34832f8da536af18bb25236981b31c04        1d9e91ebeec0d17>
  ├── fad90d2 [erect-flap]   09:45 AM      0.745     0.6483   100            6           34832f8da536af18bb25236981b31c04        1d9e91ebeec0d17>
  └── 422a560 [stone-leas]   09:45 AM        0.6     0.2453   100            2           34832f8da536af18bb25236981b31c04        1d9e91ebeec0d17>
 ───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────>

Step 7: Identify the best experiment

Look at the f1_score column.

Find the experiment with the highest f1_score.

For example:

exp-f75ec1a has the highest f1_score of 0.6795.

Step 8: Apply the best experiment

Apply it to your workspace:

```
dvc exp apply <experiment-name>
```
Example:

```
root@controlplane fraud-detection on  main [!] ➜  dvc exp apply f75ec1a
Building workspace index                                                                                             |7.00 [00:00, 1.46kentry/s]
Comparing indexes                                                                                                    |8.00 [00:00, 4.61kentry/s]
Applying changes                                                                                                      |0.00 [00:00,     ?file/s]
Changes for experiment 'f75ec1a' have been applied to your current workspace.
```
This updates:

params.yaml
metrics.json
models/model.pkl

to match the selected experiment.

Step 9: Verify the applied parameter

```
cat params.yaml
```

Example:

```
root@controlplane fraud-detection on  main [!] ➜  cat params.yaml
n_estimators: 100
max_depth: 12
```

Step 10: Verify the metrics

```
dvc metrics show
```

The displayed metrics should match the winning experiment.

Example:

```
root@controlplane fraud-detection on  main [!] ➜  dvc metrics show
Path          accuracy    f1_score
metrics.json  0.75        0.6795
```

Step 11: Verify the model

```
ls -l models/model.pkl
```

The model file should have been regenerated for the applied experiment.

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

- What is a DVC Experiment?

Normally, if you want to test a new model configuration, you would:

Edit params.yaml.
Run dvc repro.
Record the results.
Repeat.

This becomes tedious and makes it hard to compare experiments.

DVC Experiments automate this process.

- Why run multiple experiments?

Different values of max_depth can affect model performance.

For example:

max_depth = 2

A very shallow tree may underfit the data.

max_depth = 6

May provide a good balance.

max_depth = 12

A deeper tree may overfit.

The goal is to find the value that gives the best f1_score.

- What does dvc exp run do?

When you run:

dvc exp run -S max_depth=6

DVC temporarily changes:

max_depth: 6

runs the pipeline, records the metrics, and saves the results as a named experiment.

Your Git history remains unchanged.

- Why use dvc exp show?

Instead of opening each metrics.json manually, DVC displays all experiments together.

Example:

Experiment      max_depth    accuracy    f1_score
--------------------------------------------------
exp-A               2          0.88        0.84
exp-B               6          0.94        0.92
exp-C              12          0.93        0.90

This makes it easy to compare different parameter values.

- What does dvc exp apply do?

Once you've found the experiment with the highest f1_score, apply it:

dvc exp apply exp-B

This updates your workspace so that it reflects the best experiment:

params.yaml uses the winning max_depth.
metrics.json contains the winning metrics.
models/model.pkl is the model trained with those parameters.


---
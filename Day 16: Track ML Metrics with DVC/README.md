# Lab Information

After training a model, the xFusionCorp Industries ML team requires DVC to surface model metrics through dvc metrics show. Although the fraud-detection pipeline successfully trains a model and generates a metrics.json file, DVC currently does not recognize this file as a metric. Ensure that the metrics.json file is properly configured to be recognized by DVC.

A project exists at /root/code/fraud-detection/ with a three-stage DVC pipeline (process_data, split_data, train). The train stage runs src/models/train.py, which writes the model to models/model.pkl and metrics to metrics.json. Do not modify the Python files.

Acceptance criteria:

    The train stage in dvc.yaml declares metrics.json as a DVC metric output rather than a regular file output, with cache: false so the JSON lives in Git for diff history rather than in the DVC cache.
    The pipeline has been reproduced so the metric registration takes effect, and dvc metrics show reports the accuracy and f1_score values from metrics.json.

    Tip: once the metric is registered, dvc metrics diff compares its values across Git commits, which is useful when iterating on the model.

---

# Lab Solutions


✅ Part 1: Lab Step-by-Step Guidelines

Step 1: Move into the project

```
cd /root/code/fraud-detection
```

Step 2: Inspect the current train stage

Open dvc.yaml:

```
cat dvc.yaml
```


Notice that metrics.json is missing.

Step 3: Modify the train stage

Add a metrics section.

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
    params:
      - n_estimators
    outs:
      - models/model.pkl
    metrics:
      - metrics.json:
          cache: false
```



Important: Do not put metrics.json under outs:. The lab specifically requires it to be declared as a metric with cache: false.

Step 4: Save dvc.yaml and add params.yaml if it doesn't exist.

Verify the file:

```
cat dvc.yaml
cat > params.yaml <<EOF
n_estimators: 100
EOF
```

Step 5: Reproduce the pipeline

Run:

```
dvc repro
```

This regenerates:

models/model.pkl
metrics.json
dvc.lock

Step 6: Verify the metric

Run:

```
dvc metrics show
```

Expected output will look similar to:

Path          accuracy    f1_score
metrics.json  1.0         1.0

The exact values will vary.

Step 7: Verify the metric file

```
cat metrics.json
```

Example:

{
  "accuracy": 1.0,
  "f1_score": 1.0
}

Step 8: Verify dvc.lock

(Optional)

cat dvc.lock

You should see metrics.json recorded under the train stage.

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

- What is metrics.json?

After training the model, the Python script creates a file called:

metrics.json

It contains information about how well the model performed.

For example:

{
  "accuracy": 1.0,
  "f1_score": 1.0
}

These numbers help you evaluate the model.

- Why doesn't DVC recognize it automatically?

DVC only knows about files that are declared in dvc.yaml.

Currently, DVC knows about:

input files (deps)
output files (outs)
parameters (params)

It doesn't know that metrics.json contains evaluation metrics.

- Why use metrics: instead of outs:?

Regular outputs:

outs:
  - models/model.pkl

are treated as artifacts (files produced by the pipeline).

Metrics are different—they are values you want to compare between experiments.

So we use:

metrics:
  - metrics.json:
      cache: false

This tells DVC:

metrics.json contains evaluation metrics.
Keep it in Git (cache: false) instead of storing it in the DVC cache.
Allow commands like dvc metrics show and dvc metrics diff to read it.

- Why cache: false?

Normally, DVC stores outputs in its cache.

For metrics, we want the JSON file to stay in the Git repository because:

it's small,
it's easy to compare between commits,
Git can track its history.

That's why the lab requires:

cache: false

- What does dvc metrics show do?

After running:

dvc repro

you can execute:

dvc metrics show

Instead of opening metrics.json manually, DVC displays the important values in a table, for example:

Path          accuracy    f1_score
metrics.json  1.0         1.0

- What is dvc metrics diff?

As you experiment with different model parameters (such as n_estimators), the metrics may change.

Running:

dvc metrics diff

compares the metric values across Git commits, making it easy to see whether the new model performed better or worse.

---
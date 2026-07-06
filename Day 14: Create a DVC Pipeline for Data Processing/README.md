# Lab Information

The xFusionCorp Industries ML team utilizes DVC pipelines to ensure the reproducibility of data processing. The fraud-detection project has the processing scripts and raw data in place but does not yet define a pipeline. Define a two-stage DVC pipeline so the data processing runs reproducibly from start to finish with dvc repro.

    A project exists at /root/code/fraud-detection/ with DVC initialised. The scripts are at src/data/process_data.py and src/data/split_data.py, and the raw input is at data/raw/transactions.csv. Do not modify the Python files or the input data.

    Create a dvc.yaml defining two stages (use dvc stage add, or write the YAML directly):
        process_data – runs python3 src/data/process_data.py; depends on data/raw/transactions.csv and src/data/process_data.py; produces data/processed/clean_transactions.csv.
        split_data – runs python3 src/data/split_data.py; depends on data/processed/clean_transactions.csv (the upstream stage's output, so DVC chains the stages) and src/data/split_data.py; produces data/processed/train.csv and data/processed/test.csv.

    Run the pipeline with dvc repro so both stages execute in order and dvc.lock is written.

    After your changes, dvc status must report no stale stages.

    Use python3 (not python) in the stage commands. Once the pipeline is valid, dvc dag prints the dependency graph showing how the two stages chain together.

---

# Lab Solutions

✅ Part 1: Lab Step-by-Step Guidelines

Step 1: Move into the repository

```
cd /root/code/fraud-detection
```

Step 2: Verify the required files

```
find src
find data
```

You should have:

src
src/data
src/data/process_data.py
src/data/split_data.py
data
data/raw
data/raw/transactions.csv

Step 3: Create the first DVC stage (process_data)

Run:

```
dvc stage add \
-n process_data \
-d data/raw/transactions.csv \
-d src/data/process_data.py \
-o data/processed/clean_transactions.csv \
python3 src/data/process_data.py
```

Explanation of options
Option	Meaning
-n	Stage name
-d	Dependency
-o	Output
Last argument	Command to execute

Step 4: Create the second DVC stage (split_data)

Run:

```
dvc stage add \
-n split_data \
-d data/processed/clean_transactions.csv \
-d src/data/split_data.py \
-o data/processed/train.csv \
-o data/processed/test.csv \
python3 src/data/split_data.py
```

Notice that the dependency:

data/processed/clean_transactions.csv

is the output from Stage 1. This automatically links the stages together.

Step 5: Verify dvc.yaml

```
cat dvc.yaml
```

Expected structure:

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
    - data/processed/test.csv
    - data/processed/train.csv
```

Step 6: Run the pipeline

```
dvc repro
```

Expected output:

```
Running stage 'process_data':                                                  
> python3 src/data/process_data.py
Processed 15 rows
Generating lock file 'dvc.lock'                                                
Updating lock file 'dvc.lock'

Running stage 'split_data':                                                    
> python3 src/data/split_data.py
Train: 12 rows, Test: 3 rows
Updating lock file 'dvc.lock'                                                  

To track the changes with git, run:

        git add dvc.lock data/processed/.gitignore

To enable auto staging, run:

        dvc config core.autostage true
Use `dvc push` to send your updates to remote storage.
```

Step 7: Verify dvc.lock

```
ls
```
Expected:

```
data
dvc.lock
dvc.yaml
src
```

Step 8: Check pipeline status

```
dvc status
```

Expected:

Data and pipelines are up to date.

This satisfies the lab requirement of having no stale stages.

Step 9: Display the dependency graph

```
dvc dag
```

Expected graph:

```
WARNING: Unable to find `less` in the PATH. Check out <https://man.dvc.org/pipeline/show> for more info.
+--------------+ 
| process_data | 
+--------------+ 
        *        
        *        
        *        
 +------------+  
 | split_data |  
 +------------+ 
```

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

- What is a DVC Pipeline?

A DVC pipeline defines a sequence of steps for processing data. Each step is called a stage.

Instead of manually running scripts one by one, DVC knows:

which script to run
what input files it needs
what output files it creates
which stage depends on another

This makes the workflow reproducible.

- Why do we create two stages?

The project has two separate tasks:

Stage 1: Process the raw data

Input:

data/raw/transactions.csv

Script:

src/data/process_data.py

Output:

data/processed/clean_transactions.csv

This stage cleans or prepares the raw dataset.

Stage 2: Split the processed data

Input:

data/processed/clean_transactions.csv

Script:

src/data/split_data.py

Outputs:

data/processed/train.csv
data/processed/test.csv

This stage creates the training and testing datasets.

- How are the stages connected?

The output of the first stage becomes the input of the second stage:

transactions.csv
        │
        ▼
process_data
        │
        ▼
clean_transactions.csv
        │
        ▼
split_data
      ├──────────┐
      ▼          ▼
 train.csv   test.csv

This dependency allows DVC to automatically execute the stages in the correct order.

- What does dvc repro do?

The command:

dvc repro

checks the pipeline and runs any stages that need to be executed.

In this lab:

process_data runs first because it depends on the raw dataset.
After clean_transactions.csv is created, split_data runs because its dependency is now available.

You don't need to run the Python scripts manually—DVC handles the execution order.

- What is dvc.lock?

After a successful pipeline run, DVC creates:

dvc.lock

This file records:

the exact command used
the dependencies
the output files
checksums (hashes) of inputs and outputs

It ensures that the pipeline can be reproduced exactly in the future.

---
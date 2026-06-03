# Lab Information

A colleague has started a new ML project at /root/code/fraud-detection/, but the layout does not match the xFusionCorp Industries standard. Bring the project in line with the team's conventions.

    Inspect the existing project at /root/code/fraud-detection/.

    The final layout must match the tree below exactly:

fraud-detection/
├── data/
│   ├── raw/
│   └── processed/
├── models/
├── notebooks/
├── src/
│   ├── data/
│   ├── features/
│   ├── models/
│   └── utils/
├── tests/
├── configs/
├── requirements.txt
└── README.md

    Every subdirectory under src/ must contain an __init__.py file so that Python recognises it as a package.

    requirements.txt must list the following dependencies, one per line: scikit-learn, pandas, numpy, and mlflow. The canonical PyPI name for the scikit-learn package is scikit-learn.

    README.md must begin with the heading # fraud-detection.

    Review the existing project and correct everything that does not match the requirements above.

---

# Lab Solutions

🧭 Part 1: Lab Step-by-Step Guidelines

Run the following commands on the controlplane host.

Step 1 — Move into the project directory

```
cd /root/code/fraud-detection
```

Step 2 — Inspect the current structure

```
tree
```

If tree is unavailable:

```
sudo apt update && sudo apt install tree
```

Step 3 — Check  the required directory structure

Run:

```
tree
```


1. Rename incorrect directories

```
mv src/feature src/features
mv src/util src/utils
```

2. Create missing directories

```
mkdir -p data/raw
mkdir -p data/processed
mkdir -p tests
mkdir -p configs
```

3. Verify __init__.py still exists

Check:

```
ls src/features
ls src/utils
```

You should see:

__init__.py



Step 4 — Verify and fix requirements.txt

Create/update the file:

```
cat > requirements.txt 
```

Output:

```
sklearn
pandas
numpy
```

Create the correct requirements.txt:

```
cat > requirements.txt <<EOF
scikit-learn
pandas
numpy
mlflow
EOF
```

Create/update the README:

```
cat README.md 
```

Output:

```
# Fraud

ML project for fraud detection at xFusionCorp Industries.
```

Replace the README

Run:

```
cat > README.md <<EOF
# fraud-detection

ML project for fraud detection at xFusionCorp Industries.
EOF
```

Step 7 — Verify the final structure and README.md content

Run:

```
tree
cat README.md 
```

Expected structure:

fraud-detection/
├── data/
│   ├── raw/
│   └── processed/
├── models/
├── notebooks/
├── src/
│   ├── data/
│   │   └── __init__.py
│   ├── features/
│   │   └── __init__.py
│   ├── models/
│   │   └── __init__.py
│   └── utils/
│       └── __init__.py
├── tests/
├── configs/
├── requirements.txt
└── README.md


```
root@controlplane ~/code/fraud-detection via 🐍 v3.12.3 ➜  cat README.md 
# fraud-detection

ML project for fraud detection at xFusionCorp Industries.
```

---

🧠 Part 2: Simple Beginner-Friendly Explanation

This lab focuses on organising a machine learning project according to the xFusionCorp Industries standard structure.

The goal is to:

standardise project layout
improve maintainability
make collaboration easier for developers and data scientists

You must inspect the existing project and correct anything that does not match the required structure.

Understanding the Required Project Structure

The final project must look exactly like this:

fraud-detection/
├── data/
│   ├── raw/
│   └── processed/
├── models/
├── notebooks/
├── src/
│   ├── data/
│   ├── features/
│   ├── models/
│   └── utils/
├── tests/
├── configs/
├── requirements.txt
└── README.md

Each folder has a specific purpose in an ML workflow.

Purpose of Each Directory

data/
Stores datasets used in the project.

data/raw/
Contains original unmodified data.

Example:
transactions.csv

data/processed/
Contains cleaned or transformed datasets used for training.

Example:
clean_transactions.csv

models/
Stores trained machine learning models.

Example:
fraud_model.pkl

notebooks/
Contains Jupyter notebooks for experimentation and analysis.

Example:
eda.ipynb

src/
Contains the main Python source code for the application.
This keeps project logic organised and modular.

Why __init__.py Files Are Required
Every subdirectory under src/ must contain:
__init__.py

This tells Python:
“Treat this directory as a Python package.”

Without these files:
imports may fail
modules may not be recognised correctly

Example:
from src.models.train import train_model

Purpose of src/ Subdirectories

Directory	    Purpose
src/data	    Data loading and preprocessing
src/features	Feature engineering logic
src/models	    Training and prediction code
src/utils	    Helper functions and utilities

Why requirements.txt Matters
The lab requires the following dependencies:

scikit-learn
pandas
numpy
mlflow

This file helps developers install all required Python packages consistently.

Important note:

the correct PyPI package name is scikit-learn
not sklearn

Why README.md Matters
The README file provides project documentation.

The lab specifically requires it to begin with:

# fraud-detection

This acts as the project title and ensures naming consistency.

Why Exact Naming Is Important
Lab validators check:
exact folder names
exact file names
exact dependency names

Even small differences such as:
feature instead of features
util instead of utils
# Fraud instead of # fraud-detection
can cause the lab to fail.

---
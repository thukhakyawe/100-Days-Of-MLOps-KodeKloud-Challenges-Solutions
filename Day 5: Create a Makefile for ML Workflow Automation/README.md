# Lab Information

The xFusionCorp Industries ML team uses a Makefile to orchestrate common tasks—data processing, training, testing, and cleanup. A draft Makefile exists at /root/code/fraud-detection/Makefile, but make all does not complete successfully. Bring the Makefile in line with the team's standard.

    Change into /root/code/fraud-detection/ and run make all to observe the current failure.

    The corrected Makefile must declare the following six targets and behaviour:
        setup – Creates a virtual environment at mlops-venv/ and installs dependencies from requirements.txt;
        data – Runs python src/data/process_data.py;
        train – Runs python src/models/train.py;
        test – Runs pytest tests/;
        clean – Recursively removes every __pycache__ directory, removes .pytest_cache, and clears the contents of models/;
        all – Runs setup, data, train, and test in that order.

    All six target names must be declared as .PHONY so that Make never confuses them with files of the same name.

    After your changes, make all must complete without error.

Makefile recipes must be indented with a real tab character, not spaces. Make rejects any recipe that is not tab-indented.


---

# Lab Solutions

🧭 Part 1: Lab Step-by-Step Guidelines

Step 1 — Move into the project directory

```
cd /root/code/fraud-detection
```

Step 2 — Run the existing Makefile

```
make all
```

This helps identify:

Makefile:7: *** missing separator.  Stop.

Step 3 — Open the Makefile

```
vi Makefile
```

Replace the contents with the following Makefile.

```
.PHONY: setup data train test clean all

setup:
	python3 -m venv mlops-venv
	./mlops-venv/bin/pip install -r requirements.txt
	./mlops-venv/bin/pip install pytest

data:
	./mlops-venv/bin/python src/data/process_data.py

train:
	./mlops-venv/bin/python src/models/train.py

test:
	./mlops-venv/bin/pytest tests/

clean:
	find . -type d -name "__pycache__" -exec rm -rf {} +
	rm -rf .pytest_cache
	rm -rf models/*

all: setup data train test
```


Step 4  — Run make all again

```
make all
```

It should now complete successfully without errors.

Example Output:

```
Installing collected packages: pygments, pluggy, iniconfig, pytest
Successfully installed iniconfig-2.3.0 pluggy-1.6.0 pygments-2.20.0 pytest-9.0.3
./mlops-venv/bin/python src/data/process_data.py
./mlops-venv/bin/python src/models/train.py
./mlops-venv/bin/pytest tests/
============================= test session starts =============================
platform linux -- Python 3.12.3, pytest-9.0.3, pluggy-1.6.0
rootdir: /root/code/fraud-detection
plugins: anyio-4.13.0
collected 1 item                                                              

tests/test_smoke.py .                                                   [100%]

============================== 1 passed in 0.01s ==============================
```

---

🧠 Part 2: Simple Beginner-Friendly Explanation


What is a Makefile?

A Makefile is an automation script used with the make command.

It helps automate repetitive tasks such as:

setting up environments
processing data
training models
running tests
cleaning project files

Instead of typing many commands manually, developers can run:

make all

Understanding Each Required Target

setup

Creates the virtual environment and installs dependencies.

setup:
	python3 -m venv mlops-venv

This creates an isolated Python environment named:

mlops-venv/

Then dependencies are installed from:

requirements.txt

data
Runs the data processing script.

data:
	./mlops-venv/bin/python src/data/process_data.py

This step usually:

loads raw data
cleans data
transforms datasets

train
Runs the machine learning training script.

train:
	./mlops-venv/bin/python src/models/train.py

This step trains the ML model.

test
Runs automated tests.

test:
	./mlops-venv/bin/pytest tests/

This checks whether the project works correctly.

clean
Removes temporary/generated files.

clean:
It removes:
Python cache folders
pytest cache
generated model files
This keeps the project clean.

all
Runs the entire workflow.

all: setup data train test
This means:
setup
process data
train model
run tests

in that exact order.

Why .PHONY Is Important
.PHONY: setup data train test clean all

Without .PHONY, Make may confuse target names with actual files or directories.

Example:
if a folder named test/ exists
Make might incorrectly think the target already completed

.PHONY forces Make to always run the commands.

Why TAB Characters Matter
Makefiles are very strict.

Recipe lines must start with:
a real TAB character

NOT:
spaces

Correct:

setup:
<TAB>python3 -m venv mlops-venv

Incorrect:

setup:
    python3 -m venv mlops-venv

Using spaces causes errors like:

missing separator

---
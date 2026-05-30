# Lab Information

The xFusionCorp Industries data science team needs a standardised Python environment for their new ML project. Set up a virtual environment with the required ML libraries on the controlplane host.

    Create a Python virtual environment named ml-env under /root/code/ using python3 -m venv.

    Activate the environment and install the following packages: numpy, pandas, scikit-learn, and matplotlib.

    Generate a requirements.txt file using pip freeze and save it at /root/code/requirements.txt.



---

# Lab Solutions

🧭 Part 1: Lab Step-by-Step Guidelines

Run the following commands on the controlplane host:


1.Create the Python virtual environment

```
python3 -m venv /root/code/ml-env
```

2.Activate the virtual environment

```
source /root/code/ml-env/bin/activate
```

After activation, your terminal should show something like:

(ml-env)

3.Install the required ML libraries

```
pip install numpy pandas scikit-learn matplotlib
```

4.Generate the requirements.txt file

```
pip freeze > /root/code/requirements.txt
```

5.Verify the file

```
cat /root/code/requirements.txt
```

---

🧠 Part 2: Simple Beginner-Friendly Explanation

What is a virtual environment?

A virtual environment is an isolated Python workspace.
It keeps project packages separate from the system Python packages.

This helps:

avoid package conflicts
keep projects clean
make setups reproducible
Step Breakdown

Step 1 — Create /root/code

mkdir -p /root/code
mkdir = make directory
-p = create parent directories if needed

This creates the folder where the project will live.

Step 2 — Create the virtual environment

python3 -m venv /root/code/ml-env

Explanation:

python3 → use Python 3
-m venv → run Python’s built-in virtual environment module
/root/code/ml-env → name and location of the environment

This creates:

isolated Python binaries
isolated package storage

Step 3 — Activate the environment

source /root/code/ml-env/bin/activate

This tells the shell:

“Use the Python and pip inside ml-env.”

Without activation, packages may install globally instead.

Step 4 — Install ML libraries

pip install numpy pandas scikit-learn matplotlib

What each library does:

Package	Purpose
numpy	Numerical computing
pandas	Data analysis & tables
scikit-learn	Machine learning
matplotlib	Data visualization & plotting

Step 5 — Save installed packages

pip freeze > /root/code/requirements.txt
pip freeze lists installed packages
> writes output into a file

This creates a reusable dependency file.

Example:

numpy==2.x.x
pandas==2.x.x
scikit-learn==1.x.x
matplotlib==3.x.x

Step 6 — Verify

cat /root/code/requirements.txt

This displays the contents of the file to confirm everything was saved correctly.

---

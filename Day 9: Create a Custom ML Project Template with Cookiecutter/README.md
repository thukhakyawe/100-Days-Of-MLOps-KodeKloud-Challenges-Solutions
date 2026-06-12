# Lab Information

The xFusionCorp Industries ML platform team maintains a Cookiecutter template that new ML projects are generated from. A draft template exists at /root/code/mlops-template/, but it does not render. Correct the template and use it to generate a project.

    A Cookiecutter template exists at /root/code/mlops-template/. cookiecutter is installed system-wide.

    The corrected template must satisfy every one of the following:
        The cookiecutter.json declares four variables:
            project_name (default my-ml-project)
            author (default xFusionCorp)
            python_version (default 3.11)
            ml_framework with the choices sklearn, pytorch, and tensorflow
        The generated requirements.txt logic:
            Contains scikit-learn when ml_framework is sklearn
            Contains torch when ml_framework is pytorch
            Contains tensorflow when ml_framework is tensorflow
        The generated README.md content:
            Must reference both the project_name and the author from cookiecutter variables.
        The template directory structure {{cookiecutter.project_name}}/ must contain:
            Files: README.md and requirements.txt
            Directories: data/, models/, src/, and tests/

    Review the existing template in the VS Code explorer and correct everything that prevents it from rendering.

    Once the template renders, generate a project at /root/code/churn-model/:

   cookiecutter /root/code/mlops-template/ -o /root/code/ --no-input project_name=churn-model ml_framework=sklearn

    The generated project must contain a requirements.txt listing scikit-learn and a README.md that mentions xFusionCorp.

---

# Lab Solutions

✅ Part 1: Lab Step-by-Step Guidelines

This is a Cookiecutter template repair and generation task.

1. Inspect the existing template

```
cd /root/code/mlops-template
find . -type f
```

Check:

```
cat cookiecutter.json
find . -type d
```

2. Correct cookiecutter.json

It must contain exactly these variables and defaults:

```
{
    "project_name": "my-ml-project",
    "author": "xFusionCorp",
    "python_version": "3.11",
    "ml_framework": [
        "sklearn",
        "pytorch",
        "tensorflow"
    ]
}
```
3. Ensure template directory name is correct

Cookiecutter project template directory must be:

{{cookiecutter.project_name}}/

Inside it create:

{{cookiecutter.project_name}}/
├── README.md
├── requirements.txt
├── data/
├── models/
├── src/
└── tests/

4. Fix README.md

README must reference both variables:

```
# {{ cookiecutter.project_name }}

Created by {{ cookiecutter.author }}.
```

5. Fix requirements.txt conditional logic

Use Jinja conditionals:

```
{% if cookiecutter.ml_framework == 'sklearn' %}
scikit-learn
{% elif cookiecutter.ml_framework == 'pytorch' %}
torch
{% elif cookiecutter.ml_framework == 'tensorflow' %}
tensorflow
{% endif %}
```

6. Generate the project

Run exactly:

```
cookiecutter /root/code/mlops-template/ \
-o /root/code/ \
--no-input \
project_name=churn-model \
ml_framework=sklearn
```

7. Verify output

```
apt update
apt install -y tree
tree /root/code/churn-model
```

Check README:

```
cat /root/code/churn-model/README.md
```

Expected Output:

```
root@controlplane ~/code/mlops-template ➜  cat /root/code/churn-model/README.md
# churn-model
```


Check requirements:

```
cat /root/code/churn-model/requirements.txt
```

Expected Output:

```
root@controlplane ~/code/mlops-template ➜  cat /root/code/churn-model/requirements.txt
scikit-learn
```

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

- What is broken?
A Cookiecutter template is a project blueprint. Cookiecutter reads:

cookiecutter.json
template folders
template files

and generates a new project.

If any variable names, folder names, or Jinja syntax are wrong, rendering fails.

- Why do we need cookiecutter.json?
This file tells Cookiecutter which values users can provide.

Required values:

Variable	Default
project_name	my-ml-project
author	xFusionCorp
python_version	3.11
ml_framework	sklearn / pytorch / tensorflow

- Why use {{cookiecutter.project_name}}?
Cookiecutter replaces:

{{cookiecutter.project_name}}

with:

churn-model

when generating the project.

So the generated folder becomes:

/root/code/churn-model

- Why use Jinja conditions in requirements.txt?

The lab wants different packages depending on the framework.

If:

ml_framework=sklearn

then output:

scikit-learn

If:

ml_framework=pytorch

then output:

torch

If:

ml_framework=tensorflow

then output:

tensorflow

Jinja if/elif statements make this happen automatically.

---
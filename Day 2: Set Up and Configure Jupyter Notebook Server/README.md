# Lab Information

A teammate has configured a JupyterLab server for the xFusionCorp Industries data science team, but the server does not behave correctly. Inspect the configuration, diagnose the issues, and start the server.

    JupyterLab is already installed in the virtual environment at /root/code/ml-env/. The team's configuration file is at /root/code/jupyter_lab_config.py and is visible in the file explorer.

    When JupyterLab is started, the Jupyter UI button at the top of the lab must open the notebook interface.

    For this to work, the running server must meet the following requirements:
        it listens on port 8888;
        it binds on 0.0.0.0 (the lab proxy cannot reach a server that is only bound on 127.0.0.1);
        the notebook root directory is /root/notebooks/, and that directory exists on disk.

    Open the configuration file, identify every setting that prevents the requirements above from being met, and correct it. Create any missing directories.

    Start JupyterLab from the virtual environment using the corrected configuration:

   source /root/code/ml-env/bin/activate
   jupyter lab --config=/root/code/jupyter_lab_config.py --allow-root --no-browser &

Make sure JupyterLab is running before using the button at the top of the lab.


---

# Lab Solutions

🧭 Part 1: Lab Step-by-Step Guidelines

Run the following steps on the controlplane host.


Step 1 — Activate the virtual environment

```
source /root/code/ml-env/bin/activate
```

Step 2 — Open the JupyterLab configuration file

You can inspect it with:

```
cat /root/code/jupyter_lab_config.py
```

Or edit it directly:


```
vi /root/code/jupyter_lab_config.py
```
Step 3 — Fix the required settings

Ensure the configuration contains the following correct values:

c.ServerApp.port = 8888
c.ServerApp.ip = '0.0.0.0'
c.ServerApp.notebook_dir = '/root/notebooks'

Remove or correct any conflicting settings such as:

wrong port
127.0.0.1
incorrect notebook directory

Step 4 — Create the notebook directory

```
mkdir -p /root/notebooks
```

Step 5 — Start JupyterLab

```
jupyter lab --config=/root/code/jupyter_lab_config.py --allow-root --no-browser &
```
Step 6 — Verify JupyterLab is running

```
ps -ef | grep jupyter
```

Expected resuld should show:

```
root        3460    1880  0 00:49 pts/2    00:00:01 /root/code/ml-env/bin/python3 /root/code/ml-env/bin/jupyter-lab --config=/root/code/jupyter_lab_config.py --allow-root --no-browser
root        3751    3649  0 00:49 pts/6    00:00:00 grep --color=auto jupyter
```

You can also verify the listening port:

```
ss -tulnp | grep 8888
```

Expected result should show:

```
tcp   LISTEN 0      128          0.0.0.0:8888      0.0.0.0:*    users:(("jupyter-lab",pid=3460,fd=6)) 
```

---

🧠 Part 2: Simple Beginner-Friendly Explanation

What is happening in this lab?

JupyterLab is already installed, but its configuration is incorrect.

Your job is to:

inspect the config
fix bad settings
create the required notebook directory
start the server correctly
Understanding the Required Settings

The lab requires:

Requirement	Meaning
Port 8888	Jupyter must listen on this port
Bind to 0.0.0.0	Allow external access from the lab proxy
Root directory /root/notebooks	This is where notebooks are stored
Why 127.0.0.1 Causes Problems

If Jupyter binds to:

127.0.0.1

It only accepts local connections from inside the server itself.

The lab UI button cannot reach it.

Using:

0.0.0.0

means:

“Accept connections from all network interfaces.”

This allows the lab proxy to connect.

Understanding the Config File

Typical Jupyter config lines look like:

c.ServerApp.port = 8888

Meaning:

c = configuration object
ServerApp = Jupyter server settings
port = listening port
Why Create /root/notebooks

Jupyter needs a valid working directory.

If the folder does not exist:

startup may fail
notebook browser may break

Creating it with:

mkdir -p /root/notebooks

ensures the directory exists.

Starting JupyterLab

Command:

jupyter lab --config=/root/code/jupyter_lab_config.py --allow-root --no-browser &

Explanation:

Option	        Purpose
--config=	    Use the specified config file
--allow-root	Permit running as root
--no-browser	Do not open a GUI browser
&	            Run in background

---
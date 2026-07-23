# Lab Information

The xFusionCorp Industries ML team is in the process of adopting MLflow for their experiment tracking. Your task is to set up a local MLflow tracking server on the ML pipeline workstation, enabling the team to log experiments from their training code.

MLflow is pre-installed on the controlplane. Launch the tracking server in the background and choose the flags that satisfy every end-state requirement below.

    The server listens on port 5000 and is reachable on all network interfaces, not only localhost.

    The backend store is a SQLite database at /root/code/mlflow-backend/mlflow.db. Create any parent directory first — MLflow aborts at startup if the backend directory is missing.

    The artifact root is /root/code/mlflow-artifacts/.

    The MLflow UI button at the top of the lab routes through the lab proxy, which reaches the server with a non-localhost host header and a different origin. Launch the server so it accepts any host header and any origin; otherwise the button returns a 403 or CORS error.

    The server process persists in the background so it survives terminal closure.

    Once the server is running, the Default experiment can be viewed from the MLflow UI button. The experiment is empty.


---

# Lab Solutions

✅ Part 1: Lab Step-by-Step Guidelines

This lab is about starting an MLflow Tracking Server with the correct configuration so it can be accessed through the KodeKloud lab proxy.

Step 1: Create the backend directory

MLflow will fail to start if the backend database directory does not exist.

Run:

```
mkdir -p /root/code/mlflow-backend
```

Step 2: Create the artifact directory

The artifact root must also exist.

Run:

```
mkdir -p /root/code/mlflow-artifacts
```

Step 3: Start the MLflow Tracking Server

Run the following command exactly:

```
nohup mlflow server \
  --host 0.0.0.0 \
  --port 5000 \
  --backend-store-uri sqlite:////root/code/mlflow-backend/mlflow.db \
  --artifacts-destination /root/code/mlflow-artifacts \
  --allowed-hosts "*" \
  --cors-allowed-origins "*" \
  > mlflow.log 2>&1 &
```

What each option does

nohup → Keeps the server running after the terminal closes.
--host 0.0.0.0 → Listens on all network interfaces.
--port 5000 → Uses port 5000.
--backend-store-uri → Stores MLflow metadata in the SQLite database.
--artifacts-destination → Stores model artifacts and other files.
--allowed-hosts "*" → Accepts requests with any Host header.
--cors-allowed-origins "*" → Allows requests from any origin (required for the lab proxy).

Step 4: Verify the server is running

Run:

```
ps -ef | grep mlflow
```

You should see an MLflow server process.

Step 5: Verify port 5000

Run:

```
ss -lntp | grep 5000
```

Expected output should show something similar to:

LISTEN 0 128 0.0.0.0:5000

Step 6: Open the MLflow UI

Click the MLflow UI button at the top of the lab.

You should see:

Default experiment
No runs
Empty experiment list

Step 7: Verify the database

Run:

```
ls -l /root/code/mlflow-backend
```
Expected:

mlflow.db

Step 8: Verify the artifact directory

Run:

```
ls -l /root/code/mlflow-artifacts
```

The directory may be empty, which is expected until experiments log artifacts.

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

This lab sets up an MLflow Tracking Server, which is used to record machine learning experiments.

Think of the server as a central place where ML engineers store information about training runs, such as:

Parameters (for example, learning rate or number of trees)
Metrics (accuracy, precision, F1 score)
Models
Artifacts (plots, model files, reports)

- Why do we create the backend directory?

MLflow stores its metadata in a SQLite database. The database file is:

/root/code/mlflow-backend/mlflow.db

If the /root/code/mlflow-backend directory does not exist, MLflow cannot create the database and exits immediately.

- Why do we create the artifact directory?

MLflow separates metadata from artifacts.

Metadata is stored in the SQLite database.
Artifacts (such as trained models, plots, and output files) are stored in:
/root/code/mlflow-artifacts/

Creating this directory ensures MLflow has a valid location to store uploaded files.

- Why use --host 0.0.0.0?

By default, MLflow listens only on localhost, meaning only the local machine can connect.

Using:

--host 0.0.0.0

allows the server to accept connections from any network interface. This is required because the KodeKloud MLflow UI button connects through a proxy rather than directly from the local machine.

- Why are --allowed-hosts "*" and --cors-allowed-origins "*" required?

The lab proxy sends requests with a different Host header and a different Origin than localhost.

If these options are not provided, the browser may display:

403 Forbidden
CORS errors

Allowing all hosts and origins ensures the proxy can access the MLflow server successfully.

- Why use nohup?

Normally, when you close a terminal, any processes started from it stop running.

Using:

nohup ... &

starts the MLflow server in the background and allows it to continue running even after the terminal is closed. This satisfies the lab requirement that the server persists in the background.

---
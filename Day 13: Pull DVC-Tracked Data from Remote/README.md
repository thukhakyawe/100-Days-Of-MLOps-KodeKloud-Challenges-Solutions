# Lab Information

A new xFusionCorp Industries team member has cloned the fraud-detection repository onto a fresh machine. The DVC remote is already configured to point at the team's SeaweedFS bucket, but dvc pull is failing. Diagnose the cause, correct the configuration, and pull the dataset.

    A cloned project exists at /root/code/fraud-detection/ with DVC initialised, the data/raw/transactions.csv.dvc pointer file present, but the dataset itself missing from disk and from the local DVC cache.

    SeaweedFS is already running on the controlplane and the dataset has already been pushed to the dvc-storage bucket—open the SeaweedFS Filer button at the top of the lab and navigate to /buckets/dvc-storage/ to confirm that the object is there.
        S3 endpoint: http://localhost:8333
        Credentials: weedadmin / weedadmin123

    Review .dvc/config and correct everything that prevents dvc pull from authenticating against SeaweedFS.

    After the fix, the s3 remote must use:
        The access key (access_key_id) weedadmin
        The secret key (secret_access_key) weedadmin123.

    Pull the dataset. After the pull, data/raw/transactions.csv must be present on disk and its content must match the hash recorded in the .dvc pointer.




---

# Lab Solutions

✅ Part 1: Lab Step-by-Step Guidelines

Step 1: Move into the repository

```
cd /root/code/fraud-detection
```

Step 2: Verify the dataset is missing

```
ls -la data/raw
```

You should see:

transactions.csv.dvc

but not:

transactions.csv

Step 3: Inspect the DVC configuration

```
cat .dvc/config
```

Look for the s3 remote configuration.

You are specifically looking for incorrect credentials.

Step 4: Fix the SeaweedFS credentials

The lab requires:

Setting	            Value
access_key_id	    weedadmin
secret_access_key	weedadmin123

Configure them:

```
dvc remote modify s3 access_key_id weedadmin
dvc remote modify s3 secret_access_key weedadmin123
```

Step 5: Verify the configuration

```
cat .dvc/config
```

Expected section:

```
[core]
    remote = s3
['remote "s3"']
    url = s3://dvc-storage
    endpointurl = http://localhost:8333
    access_key_id = weedadmin
    secret_access_key = weedadmin123
```

Step 6: Pull the dataset

``` 
dvc pull
```

Expected output:

```
root@controlplane fraud-detection on  main [!] ➜  dvc pull
Collecting                                           |1.00 [00:00,  730entry/s]
Fetching
Building workspace index                             |2.00 [00:00,  661entry/s]
Comparing indexes                                   |4.00 [00:00, 2.90kentry/s]
Applying changes                                     |1.00 [00:00, 1.18kfile/s]
A       data/raw/transactions.csv
1 file fetched and 1 file added
```

Step 7: Verify the dataset exists

```
ls -l data/raw/transactions.csv
```

Expected:

data/raw/transactions.csv

Step 8: Verify DVC status

```
dvc status
```

Expected:

Data and pipelines are up to date.

Step 9: Verify integrity

Check the DVC pointer:

```
cat data/raw/transactions.csv.dvc
```

Example:

```
root@controlplane fraud-detection on  main [!] ➜  cat data/raw/transactions.csv.dvc
outs:
- md5: 555f037ee350464f52122d087f28e857
  size: 446
  hash: md5
  path: transactions.csv
```

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

What happened?

A teammate cloned the Git repository onto a new machine.

Git downloaded:

transactions.csv.dvc

but not:

transactions.csv

because DVC-managed data is stored separately from Git.

Why is the file missing?

Git only stores the small pointer file:

transactions.csv.dvc

The actual dataset lives in SeaweedFS:

SeaweedFS Bucket
└── dvc-storage

To download it, DVC must connect to the remote storage.

Why is dvc pull failing?

The lab says:

Review .dvc/config and correct everything that prevents dvc pull from authenticating.

This means the most likely issue is incorrect credentials.

Required credentials:

access_key_id = weedadmin
secret_access_key = weedadmin123

If either value is wrong, DVC cannot access the SeaweedFS bucket.

What does dvc pull do?

When you run:

dvc pull

DVC:

Reads transactions.csv.dvc
Finds the file hash
Connects to SeaweedFS
Downloads the matching object
Restores:
data/raw/transactions.csv

onto your machine

---
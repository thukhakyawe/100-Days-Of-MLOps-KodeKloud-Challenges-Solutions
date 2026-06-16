# Lab Information

The xFusionCorp Industries ML team uses SeaweedFS as the shared S3-compatible object store for DVC-tracked data. A .dvc/config already declares a remote called s3 for the fraud-detection project, but dvc push currently fails. Correct the configuration and push the tracked data into the SeaweedFS bucket.

    A project exists at /root/code/fraud-detection/ with DVC initialised and data/raw/transactions.csv already tracked.

    SeaweedFS is already running on the controlplane:
        S3 endpoint: http://localhost:8333
        Filer UI: open the SeaweedFS Filer button at the top of the lab (forwarded port 8888) – buckets are visible under /buckets/.
        Credentials: weedadmin / weedadmin123 (already set in .dvc/config)
        Bucket name: dvc-storage (already created and visible in the Filer UI under /buckets/dvc-storage)

    Review the existing .dvc/config and correct everything that prevents dvc push from succeeding. The remote called s3 must:
        point at the dvc-storage bucket using s3://;
        use the correct SeaweedFS S3 endpoint URL;
        be marked as the default remote.

    Push the tracked data. After the push, the dvc-storage bucket in the SeaweedFS Filer UI must contain at least one object under the files/md5/... prefix.

---

# Lab Solutions

✅ Part 1: Lab Step-by-Step Guidelines


Step 1: Move into the repository

```
cd /root/code/fraud-detection
```

Step 2: Inspect the current DVC configuration

View the config:

```
cat .dvc/config
```

Or:

dvc remote list

You will likely find one or more configuration mistakes.

Step 3: Verify the remote configuration

The lab requires:

Setting	Required Value
Remote name	s3
Bucket	s3://dvc-storage
Endpoint URL	http://localhost:8333
Default remote	s3

A correct configuration looks similar to:

```
['remote "s3"']
    url = s3://dvc-storage
    endpointurl = http://localhost:8333
    access_key_id = weedadmin
    secret_access_key = weedadmin123

[core]
    remote = s3
```

Step 4: Fix the configuration
Set the remote URL

```
dvc remote modify s3 url s3://dvc-storage
```

Set the SeaweedFS endpoint

```
dvc remote modify s3 endpointurl http://localhost:8333
```

Set the remote as default

```
dvc remote default s3
```


Step 5: Verify the final configuration

```
cat .dvc/config
```

Confirm:

```
[core]
    remote = s3
['remote "s3"']
    url = s3://dvc-storage
    endpointurl = http://localhost:8333
    access_key_id = weedadmin
    secret_access_key = weedadmin123
```

Step 6: Check DVC status

```
dvc status
```

Expected:

Data and pipelines are up to date.


Step 7: Push the data

Run:

```
dvc push
```

Expected output:

```
root@controlplane fraud-detection on  main [!] ➜  dvc push
Collecting                                           |1.00 [00:00,  658entry/s]
Pushing
1 file pushed   
```

Step 8: Verify remote storage

You can verify from DVC:

```
dvc status -c
```

Expected:

```
root@controlplane fraud-detection on  main [!] ➜  dvc status -c
Cache and remote 's3' are in sync.           
```
Step 9: Verify in SeaweedFS

Open the SeaweedFS Filer UI:

SeaweedFS Filer
→ /buckets/
→ dvc-storage/

You should see a path similar to:

files/
└── md5/
    └── xx/
        └── xxxxxxxxxxxxxxxxxxxx

The exact hash will vary.

This confirms the dataset was uploaded successfully.

![alt text](image.png)

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

- What is happening in this lab?

Previously, DVC was tracking the dataset locally.

Right now:

transactions.csv
      ↓
transactions.csv.dvc

But the actual file content is only stored on your machine.

The team wants all datasets backed up to a shared storage system.

- What is SeaweedFS?

SeaweedFS is acting like a private Amazon S3 service.

Think of it as:

Local Machine
      ↓
DVC
      ↓
SeaweedFS Bucket

Instead of storing datasets in Git, DVC stores them in the bucket.

- Why does dvc push fail?

Usually because one of these is wrong:

Wrong bucket name
Wrong endpoint URL
Remote not set as default

The lab specifically tells us the correct values:

Bucket: dvc-storage
Endpoint: http://localhost:8333
Remote: s3

- What does dvc remote default s3 do?

It tells DVC:

Whenever I push or pull,
use the remote named "s3".

Without this setting, DVC may not know which remote to use.

- What does dvc push do?

When you run:

dvc push

DVC uploads the tracked dataset into SeaweedFS.

Conceptually:

transactions.csv
      ↓
DVC Cache
      ↓
SeaweedFS Bucket

---
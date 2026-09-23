# Lab Information

The deployment team at xFusionCorp Industries requires the implementation of a batch-prediction wrapper for the registered fraud-detector champion model. This wrapper must incorporate a custom preprocessing step before the model can be accessed by downstream services. A skeleton for the pyfunc wrapper has already been created. Your task is to develop the preprocessing step and integrate the necessary MLflow plumbing to load the champion model and execute the batch prediction.

    The MLflow tracking server is already running on port 5000. The MLflow UI button at the top of the lab can be opened to view the dashboard; the Models tab shows fraud-detector registered with a champion alias on version 1.

    In /root/code/predict_with_preprocessing.py, the ScaledPredictor constructor and its pyfunc .predict() signature are provided, as are the MODEL_URI / INPUT_CSV / OUTPUT_CSV constants (the constants and the class and method signatures must NOT change). Three # TODO blocks remain:
        TODO 1: Inside ScaledPredictor.predict(), scale the input array with the wrapper's per-column mean and std, then return the inner model's predictions on the scaled array.
        TODO 2: Load the champion version of fraud-detector from MLflow's Model Registry into a variable named inner_model. MODEL_URI is already set to models:/fraud-detector@champion.
        TODO 3: Run the batch prediction over the pre-staged inputs, attach the predictions as a new prediction column on the inputs DataFrame, and write the result to OUTPUT_CSV (/root/code/predictions.csv) with index=False.

    Once all three TODOs are completed and the script has been run, the end state must include:
        A file at /root/code/predictions.csv with a header row.
        A prediction column in that CSV.
        The number of prediction rows equal to the number of input rows in /root/code/data/inputs.csv (ten).

# Lab Solutions

✅ Part 1: Lab Step-by-Step Guidelines

Step 1 — Open the script

cd /root/code

nano predict_with_preprocessing.py

First, inspect the existing code:

cat /root/code/predict_with_preprocessing.py

Important: Do not change:

MODEL_URI
INPUT_CSV
OUTPUT_CSV
ScaledPredictor constructor signature
ScaledPredictor.predict() signature

Only fill in the three TODO sections.

Step 2 — Complete TODO 1: Preprocessing

Inside ScaledPredictor.predict(), the wrapper should standardize each input column using the wrapper's stored mean and std.

The formula is:

$$ X_{scaled} = \frac{X - mean}{std} $$

Use:

scaled = (X - self.mean) / self.std
return self.model.predict(scaled)

So TODO 1 should look like:

def predict(self, context, model_input, params=None):
    X = np.asarray(model_input, dtype=float)
    scaled = (X - self.mean) / self.std
    return self.model.predict(scaled)

Important

Do not calculate a new mean/std from the incoming batch.

The wrapper already has its own:

self.mean
self.std

Those values must be used for preprocessing.

Step 3 — Complete TODO 2: Load the champion model

The lab already provides:

MODEL_URI = "models:/fraud-detector@champion"

Load the registered champion model using MLflow's pyfunc interface:

inner_model = mlflow.pyfunc.load_model(MODEL_URI)

So TODO 2 should be:

inner_model = mlflow.pyfunc.load_model(MODEL_URI)

This loads the model currently referenced by the champion alias.

Why mlflow.pyfunc.load_model()?

The ScaledPredictor wrapper is itself a PyFunc model, and the inner registered model needs to expose the standard:

.predict()

interface.

Step 4 — Complete TODO 3: Batch prediction

The script already provides the input DataFrame, wrapper, and output path.

The required sequence is:

Run the wrapper's prediction.
Store predictions in a new prediction column.
Write the DataFrame to /root/code/predictions.csv.
Do not write the DataFrame index.

Use:

inputs["prediction"] = predictor.predict(None, inputs)
inputs.to_csv(OUTPUT_CSV, index=False)

So TODO 3 should be:

inputs["prediction"] = predictor.predict(None, inputs)
inputs.to_csv(OUTPUT_CSV, index=False)

Step 5 — Save the file

If using nano:

Ctrl + O
Enter
Ctrl + X

Step 6 — Review the completed script

Run:

cat /root/code/predict_with_preprocessing.py

Your three TODOs should effectively contain:

scaled = (model_input - self.mean) / self.std
return self.inner_model.predict(scaled)
inner_model = mlflow.pyfunc.load_model(MODEL_URI)
inputs["prediction"] = predictor.predict(None, inputs)
inputs.to_csv(OUTPUT_CSV, index=False)

Step 7 — Run the batch prediction

Execute:

cd /root/code
python3 predict_with_preprocessing.py

If successful, the script should finish without a Python traceback.

Step 8 — Verify the output file exists

ls -lh /root/code/predictions.csv

You should see something similar to:

-rw-r--r-- 1 root root ... predictions.csv

Step 9 — Check the CSV

Run:

cat /root/code/predictions.csv

You should see a header containing:

prediction

For example, the structure should look similar to:

amount,num_txn,prediction
...
...

The exact prediction values depend on the registered fraud-detector champion model.

Step 10 — Verify exactly 10 prediction rows

Use:

```
python3 - <<'PY'
import pandas as pd

inputs = pd.read_csv("/root/code/data/inputs.csv")
outputs = pd.read_csv("/root/code/predictions.csv")

print("Input rows:", len(inputs))
print("Output rows:", len(outputs))
print("Columns:", list(outputs.columns))

assert len(inputs) == 10
assert len(outputs) == 10
assert "prediction" in outputs.columns

print("✅ Lab validation passed")
PY
```

Expected:

Input rows: 10
Output rows: 10
Output columns: ['...', 'prediction']
Prediction column exists: True
✅ Validation passed

🔍 Optional: Verify the input file

You can inspect the staged input data:

cat /root/code/data/inputs.csv

And check its row count:

tail -n +2 /root/code/data/inputs.csv | wc -l

Expected:

10

---

🧠 Part 2: Simple Step-by-Step Explanation

1. What is this lab actually doing?

This lab creates a prediction wrapper around an existing MLflow model.

The architecture is essentially:

inputs.csv
    │
    ▼
ScaledPredictor
    │
    │  1. Scale input
    ▼
Scaled data
    │
    │  2. Send to champion model
    ▼
fraud-detector @ champion
    │
    ▼
Predictions
    │
    ▼
predictions.csv

The important part is that the downstream service doesn't directly call the registered model.

Instead:

Downstream service
       ↓
ScaledPredictor
       ↓
Preprocessing
       ↓
fraud-detector champion

2. Why do we need preprocessing?

Machine-learning models often expect data to be transformed before prediction.

For example, suppose the model expects:

amount
num_txn

but these columns have very different scales:

amount = 5000
num_txn = 3

Standardization converts them approximately to:

(amount - mean) / std
(num_txn - mean) / std

This produces a normalized input representation.

The important point is that the same preprocessing used when the model was designed must be applied during inference.

3. What does TODO 1 do?

This:

scaled = (model_input - self.mean) / self.std

takes the incoming data and standardizes every column.

Then:

return self.inner_model.predict(scaled)

passes the transformed data to the actual fraud model.

So ScaledPredictor acts as an adapter:

Raw input
   ↓
Scaling
   ↓
Inner ML model
   ↓
Prediction

4. What does TODO 2 do?

This:

inner_model = mlflow.pyfunc.load_model(MODEL_URI)

loads the registered model.

The important part is:

models:/fraud-detector@champion

The @champion portion means:

Load whichever model version currently has the champion alias.

Therefore, the script does not hard-code a model version such as:

fraud-detector version 1

Instead, it uses:

fraud-detector @ champion

This is useful in deployment workflows because the alias can later be moved to another model version without changing the prediction code.

5. What does TODO 3 do?

This:

inputs["prediction"] = predictor.predict(None, inputs)

runs the entire prediction pipeline.

Internally:

inputs
  ↓
predictor.predict()
  ↓
scale inputs
  ↓
inner_model.predict()
  ↓
predictions

Then:

inputs.to_csv(OUTPUT_CSV, index=False)

writes the results to:

/root/code/predictions.csv

The index=False is important because otherwise pandas would add its DataFrame index as an unnecessary CSV column.
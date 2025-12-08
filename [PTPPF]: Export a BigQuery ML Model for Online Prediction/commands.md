## 🌟 [PTPPF]: Export a BigQuery ML Model for Online Prediction – Full Command Guide 🌟

This guide provides the full sequence of commands to complete the lab: [PTPPF]: Export a BigQuery ML Model for Online Prediction.

---

### Command 1 — Create BigQuery Dataset

This command creates a new BigQuery dataset named `cymbal_bqml` in the US multi-region to store your data and model.

```bash
bq --location=US mk cymbal_bqml
```

---

### Command 2 — Load Data into BigQuery Table
This command loads the star.csv data file from a specified Google Cloud Storage bucket into a new BigQuery table, cymbal_bqml.cymbal_star_table. The --autodetect flag automatically determines the schema, and --skip_leading_rows=1 ignores the header row.
```bash
bq --location=US load \
  --autodetect \
  --skip_leading_rows=1 \
  cymbal_bqml.cymbal_star_table \
  gs://cloud-training/cepf/cepf042/star.csv
```

---

### Command 3 — Create Logistic Regression Model in BigQuery ML
This BigQuery ML (BQML) command creates and trains a Logistic Regression model named cymbal_bqml.cymbal_star_model. It uses the Type column as the label for prediction, and all columns from the cymbal_bqml.cymbal_star_table are used as features.
```bash
bq query --use_legacy_sql=false '
CREATE OR REPLACE MODEL `cymbal_bqml.cymbal_star_model`
OPTIONS(
  model_type = "logistic_reg",
  input_label_cols = ["Type"]
) AS
SELECT
  *
FROM
  `cymbal_bqml.cymbal_star_table`;
'
```

---

### Command 4 — Export Model to Cloud Storage
Once the BQML model is trained, this command exports the trained model artifacts in the TensorFlow SavedModel format (ML_TF_SAVED_MODEL) to a specified Cloud Storage bucket path (uri). This is the format required for deployment on Vertex AI.
```bash
bq query --use_legacy_sql=false '
EXPORT MODEL `cymbal_bqml.cymbal_star_model`
OPTIONS(
  uri="gs://qwiklabs-gcp-01-3f298c385730-star/models/",
  format="ML_TF_SAVED_MODEL"
);
'
```

---

### Command 5 — Upload Model to Vertex AI
This Vertex AI command uploads the exported model from Cloud Storage to the Vertex AI Model Registry. It specifies the display name, region, and the pre-built TensorFlow 2 prediction container image.
```bash
gcloud ai models upload \
  --region=us-central1 \
  --display-name="cymbal-star-imported-model" \
  --container-image-uri="us-docker.pkg.dev/vertex-ai/prediction/tf2-cpu.2-12:latest" \
  --artifact-uri="gs://qwiklabs-gcp-01-3f298c385730-star/models/"
```

---

### Command 6 — Create Vertex AI Endpoint
An Endpoint is a dedicated HTTP server where your model will be deployed to serve online predictions. This command creates the endpoint resource.
```bash
gcloud ai endpoints create \
  --region=us-central1 \
  --display-name="cymbal-star-endpoint"
```

---

### Command 7 — Set Endpoint ID Variable
After creating the endpoint (Command 6), you must capture its ID and set it as an environment variable for subsequent deployment. You will need to manually replace <your-endpoint-id> with the actual ID returned by Command 6.
```bash
ENDPOINT_ID=<your-endpoint-id>
```

---

### Command 8 — Retrieve Uploaded Model ID
This command retrieves the resource ID (MODEL_ID) of the model uploaded in Command 5 using a filter based on the display name, and stores it in an environment variable.
```bash
MODEL_ID=$(gcloud ai models list --region=us-central1 \
  --filter="displayName=cymbal-star-imported-model" \
  --format="value(name)")
```

---

### Command 9 — Deploy Model to Endpoint
This final command deploys the model (identified by $MODEL_ID) to the newly created endpoint (identified by $ENDPOINT_ID), specifying the machine type for hosting the model.
```bash
gcloud ai endpoints deploy-model $ENDPOINT_ID \
  --region=us-central1 \
  --model=$MODEL_ID \
  --display-name="cymbal-star-deployment" \
  --machine-type="n1-standard-2"
```

## 🌟 [PTPPF]: Tune Gemini Model by using Supervised Fine-tuning – Full Command Guide 🌟

This guide provides the full sequence of commands to complete the lab: [PTPPF]: Tune Gemini Model by using Supervised Fine-tuning.

---

### Command 1 — Create Cloud Storage Bucket

This command creates a new cloud storage bucket in the us-east1 region. **Change bucket name and region according to your lab.**

```bash
gcloud storage buckets create gs://qwiklabs-gcp-01-7d9d55f79a9f-model-dataset \
    --location=us-east1 \
    --public-access-prevention
```

---

### Command 2 — Copy ipynb file command from inside AI workbench. Replace and use the file path provided in your lab.
```bash
!!gsutil cp gs://qwiklabs-gcp-01-7d9d55f79a9f-lab-data/gemini-supervised-tuning-qa-challenge.ipynb ~/
```

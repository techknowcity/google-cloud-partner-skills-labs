## 🌟 [PTPPF]: Train Custom ML Models on Vertex AI – Full Command Guide 🌟

This guide provides the full sequence of commands to complete the lab: [PTPPF]: Train Custom ML Models on Vertex AI.

---

### Command 1 — Create BigQuery Dataset

This command creates a new cloud storage bucket in the us-central1 region. **Change bucket name and region according to your lab.**

```bash
gcloud storage buckets create gs://qwiklabs-gcp-your-bucket-cepf \
    --location=us-central1 \
    --public-access-prevention
```

---

### Command 2 — Copy ipynb file from within AI workbench. Copying needed to finish task.
```bash
!gsutil cp gs://cloud-training/cepf/cepf051/vertex-ai-core-challenge.ipynb ~/
```

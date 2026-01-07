## 🌟 [PTPPF]: Train Custom ML Models on Vertex AI – Full Command Guide 🌟

This guide provides the full sequence of commands to complete the lab: [PTPPF]: Train Custom ML Models on Vertex AI.

---

### Command 1 — Prompt for ChatGPT

This prompt is used to give cloud shell command by gpt. Paste this below lab instructions as shown in youtube video.

```bash
give me gcloud command to create json file and upload into the given bucket. the entries within the
json file should be of a jsonl format. use below format.

structData: { "id": "<your-id>", "structData": { <JSON object> }, "content": { "mimeType": "
<application/pdf or text/html>", "uri": "gs://<your-gcs-bucket>/directory/filename.pdf" } }

```

---

### Command 2 — Filter to add for curl command
```bash
filter:"category:ANY(\"information\")"
```

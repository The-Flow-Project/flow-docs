# Tutorial: Evaluating results

This tutorial covers the evaluation form, which compares recognized text against ground
truth and reports how accurate a model's output was.

## Before you start

You will need:

- The **webhook API key** for the evaluation workflow.
- A **`line`-mode HuggingFace dataset repository** that contains both the ground truth
  text and at least one `inference_*` column, prepared with
  [Preprocessing a line-based dataset](preprocessing_line_based.md) (with ground truth,
  "Allow empty lines" unchecked), then run through [Inference](inference.md) to add the
  `inference_*` predictions.
- A **HuggingFace token** with read access to that repository (and write access too, if
  you want the evaluation results uploaded back to it).
  [Create a token here](https://huggingface.co/settings/tokens) if you don't have one
  yet.
- The **email address** for status updates.

## Step by step

1. Open the evaluation form (the "Evaluation" card on the landing page) and enter your
   **webhook API key**.
2. Fill in the **dataset repository** to evaluate.
3. Fill in the **HuggingFace token**.
4. Under **Splits**, list which dataset splits to evaluate, for example `test` or
   `train,test`.
5. Enter your **email address** and submit the form.

## What happens after you submit

n8n calls the evaluation service to start the job, then checks on it every 30 seconds.
The service reads the dataset, compares recognized text against ground truth, and writes
back evaluation statistics. Once the job finishes, you receive an email containing:

- the job's **status** (success or failure),
- the **runtime** and the **number of items** processed,
- a **link to the results**, and
- the computed metrics, in particular the **character error rate (CER)** (the
  recognition accuracy measure this service reports), or an **error message** if the
  job failed.

## Full parameter reference

For the complete, authoritative parameter list, see the
[evaluation request model](https://github.com/The-Flow-Project/service-trocr-inference/blob/main/src/app/models/evaluation_models.py)
in the inference service's repository (this workflow calls the same service as
inference and write raw XML). The service also publishes interactive API docs at
`/docs` on its own address.

## Next steps

If the results are not good enough yet, you may want to revisit the data used for
[preprocessing](preprocessing_line_based.md) or try a different model in
[Inference](inference.md).

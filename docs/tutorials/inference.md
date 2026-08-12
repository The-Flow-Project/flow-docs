# Tutorial: Running inference

This tutorial explains how to run a trained TrOCR model over a dataset using the
inference form. The input should be a **line** dataset, not raw_xml, with "Allow empty
lines" checked so every line is included as a row for the model to fill in. Prepare one
with [Preprocessing a line-based dataset](preprocessing_line_based.md) first if you
haven't already.

## Before you start

You will need:

- The **webhook API key** for the inference workflow.
- The **source HuggingFace dataset repository** to run inference on.
- A **HuggingFace token** with access to it, and to wherever the output should go.
  [Create a token here](https://huggingface.co/settings/tokens) if you don't have one
  yet.
- A decision on **where the output should be saved**: a new repository, an existing
  repository, or written back into the source repository itself. See step 7 below.
- The **HuggingFace model identifier** of the TrOCR model to use; see "Choosing a
  model" below.
- The **email address** for status updates.

## Step by step

1. Open the inference form (the "Inference" card on the landing page) and enter your
   **webhook API key**.
2. Fill in the **source dataset repository** you want to run inference on.
3. Fill in the **HuggingFace token**.
4. Enter the **TrOCR model** to use, by its HuggingFace model identifier. The field is
   pre-filled with `microsoft/trocr-small-handwritten`, the inference service's own
   default; see "Choosing a model" below for other options.
5. Leave **Target image width/height** empty unless you specifically need to resize
   images before recognition; by default the inference service uses its own sensible
   default size.
6. Under **Splits**, list which dataset splits to process, for example `train` or
   `train,test`.
7. Under "Where should the inference results be saved", choose one of three
   destinations:
    - **Create a new repository** (recommended): enter a name that does not already
      exist. Private repository is checked by default; uncheck it if you want the
      result public.

        !!! tip "Public or private?"
            On a free HuggingFace account, the built-in dataset viewer only works for
            **public** repositories, and free accounts get much less storage for private
            repos than public ones. But public really does mean public: anyone can see
            and download it, including bots that scrape the Hub. Only leave this
            unchecked if you're actually allowed to publish the data openly.

    - **Write to another existing repository**: enter that repository's name, then
      choose whether to **update** it (keep its existing content and add the inference
      results) or **replace** it (overwrite its existing content entirely).
    - **Write back to the source repository**: an advanced option that modifies the
      source dataset itself, using the same update or replace choice as above. The form
      shows a warning once this is selected.

    If you choose replace, the form asks you to confirm before submitting, since it
    overwrites existing data.

8. Enter your **email address** and submit the form.

## Choosing a model

The TrOCR model field accepts any HuggingFace model identifier that follows the TrOCR
architecture. It doesn't have to be one of Microsoft's own checkpoints; any compatible
model on the Hub works, including one your team has fine-tuned and pushed there
yourselves. If you don't have your own model yet, Microsoft publishes a small family of
ready-to-use handwritten-text checkpoints, roughly trading size for accuracy:

- [microsoft/trocr-small-handwritten](https://huggingface.co/microsoft/trocr-small-handwritten)
  - the smallest and fastest, and the form's default.
- [microsoft/trocr-base-handwritten](https://huggingface.co/microsoft/trocr-base-handwritten)
- [microsoft/trocr-large-handwritten](https://huggingface.co/microsoft/trocr-large-handwritten)
  - the most accurate, but slowest and most resource-hungry.

You can also [browse all TrOCR-tagged models](https://huggingface.co/models?search=trocr)
on the Hub to compare community and fine-tuned options.

## What happens after you submit

n8n calls the inference service to start the job, then checks on its progress every 30
seconds. The service reads the dataset from HuggingFace and pushes its own output there
directly. n8n only tracks whether the job is still running, has failed, or has
finished. Once it is done, you receive an email with the outcome and, on success, a link
to the resulting dataset.

!!! note "Check the dataset card afterwards"
    The service automatically generates a dataset card (the repository's README) for
    the result, but it's fairly bare. Worth opening it on the Hub afterwards and filling
    in anything that matters (description, license, citation, and so on). The exact
    parameters used for this run are also saved as a JSON file among the repository's
    files, if you ever need to check exactly how a dataset was produced.

## Full parameter reference

This form covers the options most people need. For the complete, authoritative
parameter list, including options not exposed here such as running without pushing
to the Hub at all, see the
[inference request model](https://github.com/The-Flow-Project/service-trocr-inference/blob/main/src/app/models/inference_models.py)
in the inference service's repository. The service also publishes interactive API docs
at `/docs` on its own address.

## Next steps

The inference output is still a `line` dataset, separate from the original Page XML
layout. If you also have the matching `raw_xml` dataset from before (the one this
`line` dataset was preprocessed from), continue with
[Write raw XML](write_raw_xml.md) to merge the recognized text back into it. If your
`line` dataset already had ground truth, continue with [Evaluation](evaluation.md)
instead, to score the recognition quality.

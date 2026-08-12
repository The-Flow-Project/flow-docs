# Tutorial: Writing results back into raw XML

After [running inference](inference.md), you have a dataset with recognized text per
line, but it is separate from the original Page XML layout. This tutorial covers the
"Write raw XML" form, which merges the two back together: it takes the original raw XML
dataset and the inference output, and produces a new dataset where the Page XML files
contain the recognized text.

## Before you start

You will need:

- The **webhook API key** for this workflow.
- The **raw XML dataset repository**: the untouched `raw_xml` dataset your `line`
  dataset was originally preprocessed from (see
  [Preprocessing a ZIP into a raw XML dataset](preprocessing_zip_raw_xml.md)). This
  workflow uses it to know where each line belongs in the original Page XML.
- The **inference dataset repository**: the `line` dataset that came out of
  [Inference](inference.md), with its `inference_*` column of recognized text.
- A **HuggingFace token** with access to the raw XML repository and to wherever the
  merged result should go. [Create a token here](https://huggingface.co/settings/tokens)
  if you don't have one yet.
- A decision on **where the merged result should be saved**: a new repository, an
  existing repository, or written back into the raw XML repository itself. See step 4
  below.
- The **email address** for status updates.

## Step by step

1. Open the "Write raw XML" form (the matching card on the landing page) and enter your
   **webhook API key**.
2. Fill in the **raw XML dataset repository** and the **inference dataset repository**.
3. Fill in the **HuggingFace token**.
4. Under "Where should the merged dataset be saved", choose one of three destinations:
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
      choose whether to **update** it (keep its existing content and add the merged
      result) or **replace** it (overwrite its existing content entirely).
    - **Write back to the raw XML source repository**: an advanced option that modifies
      the raw XML dataset itself, using the same update or replace choice as above. The
      form shows a warning once this is selected.

    If you choose replace, the form asks you to confirm before submitting, since it
    overwrites existing data.

5. Enter your **email address** and submit the form.

## What happens after you submit

n8n calls the writing service, which merges the inference output into the original raw
PageXML and pushes the result to the output dataset on HuggingFace. n8n checks progress
every 30 seconds and emails you once the job succeeds or fails.

!!! note "Check the dataset card afterwards"
    The service automatically generates a dataset card (the repository's README) for
    the result, but it's fairly bare. Worth opening it on the Hub afterwards and filling
    in anything that matters (description, license, citation, and so on). The exact
    parameters used for this run are also saved as a JSON file among the repository's
    files, if you ever need to check exactly how a dataset was produced.

## Full parameter reference

For the complete, authoritative parameter list, see the
[raw XML merge request model](https://github.com/The-Flow-Project/service-trocr-inference/blob/main/src/app/models/raw_xml_upload_models.py)
in the inference service's repository (this workflow calls the same service as
inference and evaluation). The service also publishes interactive API docs at `/docs`
on its own address.

## Next steps

The result is a HuggingFace dataset of Page XML files with the recognized text filled
in, ready to be reviewed, corrected, or evaluated.

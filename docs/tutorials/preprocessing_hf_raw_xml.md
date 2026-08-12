# Tutorial: Preprocessing from a HuggingFace dataset

If your Page XML and images are already stored as a HuggingFace dataset, you can
preprocess directly from there instead of exporting a ZIP file first. This tutorial
covers that path, producing a **raw_xml** dataset, for publishing, backing up, or
preprocessing again later. It is not directly what you'll run inference on; see
"Export modes" below for how the pieces fit together.

## Before you start

You will need:

- The **webhook API key** for the preprocessing workflow, set up by your administrator.
- A **source HuggingFace dataset repository** that already contains an `xml_content`
  column and an `image` column: in other words, a dataset already in raw_xml format.
- A **HuggingFace token** with write access to the target repository (it can be the same
  token used to read the source, if it also has read access there).
  [Create a token here](https://huggingface.co/settings/tokens) if you don't have one
  yet.
- The **email address** that should be notified when the job finishes.

!!! note "About the HuggingFace token"
    The token is only used for this one job. It is not saved anywhere as a reusable
    credential. It does, however, stay visible to administrators in n8n's execution
    history afterwards, so a [token](https://huggingface.co/docs/hub/security-tokens)
    scoped to just this repository is safer than a broad, account-wide one.

## Step by step

1. Open the preprocessing form (the "Preprocessing" card on the environment's landing
   page) and enter your **webhook API key** under Authentication.
2. Under Data Source, choose the **HuggingFace** option.
3. Enter the **source repository** (for example `myorg/my-raw-xml-dataset`). It must
   already contain the `xml_content` and `image` columns mentioned above.
4. Under HuggingFace Target, fill in:
    - **Repository name**: where the result should be saved. It will be created
      automatically if it does not exist yet.
    - **HuggingFace token**: needs write access to that repository.
    - **Private repository**: check this if the dataset should not be public.

    !!! tip "Public or private?"
        On a free HuggingFace account, the built-in dataset viewer only works for
        **public** repositories, and free accounts get much less storage for private
        repos than public ones. But public really does mean public: anyone can see and
        download it, including bots that scrape the Hub. Only leave this unchecked if
        you're actually allowed to publish the data openly.

5. Under Processing Options, set **Export mode** to `raw_xml` (this is the default); see
   "Export modes" below for the other options. If you're adding to a dataset you already
   published from an earlier export, check **Append** to add these new records to it
   instead of overwriting it, handy when you process your data in multiple batches.
   There's no automatic duplicate detection though, so appending the same source twice
   will duplicate those pages in the output.
6. Enter your **email address for status updates** and submit the form.

## Export modes

The **Export mode** field controls the shape of the output dataset. A common source of
confusion: **inference does not run on a raw_xml dataset directly**: it runs on a
`line` dataset instead. See the
[full pipeline diagram](preprocessing_zip_raw_xml.md#export-modes) in the ZIP
preprocessing tutorial for how the pieces fit together end to end.

Under the hood, export modes are handled by the
[pagexml-hf](https://github.com/The-Flow-Project/pagexml-hf) library, which supports
five of them; the form currently exposes four:

| Mode | One row per | What each row contains |
| --- | --- | --- |
| `raw_xml` | page | the full Page XML plus the page image; nothing is cut apart yet |
| `text` | page | the page image plus all its text regions concatenated into one block |
| `region` | text region | a cropped region image plus that region's text |
| `line` | text line | a cropped line image plus that line's own transcription |

- **raw_xml** (this tutorial, and the form's default): keeps the full Page XML and page
  image together. Use it to **publish** your data on the HuggingFace Hub (public
  datasets can get a DOI there), to keep a **backup**, and as the base you'll come back
  to later for **further preprocessing** into `line` format. It's also the dataset
  [Write raw XML](write_raw_xml.md) needs to know the original structure when merging
  recognized text back in.
- **line**: one row per text line, with its own cropped image. This is what actually
  feeds:
  - **[Inference](inference.md)**, when the lines don't have a transcription yet (check
    "Allow empty lines" so every line is included as a row for the model to fill in).
  - **[Evaluation](evaluation.md)**, when the lines already have a ground-truth
    transcription and you then run inference on top of that, to compare the two.
  - **Training**, when the lines have a ground-truth transcription, typically with
    augmentation turned on.

  See [Preprocessing a line-based dataset](preprocessing_line_based.md) for the
  step-by-step for all three.
- **text** and **region** are available too, for page-level or region-level text rather
  than individual lines, for whoever wants a differently-shaped dataset outside this
  environment's own pipeline.

## Expert settings (optional)

The form has an "Expert settings" section for less common adjustments:

| Field | What it does |
| --- | --- |
| Creator | An optional name stored in the output dataset's metadata. |
| Linemask and baseline recognition | Loads an existing Page XML segmentation, orders the lines, and generates line masks. Leave unchecked unless you specifically need this. |
| Min line width / height | Drops lines smaller than the given size, in pixels. Only meaningful for `line`-mode data. |
| Augmentation loops | Creates this many extra, augmented copies of each line image, following the augmentation recommendations from the TrOCR paper. Only meaningful for `line`-mode data. Set to 0 to disable. |
| Train ratio / Seed / Shuffle | Control how the dataset is split into training and test sets. |

Most of these can be left at their defaults for a first run.

## What happens after you submit

The form sends this request as a separate, JSON-based request (rather than a file
upload), so there is no intermediate storage step. n8n calls the preprocessing service
directly with the source and target repository names. From there, the process is the
same as for ZIP-based preprocessing: n8n checks progress every 30 seconds and emails you
once the job succeeds or fails.

!!! note "Check the dataset card afterwards"
    The service automatically generates a dataset card (the repository's README) for
    the result, but it's fairly bare. Worth opening it on the Hub afterwards and filling
    in anything that matters (description, license, citation, and so on). The exact
    parameters used for this run are also saved as a JSON file among the repository's
    files, if you ever need to check exactly how a dataset was produced.

## Full parameter reference

This form covers the options most people need. The preprocessing service itself is a
separate, open-source project with its own documentation; see the
[API usage examples](https://github.com/The-Flow-Project/service-trocr-preprocess#api-usage)
and the
[full request model](https://github.com/The-Flow-Project/service-trocr-preprocess/blob/main/src/app/models.py)
in its repository if you want the complete, authoritative parameter list, including
anything not exposed in this form. If the service is running in development mode, it
also publishes interactive API docs at `/docs` on its own address.

## Next steps

Publish or back up this raw_xml dataset, and keep its name; you'll need it later for
[Write raw XML](write_raw_xml.md). To actually run recognition, preprocess the same
source again into a `line` dataset: see
[Preprocessing a line-based dataset](preprocessing_line_based.md), then
[Inference](inference.md).

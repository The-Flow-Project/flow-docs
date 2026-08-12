# Tutorial: Preprocessing a line-based dataset

The preprocessing form can also produce a dataset made of individual **line images**
with their transcriptions, rather than full Page XML documents. This is the **line**
export mode, and it is the format this environment actually uses for
[inference](inference.md), [evaluation](evaluation.md), and training. raw_xml is a
different, separate dataset used for publishing, backups, and further preprocessing.
See "Export modes" below for how the pieces fit together.

## Before you start

You will need:

- The **webhook API key** for the preprocessing workflow, set up by your administrator.
- Your data, either as a **ZIP file** (uploaded directly, or reachable at a public URL)
  or as a **HuggingFace source repository**: often a `raw_xml` dataset you preprocessed
  earlier, since `line` datasets are usually preprocessed from `raw_xml`, not from a ZIP
  directly.
- A **target HuggingFace repository** and a token with write access to it
  ([create one here](https://huggingface.co/settings/tokens) if needed).
- The **email address** that should be notified when the job finishes.
- A clear idea of **what this dataset is for** (inference, evaluation, or training),
  since that changes a couple of settings below.

!!! note "About the HuggingFace token"
    The token is only used for this one job. It is not saved anywhere as a reusable
    credential. It does, however, stay visible to administrators in n8n's execution
    history afterwards, so a [token](https://huggingface.co/docs/hub/security-tokens)
    scoped to just this repository is safer than a broad, account-wide one.

## Step by step

1. Open the preprocessing form (the "Preprocessing" card on the environment's landing
   page) and enter your **webhook API key** under Authentication.
2. Under Data Source, choose how you want to provide your data:
    - **ZIP URL**: paste a public link to your ZIP file.
    - **ZIP upload**: upload the ZIP directly (up to 500 MB).
    - **HuggingFace**: enter a source repository that already contains `xml_content`
      and `image` columns (for example, a `raw_xml` dataset from an earlier
      preprocessing run).

3. Under HuggingFace Target, fill in:
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

4. Under Processing Options, set **Export mode** to `line`. It is not preselected, so
   remember to change it yourself.
5. Set the checkboxes according to what this dataset is for:

    | Purpose | Allow empty lines | Augmentation loops (Expert settings) |
    | --- | --- | --- |
    | Inference input (no transcriptions exist yet) | **Checked**: every line must be included as a row for the model to fill in | 0 |
    | Evaluation input (you have ground truth, will run inference on top to compare) | Unchecked; you only need lines that already have a transcription | 0 |
    | Training data (you have ground truth) | Unchecked | Consider turning this on; see the warning below |

    In all three cases, consider leaving **Crop** checked: cropping each line image
    tightly to its line mask can noticeably improve both training and inference results,
    but only if the linemasks are actually good. A poor or misaligned segmentation can
    crop out real text or leave in excess background, which hurts more than it helps. If
    you're not confident in the source segmentation, check **Linemask and baseline
    recognition** (next step) to regenerate it first, or try both settings and compare.

6. If you're adding to a dataset you already have (for example, a previous batch of
   training data), check **Append** to add these new records to it instead of
   overwriting it. There's no automatic duplicate detection though, so appending the
   same source twice will duplicate those lines in the output, which can quietly skew
   a train/test split or inflate an evaluation.
7. If your source data does not yet have line-level segmentation, open **Expert
   settings** and check **Linemask and baseline recognition**. This loads the existing
   Page XML segmentation, orders the lines into reading order, and generates the line
   masks needed to cut out each line image.
8. Optionally set a **min line width/height** to drop very small, likely unusable line
   images, and configure the **train/test split** if you are preparing training data.
9. Enter your **email address for status updates** and submit the form.

## Export modes

The **Export mode** field controls the shape of the output dataset. A common source of
confusion: **inference does not run on a raw_xml dataset directly**: it runs on a
`line` dataset instead, which is why this tutorial exists as its own path. See the
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

- **line** (this tutorial): one row per text line, with its own cropped image. This is
  what actually feeds Inference, Evaluation, and training; see the table in "Step by
  step" above for how the settings differ between the three.
- **raw_xml**: keeps the full Page XML and page image together, for publishing,
  backups, and further preprocessing. See
  [Preprocessing a ZIP into a raw XML dataset](preprocessing_zip_raw_xml.md) or
  [Preprocessing from HuggingFace](preprocessing_hf_raw_xml.md). It's also the dataset
  [Write raw XML](write_raw_xml.md) needs to know the original structure when merging
  recognized text back in.
- **text** and **region** are available too, for page-level or region-level text rather
  than individual lines, for whoever wants a differently-shaped dataset outside this
  environment's own pipeline.

## Expert settings (optional)

The form has an "Expert settings" section for less common adjustments:

| Field | What it does |
| --- | --- |
| Creator | An optional name stored in the output dataset's metadata. |
| Linemask and baseline recognition | Loads an existing Page XML segmentation, orders the lines, and generates line masks. |
| Min line width / height | Drops lines smaller than the given size, in pixels. |
| Augmentation loops | Creates this many extra, augmented copies of each line image, following the augmentation recommendations from the TrOCR paper. Set to 0 to disable. |
| Train ratio / Seed / Shuffle | Control how the dataset is split into training and test sets. |

!!! warning "Augmentation is for training data only"
    Turning augmentation on can genuinely improve results for a dataset you're
    **training** on. It makes no sense for a dataset you're about to run **inference**
    or **evaluation** on: you'd end up recognizing or scoring duplicated, artificially
    distorted copies of the same lines instead of your real data. Leave it at 0 for
    those two cases, as shown in the table in "Step by step" above.

## What happens after you submit

The job runs the same way as the raw_xml path: n8n calls the preprocessing service,
checks on it every 30 seconds, and emails you when it is done, with a link to the
resulting dataset on success.

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

If you prepared this without ground truth, continue with [Inference](inference.md). If
it already has ground truth and you've since run inference on top of it, continue with
[Evaluation](evaluation.md). If you prepared it for training, use it with your training
process of choice; training itself happens outside this environment.

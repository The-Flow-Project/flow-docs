# Services

The Flow environment is built on a modular microservice architecture. Each service is a
FastAPI application covering one part of the HTR/ATR pipeline, orchestrated by the
[n8n environment](htr_workflow/n8n.md) described in this documentation. They are all in
*beta*-status.

## service-trocr-preprocess

[![GitHub](https://img.shields.io/badge/GitHub-The--Flow--Project%2Fservice--trocr--preprocess-181717?logo=github)](https://github.com/The-Flow-Project/service-trocr-preprocess){ target="_blank" rel="noopener" }
[![Docs](https://img.shields.io/badge/docs-Docs-blue?logo=readthedocs)](https://the-flow-project.github.io/service-trocr-preprocess/){ target="_blank" rel="noopener" }
[![API Reference](https://img.shields.io/badge/API-Reference-85EA2D?logo=swagger&logoColor=white)](https://the-flow-project.github.io/service-trocr-preprocess/api-reference.html){ target="_blank" rel="noopener" }

Prepares TrOCR training or inference material from PageXML and image exports. This is
the service behind the [preprocessing tutorials](tutorials/preprocessing_zip_raw_xml.md),
turning raw exports into `raw_xml` or `line` datasets on the HuggingFace Hub. Built with
FastAPI, Celery, and Redis; see
[Installation](getting_started/installation.md#running-the-preprocessing-and-inference-services)
for how to run it. Publishes interactive API docs at `/docs` when running in development
mode.

## service-trocr-inference

[![GitHub](https://img.shields.io/badge/GitHub-The--Flow--Project%2Fservice--trocr--inference-181717?logo=github)](https://github.com/The-Flow-Project/service-trocr-inference){ target="_blank" rel="noopener" }
[![Docs](https://img.shields.io/badge/docs-Docs-blue?logo=readthedocs)](https://the-flow-project.github.io/service-trocr-inference/){ target="_blank" rel="noopener" }
[![API Reference](https://img.shields.io/badge/API-Reference-85EA2D?logo=swagger&logoColor=white)](https://the-flow-project.github.io/service-trocr-inference/api-reference.html){ target="_blank" rel="noopener" }

Runs inference with a trained TrOCR model, evaluates its output against ground truth,
and merges recognized text back into the original PageXML. This is the service behind
the [inference](tutorials/inference.md), [evaluation](tutorials/evaluation.md), and
[write raw XML](tutorials/write_raw_xml.md) tutorials. Built with FastAPI, Celery, and
Redis; see
[Installation](getting_started/installation.md#running-the-preprocessing-and-inference-services)
for how to run it. Publishes interactive API docs at `/docs` when running in development
mode.

!!! note "Training happens outside this environment"
    Neither service trains models. To train a TrOCR model on a `line` dataset produced
    here, use the separate [TrOCR training scripts](packages.md#trocr-training-scripts-wip).
    See the [FAQ](help/faq.md#does-this-environment-train-models) for more.

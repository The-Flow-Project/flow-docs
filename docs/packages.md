#  Packages

Since The Flow environment is modular, there are multiple packages for seperated tasks. They are all in *beta*-status.  
Here is a list of those, and (if available) the links to the API references.

## pagexml_hf

[![GitHub](https://img.shields.io/badge/GitHub-The--Flow--Project%2Fpagexml--hf-181717?logo=github)](https://github.com/The-Flow-Project/pagexml-hf){ target="_blank" rel="noopener" }
[![Docs](https://img.shields.io/badge/docs-API%20Reference-blue?logo=readthedocs)](https://the-flow-project.github.io/pagexml-hf/){ target="_blank" rel="noopener" }  
A package for preprocessing PageXML and image exports (e.g. from Transkribus or eScriptorium). You can provide a ZIP export file or a Huggingface Hub repo containing the data to preprocess and upload to the Huggingface Hub.  
Usable in your Python scripts or via CLI.

<!-- termynal -->
```
> pagexml-hf --help

usage: pagexml-hf [-h] [--mode {raw_xml,text,region,line,window}] [--window-size WINDOW_SIZE] [--overlap OVERLAP] [--repo-id REPO_ID] [--token TOKEN] [--private] [--split-train SPLIT_TRAIN] [--split-seed SPLIT_SEED] [--split-shuffle] [--get-stats] [--local-only] [--output-dir OUTPUT_DIR] [--batchsize BATCHSIZE]
                  [--append] [--debug] [--mask-crop] [--min-width MIN_WIDTH] [--min-height MIN_HEIGHT] [--allow-empty] [--num-augmentation NUM_AUGMENTATION]
                  source_path

Convert Transkribus ZIP files or XML files from Folder to HuggingFace datasets

positional arguments:
  source_path           Path to the Transkribus ZIP file (local or URL), Huggingface Dataset, or folder containing XML files and images

options:
  -h, --help            show this help message and exit
  --mode {raw_xml,text,region,line,window}
                        Export mode (default: text)
  --window-size WINDOW_SIZE
                        Number of lines per window (only for window mode, default: 2)
  --overlap OVERLAP     Number of lines to overlap between windows (only for window mode, default: 0)
  --repo-id REPO_ID     HuggingFace repository ID to upload the dataset (e.g., username/dataset-name)
  --token TOKEN         HuggingFace token (or set HF_TOKEN environment variable)
  --private             Make the repository private
  --split-train SPLIT_TRAIN
                        Split ratio for train split.Between 0 and 1, default: None (means no split),e.g. 0.8 for 80% train, 20% test
  --split-seed SPLIT_SEED
                        Random seed for train/test split (default: 42)
  --split-shuffle       Shuffle the dataset before splitting (default: False)
  --get-stats           Show statistics after converting/upload (default: False)
  --local-only          Convert but don't upload to HuggingFace Hub
  --output-dir OUTPUT_DIR
                        Directory to save the dataset locally (when using --local-only)
  --batchsize BATCHSIZE
                        Number of files per batch (default: 32, make it higher for line/window mode).
  --append              Append to existing HuggingFace Hub dataset (only for uploads, not --local-only). If True, checks feature compatibility before processing. (default: False)
  --debug               Debug mode (default: False)
  --mask-crop           Crop the mask from the image (default: False)
  --min-width MIN_WIDTH
                        Minimum width of the cropped region/line (default: None, no minimum)
  --min-height MIN_HEIGHT
                        Minimum height of the cropped region/line (default: None, no minimum)
  --allow-empty         Allow empty regions or lines (default: False)
  --num-augmentation NUM_AUGMENTATION
                        Amount of random augmentations to apply on line images (default: None, no augmentations)
```

## flow-preprocessing

[![GitHub](https://img.shields.io/badge/GitHub-The--Flow--Project%2Fpackage--preprocessing-181717?logo=github)](https://github.com/The-Flow-Project/package-preprocessing){ target="_blank" rel="noopener" }
[![Docs](https://img.shields.io/badge/docs-API%20Reference-blue?logo=readthedocs)](https://the-flow-project.github.io/package-preprocessing/){ target="_blank" rel="noopener" }

A Python package for preprocessing PageXML datasets for OCR/HTR tasks with HuggingFace integration.

###  Features

- Process ZIP files (local or remote URLs)
- Process HuggingFace datasets
- Optional image segmentation with YOLO (GPU-accelerated)
- Multiple export modes (line, region, text, window, raw_xml)
- Train/test splitting
- Line filtering by dimensions
- Direct upload to HuggingFace Hub
- GPU support with optimal performance

## flow-segmenter

[![GitHub](https://img.shields.io/badge/GitHub-The--Flow--Project%2Fpackage--segmenter-181717?logo=github)](https://github.com/The-Flow-Project/package-segmenter){ target="_blank" rel="noopener" }
[![Docs](https://img.shields.io/badge/docs-API%20Reference-blue?logo=readthedocs)](https://the-flow-project.github.io/package-segmenter/){ target="_blank" rel="noopener" }  

A Python package for segmenting XML files through automatic text structure recognition using YOLO.

### Features

- **Automatic Segmentation** Automatically recognizes text structures
- **Multiple Backends** Supports YOLO and Kraken (for baseline and linemask detection)
- **Baseline Processing** Intelligent baseline extraction
- **XML Utilities** Comprehensive XML processing functions
- **Validation** Robust input validation with Pydantic

## flow-inference

[![GitHub](https://img.shields.io/badge/GitHub-The--Flow--Project%2Fpackage--trocr--inference-181717?logo=github)](https://github.com/The-Flow-Project/package-trocr-inference){ target="_blank" rel="noopener" }
[![Docs](https://img.shields.io/badge/docs-API%20Reference-blue?logo=readthedocs)](https://the-flow-project.github.io/package-trocr-inference/){ target="_blank" rel="noopener" }  

Package for performing inference and evaluation for OCR/HTR tasks with HuggingFace integration.

## TrOCR training scripts (WIP)

Scripts for training a handwritten text recognition TrOCR model on a line based
preprocessed dataset. Moving from the `flow-training` package into its own GitHub
repository; link coming soon.  

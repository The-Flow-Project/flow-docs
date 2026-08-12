# FAQ

### What is this environment?

A self-hosted [n8n](https://docs.n8n.io/) automation stack, plus a small set of web
forms, that lets an administrator or researcher run handwritten text recognition (HTR)
preprocessing, inference, and evaluation jobs without writing any code. It came out of
the Flow Project; see [flow-project.net](https://flow-project.net) for background on
the wider project. See [How n8n fits together](../htr_workflow/n8n.md) for the full
technical picture.

### Is it free and open source?

Yes, with one licensing nuance worth knowing. This repository, and the
[preprocessing](https://github.com/The-Flow-Project/service-trocr-preprocess) and
[inference/evaluation](https://github.com/The-Flow-Project/service-trocr-inference)
services it calls, are all released under the
[MIT license](https://github.com/The-Flow-Project/flow-docs/blob/main/LICENSE); genuinely
open source, free to use, modify, and redistribute. See
[License](../index.md#license) and [Citation](../index.md#citation) on the home page for
the details, and how to cite the project.

n8n itself is different: it's source-available under the
[Sustainable Use License](https://docs.n8n.io/sustainable-use-license/) rather than an
OSI-approved open source license. It's free to self-host and use internally, including
commercially, but you can't resell n8n itself as a competing hosted service. This rarely
matters for simply running this environment - it only limits offering n8n itself as a
SaaS product built on top of it.

### Is this environment still maintained?

The Flow Project, the funded research project that built this environment, concluded
in June 2026. There is no dedicated team or funding behind it anymore. Former
contributors still check in on issues and pull requests from time to time, on a
best-effort basis, but there's no guaranteed response time.

None of that affects whether you can use it: everything here remains free and
MIT-licensed (see above), so anyone is free to keep running it, fix things, or take its
development further. See [Contributing](contributing.md) if you'd like to help.

### What do I need to run it?

Docker and Docker Compose, at minimum. See
[Installation](../getting_started/installation.md) for the full requirements and setup
steps.

### Does this environment train models?

No. It runs [preprocessing](../tutorials/preprocessing_zip_raw_xml.md),
[inference](../tutorials/inference.md), and [evaluation](../tutorials/evaluation.md)
with an existing TrOCR model. Training happens outside this environment, with whatever
training process you prefer. This environment prepares the `line`-mode dataset a
training run would need.

### Can I use my own model?

Yes. The inference form accepts any HuggingFace model identifier that follows the TrOCR
architecture, including one your team has
fine-tuned yourselves. See
[Choosing a model](../tutorials/inference.md#choosing-a-model).

### Where does my data actually live?

Datasets pass through this environment but are stored on the HuggingFace Hub.
Garage, this environment's own storage, only holds uploaded ZIP files temporarily, for
up to seven days. See
[Data and backups](../htr_workflow/n8n.md#data-and-backups) for what n8n itself keeps.

### How do I report a bug or ask a question?

Open an issue on the relevant repository: this one for the n8n environment and
workflows, or
[service-trocr-preprocess](https://github.com/The-Flow-Project/service-trocr-preprocess/issues)
/
[service-trocr-inference](https://github.com/The-Flow-Project/service-trocr-inference/issues)
for the services themselves. If you're not sure which one, this repository is a safe
place to start.

### How does this compare to other HTR tools?

This environment doesn't do recognition itself - it's an orchestration layer that
dispatches jobs to the separate preprocessing and inference services, through n8n's
web forms, with no code required to operate it.

# Contributing

This repository welcomes contributions: new or improved workflows, Docker Compose
changes, form fixes, documentation updates, or simply reporting something that doesn't
work as expected.

## Ways to contribute

- **Report a bug or suggest a feature** by opening an issue on the relevant repository:
  this one for the n8n environment and workflows, or the
  [preprocessing](https://github.com/The-Flow-Project/service-trocr-preprocess/issues)
  or
  [inference/evaluation](https://github.com/The-Flow-Project/service-trocr-inference/issues)
  services for backend behavior.
- **Improve the documentation.** The [Getting started](../getting_started/installation.md)
  and [Tutorials](../tutorials/preprocessing_zip_raw_xml.md) pages live in this
  repository's `docs/` folder.
- **Add or change an n8n workflow**, a Docker Compose file, or a form.

## How to contribute

1. **Fork the repository** and create a branch for your change.
2. **Make the change**, keeping a few things in mind depending on what you're touching:
    - **n8n workflows** (`workflows-*/*.json`): export the workflow cleanly from n8n
      after testing it end to end, and keep the sticky note at the top of the workflow up
      to date if you changed what it does or added a new required credential.
    - **Docker Compose files and `.env` templates**: if you add or rename an environment
      variable, update the matching `.env.example` / `.env.local.example` file and its
      comment, so the templates stay a trustworthy reference.
    - **Forms** (`frontend-*`, `workflows-*/form`): test the form in a browser against a
      running n8n instance before submitting, and keep the style consistent with the
      other forms (`style.css`).

3. **Test the change against a real environment** where possible. Most changes here (a
   workflow, a compose file, a form) can't be verified by an automated test suite, so a
   manual run-through matters more than it would in a typical code repository.
4. **Open a pull request** against the main branch, describing what changed, why, and
   what you tested.

## Getting help

Not sure whether a change fits, or want feedback before doing the work? Open an issue
first to discuss it.

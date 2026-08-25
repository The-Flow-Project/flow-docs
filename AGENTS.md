# AGENTS.md

This is the documentation repository of the flow environment.  
  
This environement has the following features:  
  
- docker compose files in n8n-workflows/deploy
- frontend forms for different services the n8n instance manages: preprocessing, inference, and evaluation.
- n8n workflows for the different tasks to import in the n8n UI.
- FastAPI microservices to run the different taks, like preprocessing and inference, called by the n8n workflows.

It is the intention you can use those instances just by creating proper .env files and run the specific docker compose files.

The n8n-workflows repository has one stack, not two variants:

- n8n-workflows/deploy/docker-compose.yml starts everything with nothing published to the internet.
- n8n-workflows/deploy/docker-compose.public.yml is an overlay that publishes the forms under a domain. It adds traefik routing to the forms container and to nothing else.
- n8n-workflows/deploy/docker-compose.traefik.yml runs the proxy itself, once per host.
- n8n-workflows/deploy/docker-compose.mailserver.yml as an override, if you want to self-host a simple mailserver container with DKIM keys instead of relaying through an SMTP server you already have. The workflows need no change for this: their email nodes are plain SMTP nodes and ship enabled, so the self-hosted relay is just another SMTP credential.

The forms need no webhook address configured. The nginx that serves them also proxies /webhook/ to n8n, so a form posts to its own origin. That is why n8n needs no domain name, no DNS record and no proxy route, and why its editor UI is not reachable from outside.

The preprocessing and inference services can run on other machines. The workflows read their addresses from the n8n environment as $env.PREPROCESS_API_BASE and $env.INFERENCE_API_BASE instead of hardcoding a host.

The services, which are called by the n8n-workflows have to be started via other docker compose files.
You can find the code of those in this GitHub repositories:

- n8n Workflows and Docker Compose files: <https://github.com/The-Flow-Project/n8n-workflows>
- Preprocessing service: <https://github.com/The-Flow-Project/service-trocr-preprocess>
- Inference/Evaluation service: <https://github.com/The-Flow-Project/service-trocr-inference>

## Conventions

- All variables are set in .env
- Workflows are imported by the admin via n8n interface
- Workflow notification mail is sent by plain SMTP nodes that ship enabled. There are three options, in order of preference: an SMTP relay server (the default), the self-hosted mailserver override, or an external solution with its own n8n node such as Mailjet or Gmail.
- In the default variant, the admin only needs SMTP credentials for a relay server that is already allowed to send mail, and adds them in n8n.
- In the mailserver variant, the admin needs access to the domains DNS records. And the outgoing port 25 needs to be open.
- In the external variant (Mailjet, Gmail), the admin replaces both email nodes in every workflow with that provider's node and links its credential. Mailjet needs DNS access for domain verification, Gmail needs none.
- Running without the public overlay is used in those cases:
  - If the ports 80 and 443 are not open in your network
  - You don't have a domain to run it with (then use an SMTP account to send emails - the workflows already ship with enabled SMTP nodes for exactly this)
  - For development / testing
- Publishing needs exactly one DNS record, for the host the forms are served under (be aware of the update time of 48h). n8n and the storage get no record of their own.
- The admin changes the URL/IPs of the endpoints for the API calls in the .env file, not in the workflows.
- Which forms an instance offers is set with ENABLED_FORMS in the .env file.

## Documentation

- Describe the basic structure, the requirements to run tge flow environment, required software (docker), and the required accesses / accounts.
- Describe the different use cases and what to run when starting one of those.
- Give some explanation, what n8n does and about it's pros.
- Do not use dashes in text!

###  Documentation structure

- **Flow-Project:** Landing Page, index.md

- **Getting Started:**
  - Installation: How to install the flow environment, getting_started/installation.md
  - Overview Environment: Technical insight, htr_workflow/n8n.md

- **Tutorials:**
  - Preprocessing a ZIP into a raw XML dataset: tutorials/preprocessing_zip_raw_xml.md
  - Preprocessing from a HuggingFace dataset: tutorials/preprocessing_hf_raw_xml.md
  - Preprocessing a line-based dataset: tutorials/preprocessing_line_based.md
  - Running inference: tutorials/inference.md
  - Evaluating results: tutorials/evaluation.md
  - Writing results back into raw XML: tutorials/write_raw_xml.md

- **Reference:**
  - Packages: packages.md
  - Services: services.md

- **Help:**
  - FAQ: help/faq.md
  - Contributing: help/contributing.md

The documentation is generetad via mkdocs in this repository:
<https://github.com/The-Flow-Project/flow-docs>
